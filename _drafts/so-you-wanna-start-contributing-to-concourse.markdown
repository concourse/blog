---
layout: post
title: So you wanna start contributing to Concourse...
---

Getting involved in active open source projects can be fun and rewarding, but it's oftentimes daunting as a new contributor. This blog post will try to demystify some of the team's higher-order activities around how we organize our work and strive towards collaboration, in an effort to make the contributing experience more transparent and approachable.

This builds on our [Contributing Guide](https://github.com/concourse/concourse/blob/master/CONTRIBUTING.md), which contains technical details of setting up your development environment for working on Concourse, as well as existing documents in [our wiki](https://github.com/concourse/concourse/wiki) around our team's contributing and [merging](https://github.com/concourse/concourse/wiki/How-to-Process-PRs) processes.

As a general principle, we strongly value discussion, collaboration, transparency, and expectation-setting, so we prefer and encourage contributors to start a conversation _before sending a pull request_. Believe me when I say that we don't enjoy pushing back on fully-formed features in pull requests! We'd rather have earlier, more frequent feedback loops as a general practice for community-driven engineering work, because we believe that doing so sets us all up for kinder, more empathetic, and more successful interactions.

## I want to propose a new feature or request a bug fix!

The short answer is.. open an issue! But there are a few caveats.

1. Check if an issue has already been opened! If you're unsure about what vocabulary other folks may have used previously, come talk to us in Discord and someone should be able to tell you whether your idea has been discussed. If it's been discussed, consider adding your data points to an existing issue rather than opening a new one.
2. For feature requests, please review the [Anti-Patterns Page](https://github.com/concourse/concourse/wiki/Anti-Patterns) of our wiki. Open source projects accumulate technical debt easily, and Concourse has its own set of cognitive overhead for deploying and operating, so we've tried to articulate some things we'd like to avoid doing on that page, along with an explanation of how we reached that conclusion.
3. If your issue satisfies the uniqueness criteria in #1 and is not an anti-pattern as described in #2, then go ahead and open an issue in the appropriate repository, and please provide as many details as you can. Bug reports are much more useful if they contain specific steps to reproduce. For UI/UX bugs, screenshots and device data are super valuable. For operations-related problems like workers behaving strangely, screenshots of metrics dashboards and any logs that you can share (with sensitive data redacted, of course) will help us out a lot.

## I don't know where to start. Where can I find useful things to work on?

Because every open source project has different conventions, it's often tricky to separate the signal from the noise. The next few paragraphs will describe how the Concourse core team tracks work that we've validated to be useful to the project.

We use [GitHub issues]( __GHOST_URL__ /p/963a3d8d-2f05-4940-b35e-a97683c4ac07/github.com/concourse/concourse/issues) as a central place to discuss and organize upcoming work. However, the main Concourse repository has a high volume of newly opened issues each day, making it admittedly difficult to make sense of what the priorities are. In truth, the core team does not manage this firehose of issues from top to bottom; we try to label and triage issues when we can, so that they can be organized more properly into a [Project](http://project.concourse-ci.org/). (Issues that contain more details are more likely to be responded to and triaged.)

The Pivotal teams use our Project page to track high-level progress on a few active streams of work. Within our team, we're currently organized into four streams:

1. Core: Work related to Concourse's user-facing abstractions (resources, tasks, builds, teams, etc.) as well as scheduling, garbage collection, and most of what goes on within web nodes.
2. API: The web API, as well as the Fly CLI. The same group of engineers work on both the Core and API.
3. Runtime: All work related to orchestrating the containers that run steps, including the worker code. Issues related to worker lifecycle &nbsp;also tend to land in this track.
4. UI/UX: All things related to the Elm front-end.

You'll notice that at the top of each track of work, there's a subproject suffixed with "side-road" (example: the [UX side-road](http://project.concourse-ci.org/projects/MDc6UHJvamVjdDI5OTk4MDY=).) Side-road backlogs are generally good places to start hunting for impactful, validated, and relatively small things to work on. But if nothing in the side-roads strikes your fancy, check out the other subprojects: every issue that has been sorted into any of these backlogs has been prioritized, and can be considered "ready" to pick up. We'd recommend that you post a message in the issue thread before starting work, to avoid duplicating effort and ensure that everyone is on the same page about the intended outcome.

We also sporadically use the label _[good-first-issue](https://github.com/concourse/concourse/labels/good%20first%20issue)._ However, bear in mind that it's not always the core team adding the tags, so we can't definitively vouch that every issue with that tag is low-context or small in scope! ([Here's a more in-depth explanation of how we use issue tags](https://github.com/concourse/concourse/wiki/How-Issues-are-Managed).)

Besides the main repository, we have a number of other places that always need help:

- If you're familiar with BOSH, have a look at our [BOSH release](https://github.com/concourse/concourse-bosh-release) and [default deployment manifests](https://github.com/concourse/concourse-bosh-deployment). Likewise for Helm/Kubernetes, we have a new home for [our Helm chart]( __GHOST_URL__ /p/963a3d8d-2f05-4940-b35e-a97683c4ac07/github.com/concourse/helm).
- If you want to learn more about resources, there are a [number of resource types](https://github.com/concourse/concourse/wiki/Resource-Types), created both by the core team and the community, that need extending or refactoring (or even rewriting).
- We always have a number of in-flight, larger-scale design and architecture decisions that are recorded as Requests For Comment (RFCs) in the [RFCs repo](https://github.com/concourse/rfcs). If you'd like to get involved in future-leaning discussions about Concourse's roadmap, please jump in and leave comments in those proposals!
- Help us improve our [documentation](https://github.com/concourse/docs)!

## Where can I go for help on implementing something?

No matter where you find your first issue, the core team is always happy to provide support. We have a number of channels for communicating with collaborators:

1. [Discord](https://discordapp.com/channels/219899946617274369/413770960089382922): At least a handful of us will have Discord open during east coast working hours (9:00am-6:00pm EST). Real-time and synchronous chatter means you'll probably get the fastest response to any questions you have, whether from the maintainers or from community members.
2. [Discuss](https://discuss.concourse-ci.org/): For less urgent issues and/or more speculative discussion, the forums are a good place to sound out ideas. Feel free to use Discord or Discuss to ask for a recommendation for an issue to work on.
3. If a discussion is already going in GitHub issues or you've opened a draft PR, you can tag the relevant core team members directly! You can check the list of team members and what stream of work each person is on [here](https://github.com/orgs/concourse/teams/developers/teams). You can also use the group handle, ie. **@concourse/core-api**.

That's all for now! Hopefully this post was helpful – if you have feedback, leave us a comment on Discuss or Discord!

