---
layout: post
title: Navigating layers of Abstraction
---

 **Navigating layers of Abstraction**

When Users break your UI ...

Users are in love with your product and they are using your application so much they are pulling more and more of their workflow into your application. The tools has become mission critical to do their job. Success! Hold on, friction with the interface is starting to get in the users way and the simple build, test, deploy pipeline that worked just fine a year ago has morphed over time into a 100 job pipeline that is building business critical systems. You've hit the max limit of what is reasonably legible and click-able. You've tried groups, splitting up your pipelines but, that still doesn't help.

Users breaking the UI in reality is a great problem to have as a product; it shows us that there are some big, missing features around sorting and searching across Concourse. Good thing all of our users are not experiencing this, but it certainly is on the horizon of problems for us to deal with as popularity of the product grows so, we should start thinking about how we can start slicing up the incremental improvements to start improving usability around these issues.

Let's look at the fundamentals of the interface visualization holistically so we can understand where we are starting from.

<figure class="kg-card kg-image-card"><img src="https://lh3.googleusercontent.com/xkiubWeWPAjHrYqq0jmkulKQOxVCRCwQ7Nubh_rYmkyskMIGl9zQuelRZSFCLMvrrVVnXK-GfcjPfEYXRaTyofmSubtKIiHwGvoaXxM6JtYqLUglnK7reKgBjnU3id2iyXxMCTo" class="kg-image" alt loading="lazy"></figure>

The web UI of Concourse uses a unique data visualization to show the flow of versioned resources through connected jobs which are represented as boxes and lines when combined together compose a pipeline abstraction.

This abstraction is simple enough model that it allows for reuse in other views of the app by redacting and adding attributes to create new levels of resolution depending on the data being surfaced.

The High-Density CI Monitor view is important for users looking at their entire Concourse instance for global status information of 50+ pipelines. This view displays the highest level of the pipeline abstraction and is surfacing only three attributes: running state of the pipeline, resource error, and overall pipeline state.

<figure class="kg-card kg-image-card"><img src="https://lh4.googleusercontent.com/iqnHW4z6K3g5fmCAv2OCGOwHrlU7szH_Od27Ah8GPerUA-ZIOhhXDYXG6dK6ulNrqCGpfyh2TNBl_-nKOq6kWeIoUpF_4g0xMBRQiahLEx6Kcz95WFOmvXh7_9e3xZkENBfN9nY" class="kg-image" alt loading="lazy"></figure>

For team pipeline monitoring or users with \>50 pipelines the Dashboard CI Monitor view adds another layer of attributes to the abstraction. Running state of a job, resource error, status of all job’s, time since the pipeline was last in a failing or succeeding state.

<figure class="kg-card kg-image-card"><img src="https://lh4.googleusercontent.com/xQBVH6X9yymaC1NkM7s9bjH6Y5-Ckupx3RZ4e4ZsWvY3fGq9r1WuSl_8xrrpbN4nW9I9Bxe7u0k2uEKT7vbFxXGTLBiFIKbISbM0Iznb9AiX7ez_bvjhqQp5gRIcOWJBQjn_IAw" class="kg-image" alt loading="lazy"></figure>

The Pipeline view is the base level of the abstraction. It displays all of the attributes; Resources, Resource State, Jobs, Job State, what is Job running, relationships between Jobs and Resources, what resources Trigger and which are non-trigger, the number of inputs to a job, the number of outputs to a job, groups, and the overall pipeline state.

### **So, how do we go about the task of designing a better user experience of Concourse?**

Because we are always getting feedback from a firehose of channels; the Open Source Community from Pivotal CF R+D and Field teams, we have captured some use cases that push the boundaries of the UI to the extreme and truly beyond the scale of we expected when designing these views.

