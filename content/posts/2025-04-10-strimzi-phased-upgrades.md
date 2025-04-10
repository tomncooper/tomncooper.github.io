+++
title = "Strimzi Phased Upgrades"
date = "2025-04-10"
description = "Strimzi handles the upgrade of Kafka clusters for you, making it simple and easy for infrastructure teams to handle these complex operations. The upgrade of the Strimzi operator itself is also straight-forward. However, when you have a large fleet of Kafka clusters being managed by Strimzi, the upgrade will cause all those clusters to roll. There are ways around this and this blog gives an example."
+++

Strimzi handles the [upgrade of Kafka clusters](https://strimzi.io/docs/operators/latest/deploying#proc-upgrade-kafka-kraft-str) for you, making it simple and easy for infrastructure teams to handle these complex operations. 
The [upgrade of the Strimzi operator](https://strimzi.io/docs/operators/latest/deploying#assembly-upgrade-cluster-operator-str) itself is also straight-forward. 
However, when you have a large fleet of Kafka clusters being managed by Strimzi, upgrading the operator will cause all those Kafka clusters to roll.

For large production deployments, like those we ran at Reddit, this is not a desirable situation. 
It would be better if you could control which Kafka clusters get rolled and when.
This question comes up a lot in the upstream Strimzi community, as well as from Red Hat customers. 
So I put together a blog, for the main Strimzi site, which goes over the background and an example deployment using `kustomize` to create a way of having multiple Strimzi versions deployed concurrently and handing off control of Kafka clusters between them:

[https://strimzi.io/blog/2025/04/10/phased-strimzi-upgrade-example/](https://strimzi.io/blog/2025/04/10/phased-strimzi-upgrade-example/)

I also create a [demo repository](https://github.com/tomncooper/strimzi-phased-upgrade) with example deployment files and scripting to add new Strimzi versions.

The first question might be "Why doesn't Strimzi support this upstream", to which the answer is that there are so may permutations of versions, deployment methods, RBAC setups etc. 
That it would be very difficult to come up with a generic solutions (not to say that one day we won't try). 
So in the meantime I hope this example will help teams craft their own solutions.

This example uses `kustomize` but I am also keen to try creating an example using `helm` or the Operator Lifecycle Manager (OLM). 
So look out for part 2.