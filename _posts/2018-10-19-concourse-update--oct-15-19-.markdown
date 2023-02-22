---
layout: post
title: Concourse Update (Oct 15–19)
date: '2018-10-19 00:00:00'
tags:
- product-update
---

<figure class="kg-card kg-image-card kg-card-hascaption"><img src="assets/images/downloaded_images/Concourse-Update--Oct-15-19-/1-Y05yilBhjLQKwCftw39ZVw.jpeg" class="kg-image" alt loading="lazy"><figcaption>Torontonians typically overreact when they get their first snowfall of the year. Its just a bit of frost ya’ll</figcaption></figure>

We finished our first implementation of Role Based Access Control (RBAC) this week! You can look forward to this change in our next release of Concourse.

Speaking of which, the next release of Concourse is currently blocked while we try to re-build our new [release pipelines](https://ci.concourse-ci.org/teams/main/pipelines/concourse). Along with our move to the mono-repo, we’re focusing even more on making the binary distribution of Concourse the first-class distribution of Concourse. This means that you’ll get everything you need for Concourse packaged into one nifty tgz&nbsp;! We’re still working on finalizing the pipelines, so look forward to hearing more details about these changes in the coming weeks.

This week, I’ve also been doing some analysis on our internal Concourse instance Wings. Wings currently runs on GCP and has

- 4 web instances
- 31 workers @ 4 vCPUs, 16 GB memory, 1000 GB SSD
- Google CloudSQL as the db
- 99 internal teams

Since inception last year, we’ve processed **238957900.6 build seconds, or 7 years of build activities** for Pivotal. Our peak month was in July, 2018, where we processed **48978695.88 build seconds, or 1.5 build years.**

Neat.

On to the update:

**API**

<figure class="kg-card kg-image-card"><img src="assets/images/downloaded_images/Concourse-Update--Oct-15-19-/1-I0qcGZPL9DOugmQ6eC_xVQ.png" class="kg-image" alt loading="lazy"></figure>
- We finished RBAC!
- Fixed an issue where Users who are not assigned to teams aren’t able to login [#2670](https://github.com/concourse/concourse/issues/2670)

**UX**

- Working on finalizing the fix to [#2414](https://github.com/concourse/concourse/issues/2414), which we thought was implemented but found that it didn’t work on Linux and Windows machines
- Continuing our UI cleanup work with [#2434](https://github.com/concourse/concourse/issues/2434), [#2430](https://github.com/concourse/concourse/issues/2670), [#2435](https://github.com/concourse/concourse/issues/2435)
- Picked up the corresponding UI story for pinning resources in the Web UI [#2508](https://github.com/concourse/concourse/issues/2508)

**Core**

- SPACE ([#1202](https://github.com/concourse/concourse/issues/1202) and [#2651](https://github.com/concourse/concourse/issues/2651))

**Runtime**

- Picked up some work on improving volume streaming [#2676](https://github.com/concourse/concourse/issues/2676)

**Operations**

- Working on emitting more metrics for locks held in DB [#2674](https://github.com/concourse/concourse/issues/2674)
