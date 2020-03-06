---
title:  "Okay, You Know About Subnetting, But Why Do It?"
date:   2011-09-03 15:04:23
categories: [technical]
tags: [Networking, Cisco]
---

Long time I had this question in my head, why do we subnet, yea I am being a smart guy, I am very good at sub-netting but I didn’t know why it is so crucial.

Well there are many answers to that question, the most general ones are:

- Reduced network traffic
- Optimized network performance
- Simplified management
- Facilitated spanning of large geographical distances.

Okay, that’s cool but still, How does it do these “bla bla written above” =D ??. Okay, While I was searching on-line on my beloved Internet, I have found two of the answers that turned my head lights on =D. Here are they:

“The classic answer talks about a company where they have been given a range, but want to separate departments so they don’t interfere with each other.

You would use subnets to achieve this separation. This also allows security separation, where general office people can’t get to payroll machines for example.

~Harry”

“Also, to add to what Harry said – broadcasts are an important reason to subnet. Any ethernet network (or other technology) has various technical limits to the size of the network. A significant limit is broadcast traffic. Since broadcast traffic will be seen by every node on the network, and must be processed by every node, then excessive broadcast traffic will choke a network. Since there are many kinds of broadcast traffic (arp, bootp, netbios, multicast, etc) that are needed for the network to function, a large network can produce enough broadcast traffic to degrade performance.One solution is to subnet into smaller networks, connected by routers, which block broadcast traffic. For example, imagine a network of 10000 hosts – there might be 5 thousand broadcasts per second, chewing up transmission slots on the ethernet network and forcing hosts to process large amounts of uneeded traffic.

Modern networks and computers can handle much more broadcast traffic than old 10 Mbps ethernet, but even so it is generally a good practice to limit the size of broadcast domains in networks.

~ Spice_Weasel”

I hope it helps you to 😉