The[RabbitMQ](https://www.rabbitmq.com/) team at Pivotal has been a great data point over time as their use of Concourse has grown more and more complex. Their continuous deployments are expanding as they automate more and more of their processes as shown in their pipeline below.

<figure class="kg-card kg-image-card"><img src="https://lh5.googleusercontent.com/gcHRaD4cMD0EJUJN0PVPASyXtFkTLGr-y0UQuJEPl9cg3ZuJNMbhl74hZ9kSpiXR9Udp6-rqz5lN2QWByGHNFK17KDBMthX3ETA7kiqoK0S79LTydl4VNgkSeYAwx3Fek0eIJu8" class="kg-image" alt loading="lazy"></figure>

Navigating in a linear way through Concourse is very hard for them because the clickable areas, as rendered in the base pipeline view, require extra effort to reach by zooming in and out to get to a click target, turning on and off groups to reduce the jobs displayed, and possibly using ‘command f’ to highlight and then zero in on your resource or job. This is pretty painful.

<figure class="kg-card kg-image-card"><img src="https://lh6.googleusercontent.com/Dg0c0aFaNTgQHbVeAr3gqu3KGUQmbRAV7tBJ7kFaRD5q-BPHQQ1a1HhiZqcZxmxcjbdF89G3GisijJOTHlj64fQ_iUXSOLO7riYPEyS640tkH3gG4zw-ZILFY0-XkTaEeS_bqxU" class="kg-image" alt loading="lazy"></figure>
### **So, how do we go about the task of designing a better user experience of Concourse for teams like RabbitMQ?**

There are three themes of user pains around navigation, searching and filtering views that we have identified as features we want to optimize where we can start making that incremental, iterative change.

1. **Moving between pipelines across teams.** Traversing across pipelines when you have many pipelines or teams on an instance can be an arduous workflow because you must return to the Dashboard to scan or search for the pipeline you want to get to.
2. **Navigation legibility**. When viewing large unwieldy pipelines (like RabbitMQ) views users currently use the ‘Find’ feature on the browser to be able to navigate to their desired resource in the pipeline UI to then be able to see it clearly enough to be able to click through to its resource page or to find a job to get to the desired job page.
3. **One click too many.** Users taking too long to get to where they need to be by clicking through the web UI. Users would like to ‘Jump to’ another location in the app like a different pipeline, job or resource they already have in mind.

### **How do we get there?**

There are going to be three Epic features; sidebar resurrection, filtering, and Omni search, that will slice up the improvements over many issues and releases to de-risk the features while we get feedback which will allow us to iterate as we build out these solutions app-wide. Let's dig into the proposed solutions.

## **Filtering views**

In the web UI, we use filters to reduce large data sets into consumable and actionable views for Concourse Operators.

When the dashboard was first in beta, in Concourse version 3.5.0 and 3.6.0, filtering on the dashboard page was developed to help users navigate and filter down this view to see what was most important to them and their team. Users now would like to use sophisticated queries for filtering and have this filtering expanded beyond the dashboard. The experiment we plan to run is to break the current ‘Search’ input on the dashboard and converting it into a ‘Filter’ using the same commands of pipeline, status or team.

<figure class="kg-card kg-image-card"><img src="https://lh6.googleusercontent.com/ugpv7gVQ4-FRpeVq4KyPCOnVEVPxBOtU0-MRseBExefH19yopyyOqAibg5_ezVm7FQTUmDU3umimyDpwSLxzbID9PL8i5jpCJ2rx9atT42kqYeCvB3B0puvO2NMMJUQ1LuKvZDE" class="kg-image" alt loading="lazy"></figure>

Then carrying this feature over to the pipeline view and expanding its capabilities to query using more than one facet. In the web UI this would mean adding a level 2 menu below the top navigation bar, where the groups currently live, to show the user what they have selected to filter the current view.

Filtering will be covered by stories like[#3573](https://github.com/concourse/concourse/issues/3573) and[#3792](https://github.com/concourse/concourse/issues/3792) .

## **Expanding Search**

_Users love search for two reasons:_

- _Search lets users **control their own destiny** and assert independence from websites' attempt to direct how they use the web. Testing situations routinely validate this. A typical comment is: "I don't want to have to navigate this site the way they want me to. I just want to find the thing I'm looking for." This is why many users go straight to the **homepage** search function._
- _Search is also users' **escape hatch when they are stuck** in navigation. When they can't find a reasonable place to go next, they often turn to the site's search function. This is why you should make search **available from every page** on the site; you cannot predict where users will be when they decide they are lost._

_Search is a big deal: the_ [_usability of the search on intranets_](https://www.nngroup.com/articles/intranet-usability-the-trillion-dollar-question/) _we have tested accounted for 43% of the difference in employee productivity between intranets with high and low usability._

Search will now be a filter and a new search will use powerful syntax. While it may seem counterintuitive, when we planned out the filter/search on the Dashboard view we knew that because this was a new view we could get away with adding this searching feature, get validation that users needed this and we could expand and iterate on it from there.

Features of this Omni search would include adding / into the breadcrumb on every page that would invoke an app-wide omni search to allow for a quick switch into another team/ pipeline/ resource/ job.

More about this feature here:[https://github.com/concourse/concourse/issues/3630](https://github.com/concourse/concourse/issues/3630)

<figure class="kg-card kg-image-card"><img src="https://lh4.googleusercontent.com/DopptZL1ajs5NmfuiFDF9CjzKxtZI0LBzcEANm256DHhoxnry0lGgIGBjl4yL2CVEiASOiBdgFRpg6hfERNKsmZAvQ31P1tfXPGP6p-GI05NMKccGB0Lfk9nWAvxUbHWo23Ejmc" class="kg-image" alt loading="lazy"></figure>
## **Bringing back the Sidebar!!!! 😱**

So…. this is already in 5.2.0. We listened to the pains of our users and prioritized this new and improved sidebar because they were complaining quite loudly. Never has one feature brought about so much praise when it was released in 5.0.

<figure class="kg-card kg-image-card"><img src="https://lh4.googleusercontent.com/_z2hREfuei2vSS0EBhItafL5pTfiI0ahRIRPVzaX0xi9RE-rL9NtZQyV9d3Q-CPueorg40El2n_UClIO6aViG5pVJYmWKzSTYxSJWyGMceCPTwVoTQskmEl5Lr27LCceYGbUcus" class="kg-image" alt loading="lazy"></figure>

## **Feedback!**

We want to hear your thoughts on this. What impact would these features have on your current workflows?

### **Head over to the Github issues for feedback:**

[https://github.com/concourse/concourse/issues/754](https://github.com/concourse/concourse/issues/754)

[https://github.com/concourse/concourse/issues/1682](https://github.com/concourse/concourse/issues/1682)

[https://github.com/concourse/concourse/issues/1776](https://github.com/concourse/concourse/issues/1776)

