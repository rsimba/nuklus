+++
title = 'JNCIS-Ent: Voice Vlan'
date = 2024-02-17T12:21:11+02:00
draft = true
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

Many IP phones have an internal switch, often referred to as a "built-in switch" or "integrated switch." This switch allows the IP phone to act as a network switch itself, typically with two ports: one for connecting to a switch, and another for connecting a computer or other network device. This setup enables a single network drop to serve both the IP phone and an additional device, reducing the need for multiple network connections at each desk or workstation. It also helps in simplifying network infrastructure and reducing cabling requirements.

A voice VLAN is a separate VLAN configured on a switch to carry voice traffic for IP Phones on a setup described on the paragraph above. This VLAN is specifically designed to prioritize and ensure the quality of voice traffic by separating voice traffic from data traffic on the network. Voice VLANs typically have higher priority settings to ensure low latency and minimal packet loss for voice data, helping to maintain call quality and reliability.

The voice VLAN enables a single access port to accept untagged data traffic as well as tagged voice traffic and associate each type of traffic with distinct and separate VLANs. By doing this, a CoS implementation can treat voice traffic differently, generally with a higher priority than common user data traffic 

In the blog post we are going to explore how to configure a voice VLAN on a Juniper switch.

# Lab Topology
As shown in the topology below, we have a PC connected to an IP phone and the IP phone connects to a Juniper switch. We have to configure the voice VLAN feature to make sure both Voice and Data traffic are received in the switch ge-0/0/6 access port.
![Voice VLAN Topology](/images/voice_vlan.jpg)

# Configuration
Essentially, there are two ways configure a voice VLAN on a Juniper switch. A dynamic option which uses LLDP-MED to dynamically provide the voice VLAN ID and 802.1p values to the attached IP phones; and a manual option that we are going to explore blow.

We have created two new VLANs to be used for voice and data traffic.
```
rsimba@acc1# show | compare
[edit vlans]
+   data {
+       vlan-id 11;
+   }
+   voice {
+       vlan-id 12;
+   }
```
We then configure the access port to accept the untagged data traffic:
```
rsimba@acc1# show | compare
[edit interfaces]
+   ge-0/0/6 {
+       unit 0 {
+           family ethernet-switching {
+               vlan {
+                   members data;
+               }
+           }
+       }
+   }
```
There three steps to configure a voice VLAN:
- Associate the VoIP parameters with all access ports.
- Associate VoIP parameters with specified access port.
- Referenced VLAN and forwarding class must be defined locally on swith.

We configure the voice VLAN feature on the same interface under the `[edit switch-options]` hierarchy level:
```
rsimba@acc1# show | compare
[edit]
+  switch-options {
+      voip {
+          interface ge-0/0/6.0 {
+              vlan voice;
+              forwarding-class assured-forwarding;
+          }
+      }
+  }
```

## Verification
We can confirm below that both voice and data VLANs are configured on both access and trunk port.
```
rsimba@acc1# run show vlans

Routing instance        VLAN name             Tag          Interfaces
default-switch          data                  11
                                                           ge-0/0/5.0*
                                                           ge-0/0/6.0*
default-switch          voice                 12
                                                           ge-0/0/5.0*
                                                           ge-0/0/6.0*
```

# Conclusion
In this blog post, we have explored how to configure a voice VLAN on a Juniper switch.
