---
title:  "The Software-Defiend Stack | EI"
date:   2016-02-28 15:04:23
categories: [technical]
tags: [OpenStack, Software-Defined Stack]
---

Welcome to the Software-Define Stack Series, this will be an extensive series, exploring the software-defined aspects behind OpenStack and digging deeper into the structure of OpenStack and its internal anatomy.


Before I start to talk about OpenStack specifically, let me explain why this series is called the “Software-Defined” series. The term Software-Defined was first used to describe networks which follow these following patterns:
<ul style="list-style-type:disc;">
	<li>Has decoupled control and data-planes (i.e., the brain is not  in the body)</li>
	<li>Abstracts the underlying functions of the network components (i.e., The brain only speaks one language and all the body organs understands it regardless of which organ it is)</li>
	<li>Follow a logically-centralized yet physically distributed model (no single point of failure, super-human with many brains)</li>
</ul>
With time many vendors started adopting the term software-defined and mapping it to different technologies such as storage and compute, along the line, another school of thought emerged, the “Automation” school that is.

What we will be looking at in this series is how OpenStack really applies the Software-Defined paradigms (i.e., abstractions, decupling, and automation) from the different schools of thought. Without further ado, let’s start!

<img class="alignnone size-full wp-image-536" src="https://adelzalok.files.wordpress.com/2016/04/openstack-sm.png" alt="openstack-sm" width="600" height="255" />

<strong>What is OpenStack? </strong>
<p style="text-align:justify;">You probably have read about OpenStack somewhere before you come here, but what is it really?!  I consider OpenStack a very big abstraction. The whole idea behind OpenStack is to bring together different components (e.g., networking, storage, and compute) under the same roof and allowing smooth communication between these components. The nice part here is that these components are built by different vendors.  Actually, vendors contribute huge amounts of code to OpenStack in order to rise and shine, at the end; it is all about meritocracy though. With OpenStack you get A LOT of choice on how to build your own IaaS or PaaS.</p>
<p style="text-align:justify;">Every six months, OpenStack spins out a new release fixing bugs, adding new features, etc. The names of these releases are Alphatical (e.g., Austin, Bexar, Cactus, Diablo, Essex, Folsom, Grizzly, etc). The current release of OpenStack is Liberty (released October 2015), please refer to [1] for a complete list of release names.  Furthermore, many projected are developing within OpenStack each providing a distinct function, these projects include Horizon, Keystone, Nova, Cinder, Swift, Neutron, Glance, etc. We will be targeting each one individually in other episodes of this series, however, just to maintain a level of understanding, each service represents a function (e.g., storage, compute, networking, identity management, etc)</p>
<img class="alignnone size-full wp-image-537" src="https://adelzalok.files.wordpress.com/2016/04/osog_0001.png" alt="osog_0001" width="1630" height="1158" />
<p style="text-align:justify;">The OpenStack architecture is plugin-based, by default OpenStack comes with reference architecture for each of its services, however, the default settings can be easily be replaced In accordance with the use-case.</p>
<p style="text-align:justify;"><strong>Why OpenStack? </strong></p>
<p style="text-align:justify;">The answer is dead-simple. It is because of the “Open” part in  OpenStack. Additionally, the DIY option.  Some (not all) the customers fancy building their own infrastructure from scratch, they feel more comfortable this way, so why put restrictions? With OpenStack we have many options; customers can do everything in-house OR can make use of a boxed solution which is wrapped around OpenStack with a lot of engineering around it. In both cases, OpenStack provides more flexibility, and that’s why it is appealing.</p>
<p style="text-align:justify;"><strong>What’s next? </strong></p>
<p style="text-align:justify;">In the following episodes, we will be looking at the OpenStack architecture, the components, how they interact, and also looking at how OpenStack applies a Software-Defined patterns to provide everything-as-a-service.</p>
<p style="text-align:justify;">Stay tuned!</p>
<p style="text-align:justify;"><strong>References</strong></p>
<p style="text-align:justify;">[1] <a href="https://wiki.openstack.org/wiki/Release_Naming">https://wiki.openstack.org/wiki/Release_Naming</a></p>
