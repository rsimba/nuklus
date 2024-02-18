+++
title = 'JNCIS-Ent: Inter-VLAN Routing'
date = 2024-02-17T05:37:01+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

VLANs segment a network logically, creating separate broadcast domains. By default, devices in different VLANs can't communicate. Inter-VLAN routing enables this communication by routing traffic between VLANs using a router or layer 3 switch.

Juniper switches utilize the **irb** interface for inter-VLAN routing, serving as the default gateway for VLAN devices. This post will demonstrate how to implement it.

# Lab Topology
In the network topology, we need to configure Host1 to be in VLAN 10 with its gateway set to the **acc1** switch.
![IRB Topology](/images/irb.jgp)

# Configuration
There are two main steps to configure inter-VLAN routing on Juniper switches:
1. Defining the IRB interface:
```
rsimba@acc1# show interfaces irb
unit 10 {
    family inet {
        address 192.168.10.254/24;
    }
}
```
2. Associating the IRB with the VLAN:
```
rsimba@acc1# show | compare
[edit vlans vl10]
+   l3-interface irb.10;
```
## Verification
We can confirm below that the IRB interface is up and the VLAN is associated with it.
```
rsimba@acc1# run show interfaces terse irb
Interface               Admin Link Proto    Local                 Remote
irb                     up    up
irb.10                  up    up   inet     192.168.10.254/24
                                   multiservice
```
We can also confirm the prefix is in the routing table.
```
rsimba@acc1# run show route 192.168/16

inet.0: 2 destinations, 2 routes (2 active, 0 holddown, 0 hidden)
Limit/Threshold: 1048576/1048576 destinations
+ = Active Route, - = Last Active, * = Both

192.168.10.0/24    *[Direct/0] 00:03:53
                    >  via irb.10
192.168.10.254/32  *[Local/0] 00:03:53
                       Local via irb.10
```
Host1 is now able to ping the gateway:
```
Host1> ping 192.168.10.254
84 bytes from 192.168.10.254 icmp_seq=1 ttl=64 time=22.967 ms
84 bytes from 192.168.10.254 icmp_seq=2 ttl=64 time=0.806 ms
^C
```
We can also verify the MAC address of Host1 in the **acc1** MAC table.
```
rsimba@acc1# run show ethernet-switching table vlan-name vl10

MAC flags (S - static MAC, D - dynamic MAC, L - locally learned, P - Persistent static, C - Control MAC
           SE - statistics enabled, NM - non configured MAC, R - remote PE MAC, O - ovsdb MAC
           GBP - group based policy, B - Blocked MAC)

Ethernet switching table : 1 entries, 1 learned
Routing instance : default-switch
    Vlan                MAC                 MAC         Age   GBP     Logical                NH        RTR
    name                address             flags             Tag     interface              Index     ID
    vl10                00:50:79:66:68:06   D             -            ge-0/0/0.0             0         0
```
# Conclusion
In this blog post, we explored the concept of VLANs and their role in network segmentation. We also discussed how inter-VLAN routing enables communication between devices in different VLANs. Specifically, we looked at how Juniper switches support inter-VLAN routing using the **irb** interface. By configuring Host1 to be in VLAN 10 with its gateway set to the **acc1** switch, we can facilitate communication across VLANs. This setup demonstrates the practical implementation of inter-VLAN routing on Juniper switches, enhancing network connectivity and flexibility.

