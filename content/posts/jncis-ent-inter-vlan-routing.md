+++
title = 'JNCIS-Ent: Inter-VLAN Routing'
date = 2024-02-17T05:37:01+02:00
draft = true
author = 'Ricardo Simba'
categories = ['jncis-ent', 'switching']
+++

VLANs are used to logically segment a network into multiple broadcast domains, but by default, devices within different VLANs cannot communicate with each other directly. Inter-VLAN routing allows communication between devices in different VLANs by routing traffic between them using a router or a layer 3 switch.

Juniper switches support inter-VLAN routing using the **irb** interface, which is a virtual interface that acts as the default gateway for devices in VLAN. This blog post will show you it is implemented.

# Lab Topology
In the topology below we have Host1 configured in VLAN 10 and point to **acc1** switch as its gateway.
![IRB Topology](/images/irg.jgp)

# Configuration
Define the IRB interface:
```
rsimba@acc1# show interfaces irb
unit 10 {
    family inet {
        address 192.168.10.254/24;
    }
}
```
Associate the IRB with the VLAN:
```
rsimba@acc1# show | compare
[edit vlans vl10]
+   l3-interface irb.10;
```
## Verification
```
rsimba@acc1# run show interfaces terse irb
Interface               Admin Link Proto    Local                 Remote
irb                     up    up
irb.10                  up    up   inet     192.168.10.254/24
                                   multiservice
```
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
```
Host1> ping 192.168.10.254
84 bytes from 192.168.10.254 icmp_seq=1 ttl=64 time=22.967 ms
84 bytes from 192.168.10.254 icmp_seq=2 ttl=64 time=0.806 ms
^C
```
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
In this blog post, we have seen how to configure inter-VLAN routing on Juniper switches using the **irb** interface.

