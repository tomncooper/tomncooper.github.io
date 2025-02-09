+++
title = "Flink Stale PR Cleanup"
date = "2024-12-17"
description = "Apache Flink is a huge project and currently has over 1200 open PRs. I sent this message to the dev mailing list going over previous attempts to solve this issue and detailing a proposal to address the problem."
+++

I [sent](https://lists.apache.org/thread/6yoclzmvymxors8vlpt4nn9r7t3stcsz) the following email to the Flink dev mailing list.

## TL;DR

- We have over 1.2k open PRs, this is an issue as it makes new contributors think twice about committing and looks like a problem that is too-big-to-solve for committers.
- There have been various attempts, over the last 6 years, to enable the Stale PR bot/action to prompt authors to refresh old PRs and auto-close them if no action is taken.
- These were rejected as some committers felt this was punishing contributors for the committers not reviewing/closing PRs fast enough.
- Others felt that, rather than "sweeping the problem under the rug", using the Stale PR functionality would actually reveal the true scale of the issue. Allowing committers to see what were truly active PRs.
- Other Apache projects such as Kafka, Beam, Spark, Airflow and many others have enabled the stale PR GitHub action.
- Despite this, Kafka still has 1k open PRs. However, these PRs have all been updated/commented on in the last 3-4 months, so can be considered active.
- For Flink, only 12% of the open PRs have been updated in the last 3 months and only 41% in the last year.
- I propose we enable the Stale PR Github action to clear the backlog and reduce the PRs down to those that are active and relevant.
- We can start with PRs that haven't been active in the last year and give authors 3 months to refresh them. These thresholds could then be reduced over time, towards the norm for other Apache projects, of 3 months inactivity and 1 month to refresh.

## The Problem

Currently, we have 1245 open PRs in the main upstream Flink GitHub repository. The oldest of which was created over seven and half years ago. Many of these PRs haven't been commented on or interacted with in years.

I am definitely not here to cast blame. Flink is a huge project, the committers are volunteers and only have so much time. Also, Flink is certainly not the only open source project to face this issue. However, the large number of open PRs is a drag on the community, it makes new contributors think twice about opening PRs and I am sure it is demoralising for committers to see the mountain keep growing.

Dealing with this was a big part of why the [Community Health Initiative (CHI)](https://cwiki.apache.org/confluence/display/FLINK/Community+Health+Initiative+%28CHI%29+workgroup) working group was set up. We are [making progress](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=332500010) on reviewing and triaging the top of the PR stack. However, the bottom of the stack is also an issue.

## Background

It seems reasonable that a PR that is the better part of a decade old and hasn't been commented on in years, is probably not relevant and could be closed. Indeed, this very point has been brought up before, first in [2018](https://lists.apache.org/thread/qpxkm7s0t6qy571bh14lr26jmos6plfp) [where](https://lists.apache.org/thread/k11krmyr5rbclw7jhcmshp828fov3w0n) it was commented that:

> The current situation with 350 open PRs may send a signal to contributors that it may actually be too much hassle to get a change committed in Flink.

At that time, there was some [push-back](https://lists.apache.org/thread/6qlytq81zrctbv9kbk4z30vkngxvw9o1) to the proposal of using the stale PR bot. Mostly around auto-closing the PRs being perceived as harsh, given that the issue was mostly due to lack of committer review. The Beam community went ahead and enabled it, but the discussion on the Flink side seems to have then died out.

The stale PR bot was raised again in [2019](https://lists.apache.org/thread/9gz363224zn48n03yffgsczm8gk3ohro) and had a lot of support, including several examples of other Apache projects using it to good effect. However, this was again [pushed back against](https://lists.apache.org/thread/z5r5sy5j98yb8p02l243gc2qlo267d2w) as hiding the symptoms of the underlying problem, namely committers not engaging actively enough to close PRs that were no longer relevant or had no hope of being merged. The c[ounter argument](https://lists.apache.org/thread/rz6vbzs16jbrs4smhqs940g3cg6godl0) to this was that the PR closing bot was only one part of a solution, not the whole solution and that far from hiding the problem, the stale labelling would highlight the scale of the issue.

The Stale PR closing issue was raised further in [2022](https://lists.apache.org/thread/2z51fxm201z1tnvm2jklmtrhox4t9olw) and [2023](https://lists.apache.org/thread/pml95msx21sdc539404xs9tk209sdd55) with similar arguments. Including from CHI's own [David Radley](https://lists.apache.org/thread/0poktkjxhdpv15s0xgj7oksr7209w737):

> We have over 1000 open prs. This is a lot of technical debt. I came across a 6 month old pr recently that had not been merged.
> A second Jira issue was raised for the same problem and a second pr fixed the issue (identically). The first pr was still on the backlog until we noticed it.

## What other Apache projects are doing

The [Stale PR/Issue GitHub action](https://github.com/actions/stale) is used by many Apache projects including [Beam](https://github.com/apache/beam/blob/master/.github/workflows/stale.yml), [Kafka](https://github.com/apache/kafka/blob/trunk/.github/workflows/stale.yml), [Spark](https://github.com/apache/spark/blob/master/.github/workflows/stale.yml) and [Airflow](https://github.com/apache/airflow/blob/main/.github/workflows/stale.yml) to name a few.

Apache Kafka uses a 90 day (3 months) limit to define a stale PR and then allows a further 30 days (1 month) for the author to refresh the PR before it is auto-closed. Even with the Stale PR action enabled, Kafka still has over 1000 open PRs. However, all of these PRs were updated, commented on or otherwise interacted with in the last 3-4 months. This gives a much better overview of the scale of the open PR base.

For comparison, I did some [basic analysis](https://gist.github.com/tomncooper/d92985065e948fb8fc6284a5438ff195) of Flink's open PRs. 55% were updated in the last 2 years, 41% in the last year, 12% in the last 3 months and only 8% in the last month. It is reasonable to conclude that over half these PRs are probably not relevant anymore or need significant updates to be compatible.

## What should we do?

My personal take on this is that, while I agree that the issue is mostly one of committer capacity to review these PRs, the upstream PR count is currently too high. It is discouraging engagement. But, it is also not fair to blame committers for not wanting to spend time on PRs that are years out of date and clearly not relevant anymore.

So I think we should declare PR bankruptcy and attempt to clear away the bulk of the old PRs. I don't use the word "bankruptcy" flippantly or to provoke, just to acknowledge that the scale of the issue has gotten too large to be dealt with through the hard work of committers alone. Once we get the PR backlog to a manageable size, we can then focus on using initiatives like CHI and other other workflow improvements to keep the PR count low.

## Proposal

Enable the [stale PR GitHub action](https://github.com/actions/stale). This action would:

- Identify any PR that has not been interacted with in the last `X` months as `Stale`:
    - Apply a `Stale` label to the PR
    - Comment on the PR that it is considered `Stale` and what to do to refresh it and how to engage further with the community. This will also allow committers to easily get a list of stale PRs to review and refresh/close.
- Identify any `Stale` PR that hasn't been refreshed (commented on or otherwise updated) after a further `Y` months as closeable.
    - Close the PR.
    - Leave a closing comment highlighting that it can be reopened at any point with pointers to how to engage the community.

The values of the stale (`X`) and close (`Y`) thresholds is up for discussion. At least initially, given the shear number of old PRs, we may want to be more lenient. For example X = 1 year, Y = 3 months, would limit the initial number of stale PRs and allow committers more time to review the stale PR list. Once the PR list has been reduced sufficiently we may want to reduce these values in increments until, for example, X = 3 months and Y = 1 month which seems to be the values other Apache projects have settled on.

Obviously, I am a relative newcomer to the community. I would really like to hear what others, especially committers, think of the above proposal and hear any other ideas people have for taming the PR count.

## Alternatives

Looking through the history of discussion on the subject, on several occasions people have suggested doing more fine grained checks before closing PRs, [such as](https://lists.apache.org/thread/j8s4khdm4wdhvr2px6qkq0f0kwlpc2vg):

> closing up PRs after X days which:
> a) Don't have a CI that has passed
> b) Don't follow the code contribution guide (like commit naming conventions)
> c) Have changes requested but aren't being followed-up by the contributor

This is of course an option, but would probably require updating FlinkBot. There is no reason we couldn't enable both the Stale PR GitHub Action and update Flinkbot to enforce rules like those 