---
title:  "Sandboxed Vs. Vanilla Runtimes"
date: 2021-17-11
categories: [technical,kata]
tags: [misc]
---

If you are curious how sandboxed containers look like from the inside compared to normal containers, this thread will be covering the basics.



 
{{<audio src="https://archive.org/details/jamendo-097169/05.mp3" caption="test" >}}

[@openshift](https://twitter.com/openshift) sandboxed containers is used to bootstrap and configure the runtime (aka [@katacontainers](https://twitter.com/katacontainers)) for the extra layer of isolation. It is also worth noting, that in all the following snippets, NO other fancy hardening is done (e.g., admission controllers, PSPs, PSA, SELinux,...). Don't try this at home! 

This is to highlight the effect of sandboxing alone compared to vanilla runtimes. 

Let's gooo...

## HostPID

> Controls whether the pod containers can share the host process ID namespace

Ref: [kubernetes.io/docs/concepts/…](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces)

**Sandboxing effect:** Host processes are not exposed in the sandbox.

[youtube.com/watch?v=_LEwjV…](https://www.youtube.com/watch?v=_LEwjVpWPhQ)


## HostIPC

> Controls whether the pod containers can share the host IPC namespace

Ref: [kubernetes.io/docs/concepts/…](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces)

**Sandboxing effect:** Memory sharing is disabled in the sandbox.

[youtube.com/watch?v=zgdZ-O…](https://www.youtube.com/watch?v=zgdZ-OYG4-Q)

## HostNetwork

> Controls whether the pod may use the node network namespace

**Sandboxing effect:** This is an easy one, HostNetwork is not even possible, remember kernel isolation is in effect (this is technically a "VM"). 

Ref: [kubernetes.io/docs/concepts/…](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces)

[youtube.com/watch?v=WpXfwr…](https://www.youtube.com/watch?v=WpXfwr5BhEg)


## Privileged

> A "privileged" container is given access to all devices on the host

Ref: [kubernetes.io/docs/concepts/…](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#privileged)

**Sandboxing effect:** Access to host devices are not allowed in the sandbox.

[youtube.com/watch?v=zDgj3u…](https://www.youtube.com/watch?v=zDgj3umFJgg)

For more information on [@katacontainers](https://twitter.com/katacontainers), checkout their doc page [katacontainers.io](https://katacontainers.io/) and if you want to deep-dive, here is an end-to-end interactive flow: [thinglink.com/scene/14012360…](https://www.thinglink.com/scene/1401236075678007299)

## OpenShift sandboxed containers

Finally, for more information on sandboxing with [@openshift](https://twitter.com/openshift) sandboxed containers checkout:

- The documentation: [docs.openshift.com/container-plat…](https://docs.openshift.com/container-platform/4.9/sandboxed_containers/sandboxed-containers-4.9-release-notes.html) 

- Our blog series: [cloud.redhat.com/blog/tag/opens…](https://cloud.redhat.com/blog/tag/openshift-sandboxed-containers)

Layer-up, it's worth it :)





