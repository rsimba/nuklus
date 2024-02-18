+++
title = 'JNCIS-Ent: Native Vlan'
date = 2024-02-17T12:20:46+02:00
draft = true
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

As discussed [here](https://www.nuklus.com/posts/jncis-ent-layer-2-switching/#trunking-vlan-tagging), a trunk port carry traffic for multiple VLANs and this is possible because of the Frame Identification or tagging which is the assignment of a VLAN ID to each frame that transverse the trunk port. The switch on the other end of the link examines the VLAN ID on the frame and places the frame into the correct VLAN.

For example, below is a Wireshark capture from **acc2** switch trunk port **ge-0/0/5**. We see in the capture that Host4 sends an ICMP Request to Host2 and when the frame reaches **acc2** switch trunk port it is tagged with VLAN 20.
![Wireshark Capture](/images/host2_4_w_capture.jpg)

Two terms are used to refer how frames are handled when traversing a trunk port:
- **Tagged**: A frame that has a VLAN ID in its header is called a tagged frame and it helps switches understand which VLAN the frame belongs to when it arrives at its destination.
- **Untagged**: A frame without a VLAN ID in its header is called an untagged frame.

Access ports only accept **untagged** frames and trunk ports by default only accept **tagged** frames. However, you can configure a trunk port to carry **untagged** traffic when configured with the `native-vlan-id` configuration option. This option must be enabled on all trunk ports expected to pass untagged traffic.

By default all untagged traffic is place into the **default** VLAN which uses the 802.1Q Tag of **1**:
```
rsimba@acc1# run show vlans

Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1
```
# Lab Topology
In the lab topology below, both Host1 and Host2 are are connected to different switches and are members of VLAN 1, the untagged VLAN. Both **acc1** and **acc2** have to be configured in order to send and receive untagged frames.
![Untagged Traffic](/images/untagged_traffic.jpg)

# Configuration
Before configuring a native vlan we need to make sure that the interface ge-0/0/0 on both switches are now associated with the default VLAN:
```
rsimba@acc1# run show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1
                                                           ge-0/0/0.0*
default-switch          vl10                  10
                                                           ge-0/0/5.0*
default-switch          vl20                  20
                                                           ge-0/0/1.0*
                                                           ge-0/0/5.0*
```
The native vlan configuration is specific to switch's trunk ports:
```
rsimba@acc1# show | compare
[edit interfaces ge-0/0/5 unit 0 family ethernet-switching vlan]
+       members [ vl10 vl20 default ];
```
```
rsimba@acc1# run show vlans

Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1
                                                           ge-0/0/0.0*
                                                           ge-0/0/5.0*
default-switch          vl10                  10
                                                           ge-0/0/5.0*
default-switch          vl20                  20
                                                           ge-0/0/1.0*
                                                           ge-0/0/5.0*
```
We can see below that Host3 is able to ping Host1 which are on the same VLAN, the untagged VLAN or the default VLAN (VLAN 1):
```
Host3> ping 192.168.10.1
84 bytes from 192.168.10.1 icmp_seq=1 ttl=64 time=3.014 ms
84 bytes from 192.168.10.1 icmp_seq=2 ttl=64 time=2.408 ms
^C
```
We can also confirm from the capture below that the frame sent by Host3 to Host1 is being tagged with VLAN 1.
![Capture Untagged](/images/capture_untagged.jpg)

# Conclusion
In this blog post we have seen how to configure and verify a native VLAN on a Juniper switch.
