+++
title = 'JNCIS-Ent: Native VLAN'
date = 2024-02-17T12:20:46+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

As discussed [here](https://www.nuklus.com/posts/jncis-ent-layer-2-switching/#trunking-vlan-tagging), a trunk port carries traffic for multiple VLANs, made possible by frame identification or tagging. Each frame passing through the trunk port is assigned a VLAN ID. The receiving switch examines this ID and places the frame into the corresponding VLAN. For example, here is a Wireshark capture from the trunk port **ge-0/0/5** on **acc2** switch. In the capture, we observe Host4 sending an ICMP Request to Host2. When the frame reaches the trunk port on **acc2** switch, it is tagged with VLAN 20.
![Wireshark Capture](/images/host2_4_w_capture.jpg)

Two terms are used to describe how frames are handled when traveling across a trunk port:
- **Tagged**: A frame with a VLAN ID in its header is termed a tagged frame. This tagging helps switches identify the VLAN to which the frame belongs when it reaches its destination.
- **Untagged**: A frame without a VLAN ID in its header is referred to as an untagged frame.

Access ports typically only accept **untagged** frames, while trunk ports by default accept **tagged** frames. However, you can configure a trunk port to handle **untagged** traffic by enabling the `native-vlan-id` option. This setting must be enabled on all trunk ports that are expected to pass untagged traffic. By default all untagged traffic is place into the **default** VLAN which uses the 802.1Q Tag of **1**:
```
rsimba@acc1# run show vlans

Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1
```
# Lab Topology
In the lab setup below, both Host1 and Host2 are connected to different switches and belong to VLAN 1, which is the untagged VLAN. Both **acc1** and **acc2** switches need to be configured to send and receive untagged frames.
![Untagged Traffic](/images/untagged_traffic.jpg)

# Configuration
Before configuring a native VLAN, we need to ensure that interface ge-0/0/0 on both switches is now associated with the default VLAN:
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
Next, we add the native VLAN to the switch's trunk ports:
```
rsimba@acc1# show | compare
[edit interfaces ge-0/0/5 unit 0 family ethernet-switching vlan]
+       members [ vl10 vl20 default ];
```

# Verification
With the configuration above, we can verify that the native VLAN is now associated with both access and trunk ports.
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
Host3 is now able to ping Host1, which are on the same untagged VLAN or the default VLAN (VLAN 1):
```
Host3> ping 192.168.10.1
84 bytes from 192.168.10.1 icmp_seq=1 ttl=64 time=3.014 ms
84 bytes from 192.168.10.1 icmp_seq=2 ttl=64 time=2.408 ms
^C
```
We can also confirm from the capture below that the frame sent by Host3 to Host1 is being tagged with VLAN 1.
![Capture Untagged](/images/capture_untagged.jpg)

# Conclusion
In this post, we delved into VLANs and trunk ports on Juniper switches. Trunk ports carry traffic for multiple VLANs, aided by frame tagging. We configured native VLANs on trunk ports for seamless communication between devices in the same VLAN across switches. Understanding these concepts is key for network efficiency and connectivity.
