---
title:  "Anatomy of a MAC Address, BPDU, BID, and 802.1Q Ethernet Frame Tag"
date:   2011-09-29 15:04:23
categories: [technical]
tags: [Networking, Cisco]
---


As a network engineer you must have skimmed through millions of acronyms and abbreviations like MAC, IP, FTP, ..... and so on, they are un-countable :D. Sometimes these abbreviations mix them into a cocktail in my head, so Today I thought of re-visiting some of these abbreviations and concepts that I consider fairly important if you are considering to understand switching. So let's start of by defining each one of these abbreviations.

<strong>Medium Access Control Adress  (MAC address): </strong>

Basically, A MAC address is a unique Identifier that is assigned to Network Interface Cards for communication on the network segment. MAC addresses are unique, they are burned into the network interface cards (Stored in the Network interface card hardware, for example: ROM). MAC addresses are 48 bits or 6 bytes.

<a href="http://adelzalok.files.wordpress.com/2011/09/mac-address1.gif"><img class="alignleft size-full wp-image-71" title="mac-address" src="http://adelzalok.files.wordpress.com/2011/09/mac-address1.gif" alt="" width="344" height="89" /></a>

As we can see from the figure, the first three bytes are assigned by the IEEE and they define the vendor.  So  you could copy the first three bytes and search into a database of vendors and for sure you will find your guy. Within those first three bytes you will find tow bits  the <em>I/G and the G/L</em> that could be assigned either 1 or 0. The <em>I/G </em>field means that this mac address could be for an <strong>I</strong>ndividual or for a <strong>G</strong>roup, this means that it could be a unicast (for a single machine) or a (multi-cast/broadcast) for more than one machine. The other <em>G/L  </em>parameter basically means that this MAC address could be <strong>G</strong>lobally administered ( Assigned to the NIC by the vendor) or <strong>L</strong>ocally administered (Assigned to the device by the network administrator, which overrides the original values).

<strong>Bridge Protocol Data Units (BPDUs)</strong>

Before defining what is a BPDU, lets is first define what is the <em>Spanning Tree Protocol (STP). </em>Simply, the spanning tree protocol is a protocol that helps eliminate loops from our network and allowing for link redundancy without loop problems. The STP protocol uses  BPDUs to exchange protocol related information.

BPDUs are frames exchanged between bridges/switches  that include information that helps in deciding the Root bridge and the role of each switch port.

<a href="http://adelzalok.files.wordpress.com/2011/09/stp1.jpg"><img class="alignleft size-medium wp-image-74" title="stp" src="http://adelzalok.files.wordpress.com/2011/09/stp1.jpg?w=300" alt="" width="300" height="256" /></a>

As we can see from the figure, the BPDU consists of many parameters, the first 4 parameters Identify the STP version, type and status. the second 4 parameters define the root and the cost to the root. And the last 4 parameters, determine how often the BPDUs are sent, for how long the info is retained and when to declare a root as not functioning.

One of the most Important parameters in the BPDU is the Bridge ID.

<strong>What is the Bridge ID ? </strong>

A bridge ID is used for root bridge, Designated port elections.
The Original Bridge ID consisted of  Two parameters:
<ul>
	<li>A 2 bytes Bridge Priority field.</li>
	<li>A  6 bytes MAC address as described above</li>
</ul>
But, because of the different STP variants that might have multiple VLAN Instance, A new parameter is introduced, this parameter is the Extended System ID  which Identifies the VLAN instance that we are working with.
<a href="http://adelzalok.files.wordpress.com/2011/09/071110214916.jpg"><img class=" size-medium wp-image-76 aligncenter" title="071110214916" src="http://adelzalok.files.wordpress.com/2011/09/071110214916.jpg?w=300" alt="" width="300" height="240" /></a>

By adding these extra system ID parameters we divide the original 2 Bytes bridge priority into a 4 bits Bridge priority + a 12 bits VLAN ID.

<strong> The 802.1q Tag</strong>

As we know there is two types of trunk encapsulation:
<ul>
	<li><strong>Inter-Switch Link (ISL):</strong> This is a Cisco proprietary pre-standard method that encapsulates the whole Ethernet or the
802.3 frame in a header that contains the VLAN information.</li>
	<li><em><strong>802.1q:</strong> is an IEEE standard that simply a tag to the Ethernet or 802.3 frame, this Tag contains information about the VLANs.</em></li>
</ul>
The ISL version is deprecated and most probably no longer used. So, let's have a closer look at the 802.1q Tag withing the Ethernet frame.
<a href="http://adelzalok.files.wordpress.com/2011/09/figure-13-ieee-8021q-vlan1.png"><img class=" size-full wp-image-79 aligncenter" title="figure-13-ieee-8021q-vlan" src="http://adelzalok.files.wordpress.com/2011/09/figure-13-ieee-8021q-vlan1.png" alt="" width="503" height="263" /></a>As we can see from the figure, the tag is inserted after the preamble, the DA and the SA. this tag consists of 4 parameters:
<ul>
	<li><strong><em>Tag Protocol ID</em></strong> : In that case we have 0x8100 which states that we are using 802.1q.</li>
	<li><strong><em>The 802.1p priority User priority or Class of Service (CoS)</em> </strong>: this is used for applying QoS.</li>
	<li><strong><em>Canonical Format Indicator (CFI)</em>:</strong> A 1 bit field, if it has the value of 0 then is is in a canonical format and isused for Ethernet protocol. On the hand if this value is 1 then the frame is not in it's canonical format and it could be used for Token Ring protocol, so it is backward comparable.</li>
	<li><strong><em>VLAN  ID</em></strong>: This identifies which the VLAN that this frame is advertised for.</li>
</ul>
I hope this helps,
Have a good day :).
