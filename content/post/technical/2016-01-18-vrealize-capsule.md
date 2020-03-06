---
title:  "VMware vRealize Automation Capsule"
date:   2016-01-18 15:04:23
categories: [technical]
tags: [VMware]
---
Okay, so recently I have had the chance to play around with vRealize Automation (vRA). Therefore, I am using this chance to share with you the outcomes of this interesting endeavor of mine. I have formulated this article to be in the form of questions and answers, so feel free to jump around to any question you feel like you want an answer to. The questions are:

*   What are the core components of vRA?
*   What are the logical components of vRA?
*   What are the important roles supported in vRA?
*   What are the steps for basic configuration of vRA?
*   What is vRealize Application Services?
*   What are the other topics that I didn't discuss in this capsule?

## What are the core components of vRealize Automation?

*   **vRA Server Appliance** - Acts as the web protal for end-users (for self-service)
*   **vRA Identity Appliance** - Provides SSO authentication for the environment.
*   **IaaS Server**: is "the" component, through this guy you can communicate with your cloud components via APIs
    *   _DEM workers_ - are the builders, and the doers of the tasks required in your infrastructures.
    *   _DEM Orchestrators_ - schedules tasks to be preformed by the DEM workers
    *   Agents - used for integration with external systems (e.g., vSphere)
    *   _Model Manager_ - holds the logic for communicating with external systems (e.g., SCVMM, Cisco UCS, vSphere)
    *   _Database -_ The .... database (nothing more to say sorry )
    *   _Manager Service_ – Coordinates the communication between the agents, the IaaS database, AD, and SMTP.

## What are the logical components of vRealize Automation?

*   **Fabric Groups** – defines a relationship between compute resources and their allowed access
*   **Business Groups** -an abstraction for defining the use-case for the resources (e.g., test/dev  group)
*   **End-points** – the actual reference to your compute resource provider (e.g., vCenter)
*   **Reservations** – a way to ensure that underlying resources are efficiently allocated to different parties with priorities and shares. This is done by carving out a specific portion of your compute- resources for use during provisioning activities
*   **Reservation Policies –** a container for reservations.
*   **Network Profiles:** define range of IPs  and/or configures DHCP (if needed in a lab environment)
*   **Blueprint –** defines the templates, the resources maximums and minimums and machine specific properties such as operating system, folders, etc.
*   **Entitlements** – defines what can be done with the blueprints. For example, if the blueprint is made for VM provisioning, then it specifies what action can be done on this VM.
*   **Services** - are containers for blueprints.
*   **Custom properties - properties** that allows you to customize VM deployment such as location, operation system, etc. There are many custom properties out there [1]
*   **Build Profiles –** is a grouping of one or more custom properties.
*   **Catalog Items –** are the entities available for users in self-service fashion. These entities contain attributes that the users can specify to customize his provisioning request (e.g., # of machines, CPU, memory, etc)
*   **Guest Agents** – software that installed on the guest machine that allows customization even after the machine has been deployed.
*   **Approval policies** – Allows controlled usage of resources through employing limits to services available to users (e.g., CPU>1? ask for permission first). This is really helpful to prevent resource exhaustion which might lead to problems on the long term. For an approval policy to take be populated it needs to be coupled with an entitlement.

## What are vRA important Roles?

vRA supports multi-tenancy to provide resource isolation and service access control. To do so, the vRA architecture is comprised of logical abstractions that represent the underlying physical resources, access to these resources are governed by roles. Below are the roles you will see in a vRA deployment.

*   **Tenant Roles**
    *   _Tenant Admin (TA):_
        *   Can create _business groups_ and designate a manager to each of these groups
        *   Creates approval policies, entitlements, and global blue prints.
    *   _Service Architect (SA)_**: **creates and publishes custom blue-prints in the advanced service designer.
    *   _Approval Admin (AA)_:creates and manages approval policies.

*   **System Wide Roles**
    *   _System Admin (SysAdmin):_installs vRA and creates / configures the tenants.
    *   _Infra-structure Admin:_Have additional roles such as adding end-points, manage compute resources, add fabric groups, create reservations and add reservation policies.
    *   _Fabric Admin (FA): _
        *   Manages the compute resources in the fabric-group
        *   Creates and manages reservations
        *   Creates and manages build-profiles and machine-prefixes

*   **Business Group roles**
    *   _BG Manager_
        *   creates and publishes blueprints
        *   manages entitlements and resource usage
    *   _Business User: _requests and manages services
    *   _Support User:  _requests and manages items on behalf of others
    *   _Approver: _approves catalog requests

## What are the steps for basic configuration of vRealize Automation?

*   Check the right tenant is visible
*   Configure an identity store such as Active Directory or OpenLDAP  to get your users from
*   Add your Tenant and Infrastructure Admins
*   Configure your E-mail Server from Administration
*   Configure your Infrastructure
    *   Add licensing (Standard, Advanced, or Enterprise)
    *   Create End-point credentials
    *   Add your end-points (vCenter, vRO, vCloud Directory, etc)
    *   Configure fabric-groups (Add the end-point to the fabric group)
    *   Configure a Machine Prefix (a name that will define the resources you create)
    *   Configure Network profiles and reservations.
    *   Assign Business Groups to reservations to dictate which users will use what resources.
*   Configuration your tenant attributes
    *   Start by creating a blueprint and defining limits for users (e.g., machines/user), the VM templates you are going to use for the blueprint
    *   Prepare the blueprint for provisioning by wrapping services, entitlements around it and specifying Business Groups for it .

## What is vRealize Application Services?

I think of it as a configuration management tool. It allows you to architect the applications you want to have inside your VMs. For example, I might want to run an SQLFire database inside a CentOS VM, even better, I can build mutli-tier applications (e.g., web applications with database, FE, and BE). Keep in mind that vRA application services is not part of vRA automation, it can however be integrated with vRA both ways (vRA AppServices). it is also deployed as a separate appliance with a rather lengthy configuration to have it up and running (but it is worth it).

## What are other topics I didn't have here in our humble capsule?

I am sure you didn't expect to find all the nitty-gritty details of vRA here in this capsule, vRA is a fat tool, it is comprised of many components, and can integrate with other 3rd party tools each is a story on its own.  Some of the topics I didn't discuss here with you are:

*   Integration with vReaize Orchestrator (vRO) to build custom workflows
*   Monitoring and integration with vRealize Operations Manger (vROPs)
*   Using the Advanced Service Designer with vRA
*   Reclamation of resources. Yes! vRA allows you to reclaim unused resources (neat right?)
*   vRA 7 , the "I learned from my mistakes" version of the previous vRAs
*   Deployment of vRA

## Important notes:

*   Develop the habit of checking on your compute-resources inventory every now and then, vRealize runs the “update  inventory” service once a day, so make sure to manually update the inventory when needed.
*   Before troubleshooting your vRA deployment make sure that services are running
*   vRA appliance services are registered (and complete)
*   IaaS services are visible from IaaS server.
*   Make sure to create a service with the right entitlements for each blueprint you publish for use.

If you are here already, I know what you want to say , say it!! I know this was boring because it had no pictures in it, I was lazy ... but, In my follow up article(s), I will be having a lot of colors and visuals for you, or even better, I will be updating this boring (black and white) piece of boring text to have a lot of figures. I hope this helps!

**References**

- **[1]** [Check out vRA Custom Properties](http://pubs.vmware.com/vra-62/topic/com.vmware.ICbase/PDF/vrealize-automation-62-custom-properties.pdf)
- **[2]** [vRA Documentation (Feast yer Eyes)](https://www.vmware.com/support/pubs/vrealize-automation-pubs.html)
