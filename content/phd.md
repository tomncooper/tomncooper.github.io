+++ 
title = "PhD Research" 
+++

# Modelling distributed stream processing topologies

## Introduction

Distributed Stream Processing Systems (DSPS) take a stream of real time data (often
arriving at high speed and large volume) and pass it through a series of operators. These
operators perform tasks on the packets of data (often called "tuples" in the literature).
These tasks can involve splitting large tuples into smaller ones, counting tuples with
particular characteristics (counting hashtags in tweets for example) or combining them
with other information from external systems (like databases). Operators can then create
new tuples and pass them on to operators further along the topology (downstream). Figure 1
shows an example topology.

{{< figure src="/images/topology.png" alt="Example topology" position="center" style="border-radius: 8px;" caption="Example DSPS topology" captionPosition="center" >}} 

### Scaling

DSPS provide ways to spread these operators across several machines in a cluster. Each
operator can be replicated multiple times in order to handle a large arrival workload. In
the topology in Figure 1, operator B takes a long time to process tuples, so there are 3
copies of it to ensure that it does not become a bottle neck. In this way DSPS topologies
can be _tuned_ or _scaled_ to ensure they meet throughput (tuples processed per second) or
end-to-end latency (the time between a tuple entering the topology and the last tuple
resulting from its entry leaving) targets. Conversely, if the input workload into the
topology drops then copies of operators can be removed (scaled down) to save on cluster
machine resources.

### Scheduling

Figure 2 shows an example of how the various copies of the operators from the topology in
Figure 1 could be laid out on the cluster. The process of deciding where to place each
copy of the various operators is called _scheduling_ (in Heron this is called _packing_)
and the resulting plan of operators they produce is called a _physical plan_.

{{< figure src="/images/cluster-layout.png" alt="Example physical plan" position="center" style="border-radius: 8px;" caption="The layout of operators on the cluster (physical plan)" captionPosition="center" >}}

Most modern DSPS, such as [Apache Storm](https://storm.apache.org) and [Apache
Heron](https://heronstreaming.io), have the ability to easily scale up or scale down the
operators of a topology and re-schedule the physical plan. Many scheduling algorithms are
available for these systems (each based on optimising for certain criteria) and even more
are represented in the literature.

## Problem Definition

Most DSPS provide the ability to scale their topologies to meet varying demand. However,
as far as I am aware, of all the mainstream DSPS only Heron provides a method to do this
scaling automatically. This system, Dhalion, is available in the latest Heron version but
has not yet been deployed in a production environment.

As well as Dhalion, there are many examples from the research community of attempts to
create automatic scaling systems for DSPS. These are usually based on optimising
schedulers that aim to minimise certain criteria, such as the network distance between
operators that communicate large tuples or very high volumes of tuples or to ensure no
worker node is overloaded by operators that require a lot of processing resources. While
the new physical plans these schedulers produce may be optimal compared to the current
cluster operator layout, none of these systems assess weather the physical plan they
produce will actually result in a topology that is capable of meeting a performance
target. 

If a modelling system was available that could assess a proposed physical plan and predict
its likely performance _before_ it is deployed, an auto-scaling system could iterate to a
resource and operator layout plan that is likely to meet a given performance target.

This would reduce the overhead from continually deploying a new physical plan (something
which can take significant time for the system to complete and stabilise) and assessing
its performance, only to have to re-schedule when it doesn't meet the requirements.

What's more, a modelling system would allow several different plans to be assessed in
parallel. This means that schedulers optimised for different criteria could be compared
simultaneously, which would remove the need for a DSPS user to know before hand which
scheduler is best for their particular use case.

A further advantage of a modelling system for DSPS topologies is that of pre-emptive
scaling. If the model can accept a prediction of future workload (number of tuples
entering the topology) then the auto-scaling system, where paired with a future workload
prediction technique, would be able to assess if a future workload will be an issue for
the current cluster layout. The system could then use the future workload level to find a
physical plan that is likely to meet the performance requirements and deploy this plan
before the predicted workload arrives.

## Proposed Solution

The aim of my PhD research is to create the modelling system, described above, for DSPS
topologies. The test bed for this research is the [Apache Storm](https://storm.apache.org)
DSPS.

I will use approaches from queuing theory, graph analysis and other areas to create a
system capable of modelling the performance of a proposed physical plan from one or more
scheduling algorithms.

The aim will be to create a topology model that can accept a predicted incoming workload
and physical plan. The model will then use these, along with metrics from recorded from
the topology in its current configuration, to estimate end-to-end latency, throughput and
other attributes of the plan such as queue overloads for operators. I also hope to
incorporate modelling of resource usage on the host cluster such that proposed plan can be
rejected if they are likely to lead to overloading of worker nodes.

The final modelling system will be combined with Storm's native rebalance function to
automatically assess proposed physical plans and add resource to cluster if a plan is
unlikely to meet a given performance target. In this way it is hoped that with only
several minutes of running data this system could create a reasonable configuration to
meet a given performance target without user intervention. Figure 3 show a flow diagram of
proposed auto-scaling system.


{{< figure src="/images/proposed-solution.png" alt="Example physical plan" position="center" style="border-radius: 8px;" caption="Flow diagram illustrating the operation of the proposed pre-emptive scaling system" captionPosition="center" >}} 

## Publications

A detailed description of my research can be found in my recent [Doctoral Symposium Paper](http://dl.acm.org/citation.cfm?id=2933429) published as part of the [DEBS 2016](http://www.debs2016.org/) Conference.
