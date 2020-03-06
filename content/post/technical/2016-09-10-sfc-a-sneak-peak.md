---
title:  "Neutron-SFC: A Sneak-Peak"
date:   2016-09-10 15:04:23
categories: [technical]
tags: [OpenStack, NFV]
---

In today's virtual network architecture, NFV is paving the way to become to defacto standard for providing network services in the cloud. NFV relies on Service Function Chaining (SFC), to stitch services together to provide a service-path for packets to traverse.

OpenStack, one of the more successful open cloud platforms is considered a good base to start building services for NFV related functions.  In this post, we will be having a sneak-peak over the components exposed by **neutron-sfc**, the service chaining API exposed by OpenStack.

There are several parts involved for the creation of service chain API:

- Port-Pair
- Port-Pair Group
- Flow calssifier
- Port-Chain


### A Port-pair
A port pair is created for each service function

options allowed when created a service function port-pair

```bash
neutron port-pair-create [-h]
        [--description <description>]
        --ingress <port-id>
        --egress <port-id>
        # example, the encapsulation mechanism for service function parameters
        [--service-function-parameters <parameter>] PORT-PAIR-NAME
```

### Port-Pair group
To group VMs with the same network function together for load-distribution purposes for example. A port-pair-group can have more than one port-pair created within (e.g., multiple VMs doing the same function).

```bash
neutron port-pair-group-create [-h]
        [--description <description>]
        --port-pairs <port-pair-id>
        [--port-pair-group-parameters <parameter>] PORT-PAIR-GROUP-NAME
```

### Flow-Classifier
We can use any of the following options to specify a flow for a chain. i.e., all the traffic matching a flow-classifier needs to go through a chain.

```bash
neutron flow-classifier-create [-h]
        [--description <description>]
        [--protocol <protocol>]
        [--ethertype <Ethertype>]
        [--source-port <Minimum source protocol port>:<Maximum source protocol port>]
        [--destination-port <Minimum destination protocol port>:<Maximum destination protocol port>]
        [--source-ip-prefix <Source IP prefix>]
        [--destination-ip-prefix <Destination IP prefix>]
        [--logical-source-port <Neutron source port>]
        [--logical-destination-port <Neutron destination port>]
        [--l7-parameters <L7 parameter>] FLOW-CLASSIFIER-NAME
```

### Port-Chain

You can associate flow-classifiers to port chains if you want them to be active. Otherwise, the port-chain will not be active.

```bash
neutron port-chain-create [-h]
        [--description <description>]
         --port-pair-group <port-pair-group-id>
        [--flow-classifier <classifier-id>]
        [--chain-parameters <chain-parameter>] PORT-CHAIN-NAME
```

### Encapsulation of a service chain
MPLS labels are leaverged to support chain IDs.  This MPLS has noting to do with the MPLS transport because OVS does not support Network Service Channel Header (NSH) header.


### Final Notes
Eventually, having defined the above constructs, you should be able to start pushing packets across your service chain. It is important to take into consideration that SFC requires ```ip forwarding``` as each network functions acts as a middle-box forwarding packets to other functions.

For more information check-out the below references:

- [Source Code](http://git.openstack.org/cgit/openstack/networking-sfc)
- [SFC Overview](https://launchpad.net/networking-sfc)
- [Wiki](https://wiki.openstack.org/wiki/Neutron/ServiceInsertionAndChaining)
