---
layout: post
title: A sharp edge of Elm's lazy evaluation, and its impact on the Concourse UI's
  performance
---

We were trying to add tooltips to the sidebar. We were happy when we discovered all the functions in the `Browser.Dom` module in Elm 0.19 - although we found that, because of the 'stateless/reactive' nature of the Elm architecture, we had to do a bit of a complicated flow to decide when to show tooltips:

1. user hovers sidebar item: get the viewport of the item
2. when you receive the viewport, if it's overflowing, get the item's position
3. when you receive the position, add the tooltip (positioned appropriately)

Under the hood, there will be three calls to the update function. &nbsp;On most pages this totally worked and it was quite snappy! However, almost by accident, we loaded up the build page for a build with a huge amount of log output (and therefore thousands of DOM nodes). Then we noticed the sidebar's behaviour became extremely sluggish. When we investigated using the profiler in Chrome, we realized that the entire build output (including the aforementioned thousands of DOM nodes) was being re-rendered every time we hovered or unhovered anything in the sidebar.

In some sense this should not have surprised us - Elm is generally eagerly evaluated and so you should expect the entire view to be re-rendered every time the `update` function is called (in other words, on every state change).

This isn't entirely true, though. As is pretty well-known, Elm makes use of a "virtual DOM" - this means that when the `view` function is called, it simply generates a lightweight in-memory tree structure which generally corresponds to the DOM but doesn't incur all the costs of the browser. Then Elm computes the difference between the virtual DOM produced and whatever virtual DOM was used the last time the `view` function was called. In this way it can figure out a minimal set of modification operations that need to be performed on the actual DOM to make it display the desired view. This is actually the reason for a lot of the "blazing fast" hype around Elm, and it really does make the rendering much faster.

However, if your virtual DOM is huge (a tree with tens of thousands of nodes) you can still wind up with the diffing operation described above becoming expensive and slow. This obviously doesn't scale very well to pages with a large DOM, and the Elm authors have provided a means of addressing this - the `Html.Lazy` module allows you to use an even simpler in-memory structure than the virtual DOM. In places where one of the `lazyN` functions are called, the virtual DOM just stores references to the arguments of the function. When diffing the virtual DOM tree, when it finds a lazy node, it just compares the new and old arguments -- if they match, it knows the whole tree that the function will generate will be the same, so it can move on to diffing other parts of the structure.

So we slapped an `Html.Lazy.lazy2` around the function call that produced all the build output. And it was still crazy slow. We thought about it for a second and we saw that one of the arguments being passed to `viewBuildOutput` actually would be different when we hovered something in the sidebar - this was the first argument, `session`.

Over the past few months, we've found it really profitable to converge to a kind of 'flat model' architecture to avoid data duplication, and unifying a lot of types - sacrificing some type safety in the interest of code that was easier to read, and especially easier to test. So we wound up with a global `session` value that persists across page loads, which contains stuff like your authentication data and whatever node you currently have hovered - since there can only be zero or one things hovered at any time, regardless of what page you are on.

We have found that following our 'clean code' instincts has wound up with many functions accepting the session as an argument. `viewBuildOutput` is no exception - there are some things in the build output that can be hovered, and the DOM should update appropriately in those cases. However, this means that there are times when that `session` argument will change, but have no impact on the virtual DOM rendered by `viewBuildOutput`. We know that as humans, but how do we prove it to the Elm runtime? It doesn't know which differences in the arguments matter, it just knows that you're depending on some parameter and its changed! The only safe course of action then is to call the function again and diff the giant virtual DOM that results.

OK, so let's pass something a little bit narrower than `session`. Looking closely at the usages of session in `viewBuildOutput` and the tree of functions called by it, we realized that really only `session.hovered` and `session.timeZone` (used to display the timestamps on the left-hand side of the log lines) were actually used.

OK, so what if we just pass those, instead of the whole session? That means the `viewBuildOutput` function will have three parameters instead of two. Well, kinda better, but the problem is that the `.hovered` value still changes when you hover stuff on the sidebar, causing unnecessary diffing. OK, so what if we are extra careful about the values that we pass into `viewBuildOutput` -- we built a function called `projectOntoBuildPage` which basically leaves values of interest to the build page alone, but turns all the values from the other pages to a default value. It is a projection (idempotent map) in the mathematical sense.

So after all this, we went from

    Html.Lazy.lazy2 viewBuildOutput session currentBuild.output

to

    Html.Lazy.lazy3
        viewBuildOutput
        session.timeZone
        (projectOntoBuildPage session.hovered)
        currentBuild.output

At this point we could really convince ourselves that hovering an item in the sidebar should not affect any of the values being passed into this function. &nbsp;We could even verify by adding `Debug.log`s around each of the values, and seeing that the same values were always being logged.

