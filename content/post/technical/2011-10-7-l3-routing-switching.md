---
title:  "L3 Routing vs. L3 Switching vs. Multi-layer Switching"
date:   2011-10-07 15:04:23
categories: [technical]
tags: [Networking, Cisco]
---


Okay, the difference between a layer 3 router,  switch and a multilayer switch seems to confuse alot of networkers, so I decided to write this to briefly explain in a nutshell the main between both.

<b>Before starting to talk about layer 3 switches or multi-layer switches, why not briefly summarize "Switching" as in basic layer 2 switching.</b>

Before switches existed there were devices called bridges, and I will not be explaining or going into much details about what is a bridge, but here is the main differences between a bridge and a switch:
<ul>
	<li>A Switch is a bridge but with more than only two port.</li>
	<li>A Switch can connect more the two LANs, while bridges were originally intended for connecting two LANs together.</li>
	<li>Now that we know the main differences, lets talk about layer 2 switching.</li>
</ul>
In layer 2 switching, switching is done based on the source and destination MAC addresses (SA and DA), Here are the steps:
<ul>
	<li>The first thing that the switch do it to learn MAC addresses, MAC addresses are simply learned  by saving Source Addresses in the Switch CAM table mapped to their associated ports).</li>
	<li>If the SA already exists in the CAM table, then the switch updates it's aging timer.</li>
	<li>The switch then examines the DA, If the destination address is a Broadcast or an UN-recognized unicast, the frame is flooded out to all ports (Broadcast is flooded out all ports because we are not using VLANS)</li>
	<li>If the SA and the DA are the same, the frame is discarded.</li>
	<li>If the DA exists in the CAM table, the frame is forwarded to the DA associated port.</li>
</ul>

## Layer 3 Routing

Okay, now we are getting to the interesting stuff. Basically a Layer 3 Router is a device that uses layer 3 addresses (IP as example) to transmit packets from one network to another. How does it do that ? Well, there are two ways:
<ul>
	<li><b><i>Static Routing</i></b><i>: </i>Manually stating the next hop the packet should take to travel to a certain destination network, For example: ip route 172.31.10.0 255.255.255.0 10.10.10.2</li>
	<li><b><i>Dynamic Routing</i></b>: Using Dynamic Routing protocols to calculate the best path from a source network to a destination network. There are alot of Routing Protocols out there (RIP, EIGRP, OSPF, BGP, IS-IS,...)</li>
</ul>
A Layer 3 router Can inherently separate both collision domains and Broadcast domains. Let's stop saying a layer 3 router because a "router" only resembles layer 3 xD. A router can have WAN connectivity on WAN interfaces (Keep this in mind, we will use it later).

## Layer 3 Switching:

Okay, so what makes a layer 3 switch different from a switch or from a router? Basically a layer 3 switch is a normal  switch, but it can do a router's job.

A Layer 3 switch:
<ul>
	<li>Has as many ports as a normal switch (24 for example).</li>
	<li>Has a route processor built inside it, this means that it can understand layer 3 addresses and can perform operations using them.</li>
	<li>Route packets at wire speed. It can route packets between different IP subnets.</li>
	<li>Doesn't have WAN Connectivity like a router (stated above)</li>
	<li>Uses Application Specific Integrated Circuits (ASICs) hardware to route packets</li>
	<li>Can filter packets and perform QoS based on layer 3 IP addresses and DSCPs.</li>
	<li>Can be placed any where in the network without causing performance degradation, Un like a router which causes congestion and bottle necks that leads to  poor performance, However using a router is simpler to set up and has a lower cost.</li>
	<li>It is a switch with a router inside :D</li>
	<li>It uses Cisco Express Forwarding to route packets at wire speed.</li>
</ul>

## Finally, Let's talk about Multi-layer switches:

A Multilayer switch, is a switch that can perform Basic layer 2, layer 3 and up to layer 7 switching. There is a trick behind that. For a multilayer switch to perform routing function it has to have a route processor, in another words, it must have a router module or a card that collects routing table information, then updates the router's line modules or cards with that information, this means that it is gonna need an additional piece of hardware.

Multi-layer switches are capable of using Topology based Cisco Express Forwarding or Route cache (flow based) forwarding. This means that it can cache a learned route for a packet and use it later for other packets, a route once switched many concept.

Not all multi-layer switched have the two components (Switch engine and route processor)that successfully does routing. In another words, Every layer 3 switch is a Multi-layer switch (It uses CEF and can cache Information) , however, not every multi-layer switch can become a layer 3 switch  (it needs an additional Module to perform routing functions)
