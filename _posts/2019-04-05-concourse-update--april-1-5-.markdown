---
layout: post
title: Concourse Update (April 1–5)
date: '2019-04-05 00:00:00'
tags:
- product-update
---

<figure class="kg-card kg-image-card kg-card-hascaption"><img src="assets/images/downloaded_images/Concourse-Update--April-1-5-/1-nZQ4xbWXguDpNQzLEkcJcA.jpeg" class="kg-image" alt loading="lazy"><figcaption>Concourse in action at the CF Summit 2019 Grape Up booth</figcaption></figure>

Greetings from sunny Philadelphia! The team was there for the [Cloud Foundry 2019 NA Summit](https://www.cloudfoundry.org/event/nasummit2019/) for a few days; talking to Concourse users and attending talks. Recorded videos of the talks should be uploaded soon; so I’ll point you to the interesting Concourse-related ones next week.

On to the update.

## For Active Discussion

- Please take some time to review and comment on the latest [Concourse + k8s Runtime RFC](https://github.com/topherbullock/rfcs/blob/e4a80f902bc835b2d528a7550b427bfa83a5660d/008-k8s-runtime/proposal.md)
- Regarding the runtime, there’s been an active conversation around better build scheduling and load distribution. You can catch up on the thread [here](https://github.com/concourse/concourse/issues/2928). We’d love for you to tell us about your own experience in our meta-issue [#3695](https://github.com/concourse/concourse/issues/3695)

## Coming Soon: Concourse 5.1.0
<figure class="kg-card kg-image-card kg-card-hascaption"><img src="assets/images/downloaded_images/Concourse-Update--April-1-5-/1-dEssJTEo9_VnNszNDUj6gQ.png" class="kg-image" alt loading="lazy"><figcaption>Icons on resources</figcaption></figure>

We’re in the process of polishing up some items that we weren’t quite able to zfit into the 5.0.0 release. There’s been also some interesting new features and new PRs that you can look forward to in 5.1.0 as well:

- Resource icons [#3581](https://github.com/concourse/concourse/pull/3581) thanks to efforts of contributor [mockersf](https://github.com/mockersf)
- You can now pause the pipeline from the pipeline view [#3475](https://github.com/concourse/concourse/pull/3475) thanks to the efforts of contributor [robwhitby](https://github.com/robwhitby)
- There’s been a great de-coupling of the API from the runtime & scheduler [#3307](https://github.com/concourse/concourse/pull/3307). This is a refactor and cleanup that brings us closer to an API that we’d be happy to publish and support.
- Introduced an on\_error option to allow outside sources to be notified of CI failure [#3652](https://github.com/concourse/concourse/pull/3652) thanks to contributor [amanw](https://github.com/amanw)
