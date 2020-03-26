+++
title = "Caladrius"
date = "2018-07-18"
+++

## TL;DR

Caladrius is the result of a 4 month internship with Twitter's Real Time
Compute team. It is a service that interfaces with the
[Heron](http://heronstreaming.io) stream processing system and models the
performance of topologies running within it. Caladrius has the ability to
predict future incoming workloads (based on sufficient historical data) which
allows the performance of a running topology to be checked against that
expected workload, giving the option to pre-emptively scale the topology before
that workload arrives.

The (very alpha and not yet production ready) code has been open sourced (under
the [Apache v2 Licence](https://www.apache.org/licenses/LICENSE-2.0)) and is
available on [GitHub](https://github.com/twitter/caladrius). Further
details about the features and operation of Caladrius are provided on the
[documentation site](http://caladrius.readthedocs.io/en/latest/) (which will be
heavily updated over the next few weeks).

## A bit of context...

My PhD research (as part of [Newcastle University's](https://www.ncl.ac.uk/) [Center for
Cloud Computing for Big Data](http://www.bigdata-cdt.ac.uk/)) is focused on modelling the
performance of distributed stream processing topologies, like those that run inside
[Apache Storm](http://storm.apache.org/). The [PhD](/pages/phd-research.html) section of
this site has more details and explanations of the systems and terminology used in this
post. 

### A bit of backstory...

In the summer of 2016 I attended the Distributed Event Based Systems
([DEBS](https://www.ics.uci.edu/~debs2016/)) conference in California, where I was
presenting my work as part of the conference's Doctoral Symposium (you can find the paper
I presented [here](http://dl.acm.org/citation.cfm?id=2933429)). The keynote speaker at
DEBS that year was [Karthik Ramasamy](https://www.linkedin.com/in/kramasamy/), who at the
time was the Engineering Manager for Twitter's Real Time Compute team, which handled their
stream processing system. [His
talk](https://www.ics.uci.edu/~debs2016/keynote-speakers.html#ramasamy) focused on their
replacement for Apache Storm (the system I was using as the basis for my research) with a
new system called [Heron](http://heronstreaming.io). At the end of talk he listed some
continuing challenges in stream processing, one of which was scaling the topologies to the
correct size to meet the expected incoming workload. Their engineers were spending days
and weeks tweaking the production topologies. The main issue was that the loop: deploy
topology \> wait for it to stabilise \> analyse the performance \> tweak \> redeploy the
topology was very time consuming and would have to be repeated multiple times to get the
topology to perform at the required level. 

It seemed to me that if you could model the performance of a topology before it was
deployed, then you could skip several of those steps and reduce the time taken to scale
the topology. I remember him saying: 

> "_If anyone one thinks they would be able to help with this, come and see me
  at the coffee break..._" 

I duly chewed his ear off (a delightful English phrase implying I talked incessantly to
him) at the coffee break and after nearly 2 years of emails, video calls, Slack chats,
Karthik leaving Twitter to start a new company ([Streamlio](https://streaml.io/)),
connecting with the new team leader ([Maosong
Fu](https://www.linkedin.com/in/maosong-fu-63471a34/)) and passing Twitter's intern
application process, [I started]({filename}2018-03-20-twitter-internship.md) at Twitter's
San Francisco HQ in March this year.

### What's in a name...

So, in computer science, naming things is one of the two hard problems (along with cache
invalidation and off-by-one errors). When you start a project you need to call it
something and the _Heron Topology Performance Modelling Service_ (HTPMS) didn't exactly
roll off the tongue. So I wanted a single word name for the project that was in some way
linked to the Heron. Recently a auto-scaling framework for Heron (and other stream
processing systems), called
[_Dhalion_](https://blog.acolyer.org/2017/06/30/dhalion-self-regulating-stream-processing-in-heron/),
had been introduced by researchers at Microsoft ([Avrilia
Floratou](/www.microsoft.com/en-us/research/people/avflor/) and [Ashvin
Agrawal](https://www.linkedin.com/in/ashvinagrawal/), who I had the great pleasure of
meeting in my first few weeks in California). Dhalion is the Greek word for the legendary
bird that is said to be able to take the sickness into itself and then fly away,
dispersing the sickness and healing both itself and the sick person. 

![Caladrius](https://upload.wikimedia.org/wikipedia/commons/thumb/9/97/Caladrius2.jpg/343px-Caladrius2.jpg)

Thanks to the wonders of [Wikipedia](https://en.wikipedia.org/wiki/Caladrius) I discovered
that the Roman version of the Dhalion legend is called _Caladrius_. I hoped that the
modelling service I was building could one day integrate with Dhalion and this, along with
the fact Caladrius was also a bird (so seemed apt for a Twitter based project), made it
feel like the perfect fit. And that, dear friends, is how Caladrius got its name.

As a side note, I wish more open source and academic software projects would explain their
names (The [Dhalion
paper](https://www.microsoft.com/en-us/research/publication/dhalion-self-regulating-stream-processing-heron/)
had a wonderfully informative footnote to this effect). 

## What does Caladrius do?

### The best laid plans...

Initially, my goal was to directly apply my research from Apache Storm to Heron, which is
designed to be backward compatible with Storm topologies. The idea was to intercept
proposed topology physical plans (called _packing plans_ in Heron) and model their
expected performance, perhaps integrating the system into Heron's existing command line
tool as part of the `update --dry-run` option.

However, as I dug deeper into the Heron code it became apparent that there were
significant differences between how Heron and Storm handle the partitioning of state
within the steam operators (_instances_ in Heron and _executors_ in Storm). Storm's state
management approach (using a set number of Tasks to create a pre-defined key partition
space) allows me to predict how key based (fields) connections will route keys when the
number of downstream operators changes. Heron's approach (with no fixed Tasks or
pre-defined key partitioning) mean that this is not possible. So for the time being
predicting the performance of proposed changes to a topology was out the window.

This meant that I had to pivot to a new plan. I decided to focus on modelling the
performance of a currently running topology and use that model to predict the effect on
that topology of different incoming workloads (traffic).

### Currently.....

Caladrius provides a REST API by which you can supply a traffic level for a given topology
running on your Heron cluster. It will talk to the Heron Tracker service and configured
metrics databases to create a model of the topology, returning a prediction of the
topologies performance. This currently takes the form of arrival rate predictions for all
the topology's operators and a check for back pressure issues.

Caladrius also provides a way to predict the traffic into a topology (provided a
sufficient amount of historical traffic data is available). This feature uses the open
source [Prophet](https://facebook.github.io/prophet/) library (from Facebook) to build a
model of the traffic and predict a given amount of time into the future. This predicted
traffic level can then be fed to the performance modelling service, allowing Heron users
to check, ahead of time, if their topology is likely to experience slow downs or back
pressure issues.

There are some caveats to the types of topologies that can be modelled and these are
described in more detail on the [documentation
site](http://caladrius.readthedocs.io/en/latest/).

## What will Caladrius do in the near future?

Given the short time frame in which Caladrius was created there are many areas that can be
improved upon. Caladrius is still under active development at Twitter, with interns who
came after me continuing the research and testing the system against Twitter's production
topologies. Together we are working to flesh out the performance prediction code (to
include resource usage predictions), simplify the REST API and improve the overall
performance of the modelling process.

### Proposed physical plans

As I mentioned above, the original plan was to have Caladrius predict the performance
effect of a change to a topology's physical plan. Even though this is not feasible with
Heron currently, there are plans to introduce changes to Heron's stateful processing code
that will make this kind of prediction possible in the future. 

Once these changes are completed Caladrius can be extended to allow a fully pre-emptive
scaling approach. A topology's incoming traffic level can periodically be predicted with
the effect of that traffic level assessed using the current performance model. If this
indicates that there will be an issue, one of the many optimising packing algorithms or
scaling systems like Dhalion can be employed to create new packing (physical) plans for
the topology. These proposed plans can then be modelled allowing the best plan to be
selected and deployed before the problematic workload arrives at the topology.

### Additional models

Currently, the performance modelling system is based on a queuing theory approach and the
traffic prediction uses the Prophet modelling package. However, Caladrius is designed to
be extensible. Multiple models can be configured and run on each topology. My hope for
Caladrius is that is could be used as the basis for further research in this area.
Researchers and engineers can use the metrics interfaces, graph database facilities and
modelling code that Caladrius provides to quickly get up an running with the data they
need to develop new modelling approaches.

### Additional streaming systems

As Caladrius is built to be extensible, I also intend to adapt the code I have already
written for performance modelling of Apache Storm topologies into the system. As with
developing multiple models, it is also my hope that Caladrius could be used to model
systems other than Heron and Storm, such as Apache [Flink](https://flink.apache.org/) or
[Spark](https://spark.apache.org/). This would allow a single Caladrius instance to be
used with multiple stream processing systems and compliment their auto-scaling efforts.

## I would like to thank....

Firstly, Karthik Ramasamy for listing to an overly excited PhD student all those years ago
and continuing to provide advice. Maosong Fu for listening to my proposal and letting me
join his team. The Twitter Real Time Compute team: Huijun Wu - for mentoring me, - Ning
Wang for being so friendly, Neng Lu - for being a great technical interviewer and to all
of them for putting up with my constant questions! The Heron community were (and continue
to be) a great help, I think the [slack channel](http://heronstreaming.slack.com) are sick
of hearing from me! 

Thanks go to [Remy DeCausemaker](https://www.linkedin.com/in/decause/), of the [Twitter
Open Source Software](https://twitter.com/TwitterOSS) team, for his help in shepherding
Caladrius through Twitter's OSS process.

I would also like to that [Faria Kalim](http://kalim2.web.engr.illinois.edu/), who is a
summer intern with the Real Time Compute team and has been given the unenviable task of
continuing the development of Caladrius!

Finally, I would like to thank my Supervisor [Dr Paul
Ezhilchelvan](https://www.ncl.ac.uk/computing/people/profile/paulezhilchelvan.html#background)
for his continued patience and support and the [EPSRC](https://epsrc.ukri.org/) [Cloud
Computing for Big Data CDT](http://www.bigdata-cdt.ac.uk/) at
[Newcastle](http://www.ncl.ac.uk) who have provided the environment (and funding) to make
this research and internship possible.
