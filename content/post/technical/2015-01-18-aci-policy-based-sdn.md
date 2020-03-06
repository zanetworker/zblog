---
title:  "ACI Policy-based SDN: A Morning Capsule"
date:   2015-01-18 15:04:23
categories: [technical]
tags: [ACI, Cisco]
---

IT Manager, Ops, and network administrators suffer today from the complex approaches being followed towards network operations which gives no space for innovation. Additionally, the current box-by-box approaches for configuration and provision can be cumbersome and lack agility to turn up new services quickly to meet demands and Service-Level Agreements (SLA) of internal customers. Adopting a new approach towards network automation is key to addressing these challenges, by automating network operation not only do we reduce operational and management expenses but we also provide investment protection as we adopt the application economy (a software approach that grows much faster).

SDN startled a revolution in the networking community, making network management, control and configuration a lot easier with the use of software-based tools. Briefly, SDN is about network programmability and automation. However, the old version of SDN (using OpenFlow) completely decoupled the control and data-plane, leaving the controller out alone to handle all the complex control logic, this solution was considered fine in small network environments but it would not scale in large network environments. Therefore, there are a lot of efforts towards making SDN more scalable and efficient, thus leading to simple, automated network ecosystem that can scale. Cisco presents the Application Centric Infrastructure (ACI) architecture as one of the leading efforts for taking SDN to the next level. <b>What is Cisco ACI?</b> Cisco’s ACI is an innovative, pretentiously secure architecture that allows for centralized, application-driven policy automation and management. Cisco’s ACI provides increased visibility in physical and virtual Networks. Furthermore, it gives IT leaders the ability to manage complex private, public and hybrid environments, it can also lower operational overhead and deliver higher service levels. The main building blocks of Cisco’s ACI architecture are:
<ul>
	<li>Application-focused fabric</li>
	<li>Centralized management, automation, and orchestration</li>
	<li>Extensibility and openness through open source and open APIs</li>
</ul>
<b>What are the use-cases of Cisco’s ACI architecture?</b> Using Cisco’s ACI architecture many services will be possible, including but not limited to:
<ul>
	<li><b>Security</b> - automate thread detection and prevention by making use of the dynamic policy-based network control.</li>
	<li><b>Path Optimization: </b>automated WAN optimization through Performance Routing (PfR) configuration and compliance assurance.</li>
	<li><b>Automated Compliance: </b>Using Cisco APIs it will be possible to pull audit logs from the cisco Application Policy Infrastructure controller (APIC) and create compliance reports autonomically (e.g. PCI compliance reports).</li>
	<li><b>QoS: </b>compliance assurance, QoS policy, Follow-Me QoS - Automated QoS for user mobility, etc</li>
</ul>
<b>What are the main components of the ACI Architecture ?</b> The Cisco ACI architecture is comprised of the following components:
<ul>
	<li>Cisco Application Policy Infrastructure Controller (APIC)</li>
	<li>Cisco Nexus 9000 Series Switches (Cisco ACI spine and leaf switches)</li>
	<li>Cisco ACI ecosystem</li>
</ul>
The Cisco APIC controller is the policy maker, and distributor, it has a network wide view and can communicate with the Nexus 9000 switches (or virtual switches) to disseminate policies across the network. In addition to the APIC controller, Cisco Nexus 9000 switches support the ACI architecture and work in conjunction with the APIC controller. Nexus 9000 switches can operate in NX-OS or in ACI mode for backward compatibility. The switches can be used as spine (main traffic carriers) or leaf (interfacing with end-points) switches. The Cisco ACI framework allows for a broad integration and interoperability with management, virtualization and layer 4 through 7 services from a diverse set of vendors.

Although there are not yet many deployments of ACI, Cisco promises that by 2015 ACI will be receive a much higher adoption rate. Well, for me I do see competition so Cisco needs to work very hard. In my future posts, I will be explaining other variants of SDN 2.0 that will be facing off Cisco's ACI in 2015.
