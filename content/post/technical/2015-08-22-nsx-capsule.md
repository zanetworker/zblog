---
title:  "NSX: A Morning Capsule"
date:   2015-08-22 15:04:23
categories: [technical]
tags: [VMware]
---
I have recently had the chance to do some reading on VMware NSX. I come from a networking background Intertwined with software, previously I have had a chance to research on some of the underpinnings of NSX but never had the time to look at the big picture, so in this writing, I am going to briefly describe the NSX architecture.

### What is NSX?
<p style="margin-bottom:.0001pt;">NSX is VMWare virtual networking on steroids.</p>
<p style="margin-bottom:.0001pt;">There are several components that constitute the NSX Architecture. The reason why I believe that NSX is a true Software Defined architecture is the clean decoupling and separation of its components.</p>

<p style="margin-bottom:.0001pt;">The NSX architecture is divided into three planes; the management-plane, the control-plane, a<span style="font-size:10pt;line-height:1.5em;">nd the data-plane. Most of the other architectures mix both the control and management plane </span>functions which leads to a highly complex architecture. Accordingly, the components that constitute the NSX architectures are divided to match the decoupled architecture.</p>
<p style="margin-bottom:.0001pt;">Lets have a deeper look into each.</p>
<p style="margin-bottom:.0001pt;"><a href="https://adelzalok.files.wordpress.com/2015/08/high-level-description-components-nsx.png"><img class="alignnone wp-image-330" src="https://adelzalok.files.wordpress.com/2015/08/high-level-description-components-nsx.png?w=300" alt="High-Level-Description-Components-NSX" width="543" height="282" /></a></p>

### Management Plane
<p style="margin-bottom:.0001pt;">The NSX manager is the entity acting as the management plane in NSX.  It automates configuration management functionalities (e.g., switching on/off network interfaces, timers, VLANs, VxLANs,  etc).  <span style="font-size:10pt;line-height:1.5em;">To configure the underlying devices, it utilizes the REST style with JSON or XML as the protocols to carry the  the management commands.</span></p>

### Control Plane
<p style="margin-bottom:.0001pt;">The NSX Controller Cluster is what makes the control-plane. I would describe the controller cluster as the network state maintainers. It keeps track of all the ARP entries, the MAC tables, or the VxLAN mapping details. Additionally, this is where the routing table is stored, this is crucial to routing between different virtual networks.</p>
<p style="margin-bottom:.0001pt;">In NSX there are multiple ways to which VMs in different hosts / clusters communicate.</p>

<ul style="list-style-type:disc;">
	<li><span style="text-decoration:underline;">Unicast</span> is where packets are sent from one VM to each other VM separately, this requires the VM to know all the destination VMs addresses.</li>
	<li><span style="text-decoration:underline;">Broadcast</span> is where packets is sent to all the VMs in the network (this depends on the underlying broadcast domains)</li>
	<li><span style="text-decoration:underline;">Multicast<em>,</em></span> here VMs the wishes to receive the packet need to pre-subscribe for packet updates. Only subscribed VMs will be able to receive the packet.</li>
</ul>
<p style="margin-bottom:.0001pt;">Maintaining state in the NSX controller cluster can greatly reduce network traffic, this is because controllers are aware of the network topology and state.</p>


### Data Plane
<p style="margin-bottom:.0001pt;">This is where the packets dance (i.e., the data path).  There are many entities that make up the data plane.</p>

<ul style="list-style-type:disc;">
	<li><span style="text-decoration:underline;">Logical Switches </span>for Intra-network VM  traffic management</li>
	<li><span style="text-decoration:underline;">Distributed Logical Routers </span>kernel modules for Inter-network VM traffic management</li>
	<li><span style="text-decoration:underline;">NSX Edge</span> provides multiple functions such as:
<ul style="list-style-type:circle;">
	<li>Firewall</li>
	<li>Network Address Translation (NAT)</li>
	<li>DHCP</li>
	<li>VPN (L2, SSL)</li>
	<li>Load balancing</li>
</ul>
</li>
	<li><span style="text-decoration:underline;">Distributed Firewall </span>maintains per-flow isolation of traffic; it supports creation of security groups and application of L2-L4 security policy. Additionally, provides support for integration with third-party modules for L4-L7 support.</li>
</ul>


In summary, I found NSX pretty decent compared to other Software-Defined solutions out there. The clear decoupling between the different network functions did it for me.  It is important to keep in mind that NSX being improved and developed, in its current state (v6.1) it only supports one data center instance, in future releases (v6.2) , it will support universal policy and universal state management taking datacenter networking to a whole new level.

Of course this article is just scabbing the surface, however, I hope that it has helped you get a better understanding of what NSX is and what it is capable of. Please contact me if you think modifications / corrections are needed.
