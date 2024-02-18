+++
title = 'JNCIS-Ent: Voice Vlan'
date = 2024-02-17T12:21:11+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

Many IP phones come equipped with an internal switch, often called a "built-in switch" or "integrated switch." This feature allows the IP phone to function as its own network switch, typically offering two ports: one to connect to the main network switch and another for connecting a computer or another network device. This setup simplifies connectivity by enabling a single network drop to serve both the IP phone and an additional device, reducing the need for multiple network connections at each workstation and streamlining cabling requirements.

A voice VLAN is a specialized VLAN configured on a switch to handle voice traffic from IP phones connected to the network. It separates voice traffic from data traffic, prioritizing voice data to ensure quality and reliability. Voice VLANs are configured with higher priority settings to minimize latency and packet loss, maintaining high call quality.

By implementing a voice VLAN, a single access port can accept both untagged data traffic and tagged voice traffic, associating each type with its respective VLAN. This allows for differentiated treatment of voice traffic, typically with higher priority compared to regular user data traffic.

In our upcoming blog post, we will delve into the configuration process of a voice VLAN on a Juniper switch.

# Lab Topology
In the given topology, there is a PC connected to an IP phone, and the IP phone is connected to a Juniper switch. To ensure that both voice and data traffic are properly received on the switch's ge-0/0/6 access port, we need to configure the voice VLAN feature.
![Voice VLAN Topology](/images/voice_vlan.jpg)

# Configuration
Essentially, there are two ways to configure a voice VLAN on a Juniper switch: a dynamic option that utilizes LLDP-MED to dynamically provide the voice VLAN ID and 802.1p values to the attached IP phones, and a manual option that we will explore below.

We have created below two new VLANs to be used for voice and data traffic purposes.
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
We then configure the access port to accept untagged data traffic.
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
There are three steps to configure a voice VLAN:
1. Associate the VoIP parameters with all access ports.
2. Associate VoIP parameters with specified access ports.
3. Define referenced VLAN and forwarding class locally on the switch.

We configure the voice VLAN feature on the same interface under the `[edit switch-options]` hierarchy level.
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
We can confirm below that both the voice and data VLANs are configured on both the access and trunk ports.
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
In this blog post, we learned how to set up Voice VLANs on Juniper switches. Voice VLANs help separate voice and data traffic for better quality calls. We manually created separate voice and data VLANs and linked them to access ports. This ensures that phones get both voice and data traffic smoothly. Following these steps helps network folks improve voice communication reliability on Juniper switches.