But it was still slow. And we could verify (again through `Debug.log`s) that `viewBuildOutput` was still being called every time we moved our mouse over items in the sidebar.

We stared at the problem for a while, started hopelessly Googling things and then found this article on the Elm language forum: [https://discourse.elm-lang.org/t/how-does-elm-lazy-work/3617/7](https://discourse.elm-lang.org/t/how-does-elm-lazy-work/3617/7). We stared at the problem a little longer and realized that `projectOntoBuildPage` will always return things that are equal _by value_, but will always produce values that are different _by reference_. OK, so we make a concession in the interest of performance, and actually do some of our own caching by storing the projected value in the model.

But it was _still_ slow.

We started to conduct an investigation of this whole 'reference equality' thing in the abstract. Reading the docs we can see that `lazyN` definitely treats primitive values (strings, characters, booleans and numbers) as equal, so we just need to investigate how it handles records and custom types.

To test, we used [this](https://github.com/pivotal-jamie-klassen/lazy.git) Javascript project. We create a basic Elm app with a single input port. Then we build it and drive the test with the puppeteer script at `index.js`.

If you run it yourself, you can reproduce the findings. The key part is that record-update syntax always produces a value that is not equal by reference. &nbsp;That is to say

    record = { field = "" }

is different, by reference, from

    { record | field = "" }

even though they are equal by value, and look pretty much the same! Furthermore, if a record like that were to be deeply nested in a structure of records and custom types, _changing that deeply-nested field would change the value-by-reference of all of that record's ancestors_. You can see this demonstrated in our experiment, which gives the following output:

    init: ""
    Custom.view: { field = "" }
    Custom: "deeply nested"
    Custom.view: { field = "deeply nested" }
    Canary: "canary"
    Custom.view: { field = "deeply nested" }

So what's really going on here? The model consists of two pieces of data, called 'custom' and 'canary'. The test script sends a message into Elm that causes the deeply-nested record field to change, and therefore the the lazy view needs to actually be evaluated. But the second 'canary' message should only change the outer `Html.text model.canaryString` part below, and the `Html.Lazy.lazy` call should notice that it's being called with identical arguments, but based on the log output above we can see that `Cusom.view` is getting called again with the same value!

    view : Model -> Html Msg
    view model =
        Html.div
            []
            [ Html.text model.canaryString
            , Html.Lazy.lazy Custom.view model.custom
            ]

the culprit in this behaviour is the above-mentioned reference-equality-breaking behaviour in `DeeplyNestedRecord.elm`:

    update : Msg -> DeeplyNestedRecord -> DeeplyNestedRecord
    update msg deeplyNestedRecord =
        { deeplyNestedRecord
            | field =
                case msg of
                    Canary _ ->
                        deeplyNestedRecord.field
    
                    Custom newField ->
                        newField
        }

In the Concourse code base, it turns out that when you are viewing the build page and you hover anything anywhere in the app, there is a deeply-nested record that will always be modified with that same pesky record-update syntax (excerpt from `web/elm/src/Build/Build.elm`:

    update : { a | hovered : HoverState.HoverState } -> Message -> ET Model
    update session msg ( model, effects ) =
        case msg of
            Hover _ ->
                let
                    newModel =
                        { model | hoveredCounter = 0 }
                in
                updateOutput
                    (Build.Output.Output.handleStepTreeMsg <| StepTree.updateTooltip session newModel)
                    ( newModel, effects )

So if we can avoid having reference equality break here, we can fix all our (surprisingly-widespread) expensive DOM-diffing woes.

In general, the Elm language authors preferred to have an eagerly-evaluated language to make it easier to reason about the runtime characteristics of a system. To understand this, note that performance problems in Haskell are notoriously difficult to diagnose in the rare cases when they occur because Haskell is lazily-evaluated.

So when the Elm authors added an option for lazy evaluation, they knew it was only for performance-intensive use cases and they wisely observed that in those cases you will want an equality check that is even cheaper than virtual DOM diffing, so they went even cheaper than the value equality that is the only kind of 'real' equality in a pure functional language. They went for reference equality, but this then means that a lot of other abstractions in your system become very leaky. This 'pass-by-reference' behaviour of records and custom types is encapsulated 99% of the time when working on Elm, and it is really hard to write any meaningful assertions about it (certainly not assertions that can be written in Elm itself, since the runtime has no means of talking about side effects by design).

The real kicker is that record-update syntax breaks reference equality by default. I might hope that in future versions of Elm this stops happening, so that we can trust our tests and types to give us (pretty much) the whole story about how our program will behave at runtime.

