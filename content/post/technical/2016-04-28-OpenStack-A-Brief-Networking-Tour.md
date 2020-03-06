---
title:  "The Software-Defined Stack | EII OpenStack Networking"
date:   2016-04-28 15:04:23
categories: [technical]
tags: [OpenStack, Software-Defined Stack]
---

In this post, we are going take a quick look at how OpenStack does networking, along the process we are going to mention the important agents involved.

<ul style="list-style-type: disc;">
	<li>Tiny Networking Capsule</li>
	<li>Nova-network</li>
	<li>Neutron Networking</li>
	<li>Meta-data service</li>
	<li>Summary</li>
</ul>
<!--more-->
<strong>What is a layer 2 network? </strong>

It is a network that does require any routing hops (e.g., traffic in the same subnet).
<ul style="list-style-type: disc;">
	<li>Usually, a switch does not divide devices connected to it into multiple domains, all are part of the sane broadcast domain.</li>
	<li>To break a broadcast domain VLANs are used (tagging traffic), the VLAN tagging types are:
<ul style="list-style-type: circle;">
	<li>Access port: configured for access to one-vlane (i.e., a one tag port)</li>
	<li>Trunk port: can recieve frames tagged / labled from different VLANs</li>
</ul>
</li>
</ul>
<strong>What is Nova-Network?                                                                                                  </strong>

It is the primitive way of doing networking in OpenStack.  It does the following:
<ul style="list-style-type: disc;">
	<li>Basic layer-2 bridging through Linux bridges</li>
	<li>IP Address management for tenants (give IP address and management for tenants)</li>
	<li>Configure DHCP and DNS entries in "dnsmasq"</li>
	<li>Configure fw-policies and NAT in IPTables to build security for in/out-bound traffic</li>
</ul>
<strong>What are Nova-network Models? </strong>
<ul style="list-style-type: disc;">
	<li>FlatNetwork – no traffic segregation on the host level, VLANs and network functions are set at the network level</li>
	<li>VLANetwork – each tenant gets one VLAN (the VLAN limit) + dnsmasq service that does DHCP functions on host level</li>
</ul>
<img class="alignnone size-full wp-image-478" src="https://adelzalok.files.wordpress.com/2016/03/capture.png" alt="Capture" width="748" height="475" />

<strong>Problems in Nova-Networking? </strong>
<ul style="list-style-type: disc;">
	<li>Inherits the VLAN sizing limitations (e.g., 4094 VLAN IDs).</li>
	<li>Since it is coupled with Nova (the compute part of OS), it is very limited and does not allow integrations with networking products from other vendors</li>
	<li>Poor at multi-tenancy (Flat-network)</li>
	<li>No Support for L3 devices</li>
</ul>
From precious posts it is about SDN it is highlighted that Abstraction is a key element for a <strong><em>Software-Defined technology</em></strong>, and it is clear that Nova-network is not doing that abstraction very well, we need something else clearly…. Neutron to the rescue.

<strong>What is Neutron? </strong>

Neutron is an API wrapper. It basically receives commands / calls to perform traditional networking and relay it to the <span style="font-size: 12pt;">network </span>plugin of choice (e.g. NSX). With this, abstraction is achieved.

<strong>What are the main components of Neutron? </strong>

Neutron runs along-side a controller node on what is referred to as the network node.  On the network node, the following services are running:
<ul style="list-style-type: disc;">
	<li>Neutron Server + Open vSwitch (OVS) Plugin – controls all the agents running on the network node</li>
	<li>N-L3Agent – controls namespaces and ip-tables and does all the NAT functions</li>
	<li>N-L3-DHCP – Assigning IPs to VMs and acting as a DHCP server (i.e., controls the “dnsmasq” MAC to IP mappings)</li>
	<li>N-OVS-Agent – talks to the switch to configure flows for traffic</li>
</ul>
<img class="alignnone size-full wp-image-479" src="https://adelzalok.files.wordpress.com/2016/03/capture2.png" alt="Capture2" width="720" height="521" />

Basically, the OVS plugin communicates with the OVS-agent which in turn configures the flows and tunnles on the underlying bridges. The ovs-agents are installed on all the nodes (compute and network). When the traffic is forwarded it gets tunneled in the bridge through the configuration applied to by the OVS agent.

<span style="text-decoration: underline;"><em>Note:</em></span> This is not a completely software-defined pattern as we still have the control-plane coupled in our virtual network. By adding a controller we could better comply with the software-defined pattern (will address that in a different post).

<strong>Nova Meta-data service</strong>

A Metadata services provides important information to the guest instance for correct communication, these include:
<ul style="list-style-type: disc;">
	<li>Setting a default locale or a host name</li>
	<li>Setting up ephemeral storage mount points</li>
	<li>Generate SSH private keys, and add SSH keys to us user’s .ssh directory</li>
</ul>
In previous versions of Neutron (e.g., Folsom), the <em>dhcp-agent</em> used to provide a static routing option to add static routes to “169.254.169.254” (i.e., <em>dhcp-agent</em> host), <em>IPtables</em> on the <em>dhcp-agent</em> hosts then NATs the request either to the local met-data server or to a meta-data services which is remotely installed. This method however has no support for overlapping IPs in different projects / Tenants.

<img class="alignnone size-full wp-image-476" src="https://adelzalok.files.wordpress.com/2016/03/met-data-service.png" alt="met-data-service" width="491" height="134" />

In later versions of OpenStack (e.g., Grizzly, Havana, Icehouse, Juno, etc.) the problem was solved by dedicating two services (<em>quantum-ns-metadata-proxy</em> and <em>quantum-metadata-proxy</em>). Meta-data requests are then forwarded by the N-L3-Agent to the <em>quantum-ns-metadata-proxy </em>in the tenant namespace and then forwards it to the <em>quantum-metadata-proxy </em>service via a dedicated internal socket, and adding two headers that identifies the tenant namespace (X-Forward-For and X-Quantum-Router-ID) and this solving the overlapping IP problem previously existed. Since the metadata-proxy is the only service that can reach to the management network, it will be used to forward the request to the nova-metadata which could be installed on one of the nodes in the management network.

<img class="alignnone size-full wp-image-480" src="https://adelzalok.files.wordpress.com/2016/03/meta-data-late.png" alt="meta-data-late" width="474" height="344" />

<strong>Summary </strong>

In this post we reviewed nova-networking (deprecated) that was used before neutron, we also highlighted the problems that led to the development of Neutron. With Neutron, many extra functionalities were added (e.g., tunneling and improved multi-tenancy through the usage of IDs and headers), it should be noted that we did not address all the aspects of Neutron only those of major importance. In future writings, we will be tackling deeper topics and we will be looking more into the other components of OpenStack.
