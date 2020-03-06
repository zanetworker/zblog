---
title:  "CloudArray: A Seamless Portal to the Cloud"
date:   2016-02-09 15:04:23
categories: [technical]
tags: [Storage]
---

The formal definition for CloudArray is:

“EMC-Cloud Array is a data storage software technology that easily integrates cloud (object) storage into traditional enterprise IT environments”

I am not sure if the definition really reflects what CloudArray does, CloudArray can be considered as a Cloud-Gateway, the reason I think of it this way is because it acts as a middle-man between Applications that talk standard SAN/NAS protocols (iSCSI, NFS, CIFS, and SMB) and public or private cloud providers (e.g., Amazon EC2, OpenStack, ECS, Atmos, Google Cloud, etc.) where most of them are Object-storage based.  CloudArray presents Object storage as local-disks to your hosts and applications.
<!--more-->
<img class="alignnone size-full wp-image-335" src="https://adelzalok.files.wordpress.com/2015/11/cloudarray.png" alt="CloudArray" width="1268" height="590" />

<strong>What are the CloudArray components? </strong>

The CloudArray architecture is very simple and intuitive. There basically three things you need to understand about the architecture:
<ul>
	<li>The Protocols</li>
	<li>The Cache</li>
	<li>The Cloud Provider</li>
</ul>
CloudArray supports SAN and NAS protocols (iSCSI / NFS / CIFS / SMB) that can be used to talk to the cache. The cache is a near-distance storage that is used to maintain a copy of your most used data, instead of going all the way to access your replicated data from the cloud, you are provided with a faster access portal through the cache. The cache is a block-storage device that either runs on your existing block storage (e.g., VNX, VMAX) or is provided to you when you purchase the physical appliance (keep reading to find out about the different flavors of CloudArray). The final piece of the puzzle is the cloud provider, this can be one of many supported providers public and private (e.g., OpenStack, ECS, Atmos, Amazon, Google, etc.). This is where your data resides, the cache receives the data from the applications, compresses, encrypts, and send to the cloud.

To start using CloudArray,  you need to have a cache configured and tied up in a pair with one of the cloud providers, this way when the data is written to the cache, it gets replicated to the cloud provider in the pair, another name for this massive bunch of words is “Provisioning Policies”

 

<strong>What is featured by Cloud Array? </strong>

Thank you for asking this. I almost forgot.

CloudArray supports:
<ul>
	<li>In-Cloud Snapshots (you can roll back to points in time if you corrupted your data)</li>
	<li>Compression (data is compressed before it is sent to the cloud)</li>
	<li>Encryption (data is encrypted by an AES-256 bit key, quite secure I would say)</li>
	<li>Dynamic Cache (expand or shrink your cache)</li>
	<li>Bandwidth throttle (CloudArray is bandwidth hungry, don’t let it eat up all your BW)</li>
	<li>Configuration backup (destroyed your CloudArray? Don’t worry it is shortly fixable)</li>
</ul>
<strong>Why would you use CloudArray? </strong>

Mid-tier storage arrays such as VNX are usually used for increased performance and for fast data access.  Furthermore, it uses block storage and somehow highly priced disks. Now the question is, do you really want to utilize these disks for use by mission-critical data or do you want to waste this valuable storage on cold-data that you could off-load somewhere else? I don’t think you chose the latter, and that’s why CloudArray makes sense to use in this case. It maintain a good performance by using an internal cache to store your most recently used data and replicates to cheap storage on the cloud (whether public or private).

Another reason to use CloudArray is to consolidate your storage in one place.  Most of the hype today is all about how to simplify your infrastructure management, whether it is your network, storage, or compute, and when it comes to storage, I consider CloudArray to be one of the simplified storage management pillars.  In the usual case, you would have a separate NAS server for each of your remote Sites, each of which requires maintenance and management, and we all know what happens when there are many devices with many people managing those … nothing good, only trouble. With CloudArray however, there is only your cloud storage and you are not even managing that, it is already taken care of for you!!

<img class="alignnone size-full wp-image-336" src="https://adelzalok.files.wordpress.com/2015/11/post.png" alt="Post" width="974" height="383" />

When you are confused about where to put your cold data and still want to maintain an acceptable level of performance, CloudArray pops up out of nowhere and puts a hand on your shoulder with a reassuring smile telling you, <strong>I got your back.</strong>

 

<strong>How can you deploy CloudArray? </strong>

CloudArray comes in two flavors, a physical appliance and a virtual one.  Now, each has its pros and cons, it all depends on how you want to use it and what type of a person you are.  If you want to set and forget then the physical appliance is better for you, everything is already pre-configured, the cache, the RAM, and all the annoying variables, you are basically good to go. Howeverrr, you trade of your flexibility, with the physical appliance, the storage type / RAID is already chosen for you, you cannot tweak performance and play around with storage tiers , the fate  of your cache performance has already been determined. Furthermore, your storage size is also a final static value, if you are looking for extensions;  you need a new appliance.

Don’t get me wrong here, I am not saying that the virtual appliance is a better fit, again, it depends. If you want freedom in extending your storage capacity, in tweaking the performance based on the application you are running. A lighter weight and better fit for you would be the virtual appliance.

At the end, you are the master of your choice, the blue pill or the red pill; it is all about perspective ;)

 

I hope this helps J
