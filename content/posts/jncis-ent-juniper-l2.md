+++
title = 'JNCIS-Ent: Juniper Switches and OS'
date = 2024-02-09T05:33:04+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

Juniper made its debut in the world of switches back in 2008 when it introduced the EX Series switches. Since then, it has become a significant player in the networking arena. Whether you're starting out in network certification or just improving your technical skills, understanding Juniper switches and their operating system is essential. In this post, we'll explore the basics of Juniper's switch lineup and make their operating system easier to understand. So, if you're curious about how Juniper switches work, you're in the right place. Let's get started!

## The EX Switches
The EX Series switches are Juniper's lineup of Ethernet switches designed for various network layers, including access, aggregation, and core functions. These switches come in both fixed and modular configurations, offering flexibility in port density, speed, and redundancy features, both in hardware and software. It's worth noting that support for Layer 2 switching features may vary across different platforms.

Similar to other Juniper devices, the EX switches follow the design philosophy of cleanly separating the control and forwarding planes. This ensures stability and efficiency, with the control plane managed by the RE and the forwarding plane handled by the ASIC-based PFE, which swiftly forwards frames or packets.

## Junos OS for Layer 2 Switching
Junos OS is known for its modularity, which improves stability and reliability. This modularity is achieved through software daemons operating within protected memory spaces. Stability is further ensured by well-tested code and the use of the FreeBSD and Linux kernel. On top of this kernel, various software processes provide Junos services and user interfaces for configuration and monitoring.

The EX platforms run on a specialized version of Junos OS designed for both Layer 2 switching and Layer 3 routing. This unified software package seamlessly combines Layer 2 and Layer 3 switching and routing functions.

## Junos Frame Processing
Junos OS efficiently handles frame processing by utilizing both the control and forwarding planes. Let's explore how it manages different scenarios.

### Handling Unknown Source MAC Addresses
When a frame with an unknown source MAC address arrives, Junos OS takes the following steps:
1. The frame enters the ingress port and connects to the ingress PFE.
2. The ingress PFE checks the MAC address and forwards header information to the RE if the source MAC is unknown.
3. The RE decides whether to add or discard the MAC address, with any new entries programmed into all PFEs.

### Handling Known Destination MAC Addresses
For frames with known destination MAC addresses, Junos OS follows these steps:
1. The frame enters the ingress port and connects to the ingress PFE.
2. The ingress PFE checks the MAC address and forwards the frame to the egress PFE and port.
3. The egress PFE sends the frame out of the egress port toward its destination.

### Handling Unknown Destination MAC Addresses
When faced with frames containing unknown destination MAC addresses, Junos OS proceeds as follows:
1. The frame enters the port and connects to the ingress PFE.
2. If no entry exists, the ingress PFE duplicates the frame to other PFEs and all local ports within the same broadcast domain (VLAN).
3. All other PFEs replicate and forward the frames out of all egress ports within the same broadcast domain.

### Processing Routed Packets
For packets destined to the switch's MAC address, Junos OS performs the following actions:
1. The frame enters the ingress port and connects to the ingress PFE.
2. The ingress PFE checks the MAC address and conducts a Layer 3 lookup.
3. Depending on the destination IP address, the packet is sent to the RE or forwarded to the egress PFE.
4. The egress PFE forwards the packet out of the egress port toward its destination.

# Conclusion
Understanding Juniper switches and their operating system is essential for anyone entering the world of networking. With their strong design, flexible architecture, and effective frame processing, Juniper switches provide a reliable basis for creating and overseeing modern networks. Whether you're an experienced network professional or a new technician, exploring Juniper's switch lineup and Junos OS can lead to a better grasp of network infrastructure and operations.
