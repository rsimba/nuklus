+++
title = 'JNCIS-Ent: Configuring STP'
date = 2024-02-20T07:48:14+02:00
draft = true
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

As discussed [here](https://www.nuklus.com/posts/jncis-ent-layer-2-switching/#stp), STP is a Layer 2 protocol that prevents loops and calculates the best path through a switched network that contains redundant paths. When a topology changes, STP automatically rebuilds the tree.

At the highest level, there are four main steps that STP takes to prevent loops:
1. Switches exchange BPDUs.
2. Switches elect a Root Bridge.
3. Port role and state election.
4. Tree is fully converged.

There are two types of BPDU:
- **Configuration BPDU**: Determine the tree topology of a LAN. STP uses the information that the Configuration BPDU provide to elect a Root Bridge, to identify Root Ports for each switch, to identify Designated Ports for each LAN segment, and prune specific redundant links to create a loop-free tree topology.
- **Topology Change Notification** (**TCN**) **BPDU**: Used to report topology changes within a switched network.

There are newer and different version of STP which includes enhancements and improvements over the original STP. These includes: RSTP, VSTP, and MSTP. In this blog post we will configure RSTP on switches running Junos OS.

# Lab Topology
As prerequisite, the links connecting the switches are configured as trunk, and obviously the interfaces connecting the Hosts on each switch are configured as access ports.
![STP Topology](/images/stp.jpg)

# Configuration
As seen the output bellow, Junos switches don't come with STP enabled by default:
```
rsimba@acc1> show spanning-tree bridge
Spanning-tree is not enabled at global level.
```
There are two ways to enable RSTP on switch running Junos OS: on a interface basis or globally by using the `all` keyword. The Configuration is done under the `[edit protocols rstp]` hierarchy:
```
rsimba@acc1# set protocols rstp interface ?
Possible completions:
  <name>
  ge-0/0/0
  all                  All interfaces
```
As soon RSTP is enabled, it assumes the following default settings for the Root Bridge:
```
rsimba@acc1# run show spanning-tree bridge
STP bridge parameters
Routing instance name               : GLOBAL
Context ID                          : 0
Enabled protocol                    : RSTP
  Root ID                           : 32768.2c:6b:f5:4a:fc:d0
  Hello time                        : 2 seconds
  Maximum age                       : 20 seconds
  Forward delay                     : 15 seconds
  Message age                       : 0
  Number of topology changes        : 0
  Local parameters
    Bridge ID                       : 32768.2c:6b:f5:4a:fc:d0
    Extended system ID              : 0
```
As well as the following default settings for the interfaces:
```
rsimba@acc1# run show spanning-tree interface
Spanning tree interface parameters for instance 0
Interface                  Port ID    Designated         Designated         Port    State  Role
ge-0/0/0                     128:1        128:1  32768.2c6bf54afcd0        20000    FWD    DESG
ge-0/0/1                     128:2        128:2  32768.2c6bf54afcd0        20000    FWD    DESG
ge-0/0/5                     128:3        128:3  32768.2c6bf54afcd0        20000    FWD    DESG
ge-0/0/6                     128:4        128:4  32768.2c6bf54afcd0        20000    FWD    DESG
```
