+++
title = 'JNCIS-Ent: Layer 2 Switching Primer'
date = 2024-02-06T15:56:29+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

The purpose of computer networking is to facilitate communication and resource sharing between computers and other devices within the same or separate physical locations. This is achieved by connecting computers, servers, printers, and other devices both physically and logically.

For physical connections, various options can be used, including cable connectivity such as fiber or copper, as well as wireless connectivity such as Microwave and WiFi. However, simply connecting networking devices is not sufficient for communication to flow between them. A logical connection is also required, which is achieved through a set of protocols and rules.

These protocols and rules are usually organized into layers to provide a modular and hierarchical structure for network communication. One well-known example is the TCP/IP protocol suite. This organization allows for efficient and standardized communication across diverse network environments.

# The TCP/IP Link Layer
The TCP/IP suite consists of four layers, where the **Link layer** is responsible for both the physical and logical connections between devices. At this layer, various protocols, including Ethernet, facilitate the establishment of these connections and other functions. Ethernet is a set of standards governing aspects such as frame format. Broadly, Ethernet standards encompass:
- Characteristics of the Physical layer.
- Protocols governing access to the physical medium, such as CSMA/CD.
- Frame format.

## Ethernet Frame Format
Bits transmitted by an Ethernet-supported device are organized into frames, which comprise the Layer Header and Trailer, along with the encapsulated data. There are two main Ethernet frame formats:
- **Ethernet II frame format**: This is the most common Ethernet frame format used today, defined by the IEEE 802.3 standard. It includes fields such as destination and source MAC addresses, EtherType, and payload. Notably, it lacks a length field, distinguishing it from the IEEE 802.3 frame format.
- **IEEE 802.3 frame format**: This is an older frame format used in traditional Ethernet networks. It includes fields such as preamble, start frame delimiter (SFD), destination and source MAC addresses, length/type (indicating either the length of the frame or the protocol type), data, and Frame Check Sequence (FCS).

## Ethernet Addressing
Each host using Ethernet has a unique address called a MAC address, which is a sequence of six numbers separated by colons. Each number corresponds to one byte of the 6-byte address and is represented by a pair of hexadecimal digits, with leading zeros omitted.

There are three different types of MAC addresses used in Ethernet, each with different operations when being switched:
- **Unicast**: This is a unique address assigned to a single device. When a device wants to communicate with another device on the same network, it includes the MAC address of the destination device in the data packet.
- **Multicast**: This type of address is used to send data to a group of devices for one-to-many communication.
- **Broadcast**: This is a special type of address used for one-to-all communication.

## Switched LAN 
Computing networks can be connected and organized based on their geographical scopes and characteristics. For example, a LAN is a network that spans a relatively small geographical area, such as a single building, office, or campus.

There are two different types of LAN architectures with distinct characteristics and operational principles:
- **Shared LAN**: This is an older form of connecting LAN devices where all devices are connected to a central device called a hub. Shared LANs operate in a single collision domain, meaning that when one device transmits data, all other devices connected to the same hub receive that data, even if it is not intended for them. This also increases the chance of collisions of frames. Ethernet uses the CSMA/CD protocol to avoid and manage frame collisions.
- **Switched LAN**: In this setup, devices are connected to a switch, which reduces the likelihood of collisions by breaking a single collision domain into multiple smaller collision domains. A collision domain in a switched LAN consists of the physical segment between a node and its connected switch port.

## Switching an Ethernet Frame
A switch's ultimate goal is to deliver Ethernet frames to the appropriate destinations based on the destination MAC address in the frame header. The switch builds and maintains a CAM table, which matches the destination MAC address with the port used to connect to a node. A switch achieves this using the following mechanisms:
- **Address Learning**: The switch learns the MAC address of the devices connected to its ports by examining the Ethernet header information of the received frame, looking for the source MAC address of sending hosts, and stores them in the CAM table.
- **Forwarding**: The switch forwards the frame to the appropriate port based on the destination MAC address in the frame header.
- **Flooding**: The switch sends frames with an unknown destination MAC address to all active ports, except the port on which it was received.
- **Filtering**: The switch drops the frame if the destination MAC address is the same as the source MAC address.
- **Aging**: The switch removes the MAC address from the CAM table if it has not been used for a certain period of time.

A switch performs switching/bridging processes for determining what to do with a frame as follows:
- **Learning**: When a switch receives a frame, it examines the source MAC address and incoming port. It then performs one of the following actions, depending on whether the MAC address is present in the CAM table:
  - If not present, it adds the source MAC address and port number to the MAC table and starts the default 300-second aging timer for this MAC.
  - If present, it resets the default 200-second aging timer.
- **Unicast Frame Forwarding**: A switch examines the destination MAC address and if it is a unicast frame, performs one of the following actions, depending on whether the MAC address is present in the MAC table:
  - If not present, it floods the frame out all ports except the incoming port.
  - If present, it forwards the frame out of the port from which that MAC address was learned previously.
- **Broadcast or Multicast Frame Forwarding**: The switch examines the destination MAC address and if it is a broadcast or multicast, forwards the frame out all ports except the incoming port.

# Switching Technologies
Switching technologies refer to the methods used by switches to forward data packets from one port to another within a network. These switching technologies vary in terms of latency, error checking capabilities, and suitability for different network environments.

## Switching Modes
Vendors offer various switching modes to determine when to switch a frame. However, three modes in particular dominate the industry:
- **Store-and-forward**: The switch receives the entire frame before beginning the switching process.
- **Cut-through**: The switch starts the forwarding process as soon as it receives the destination address.
- **Fragment-free**: The switch forwards a frame after receiving the first 64 octets of the frame, allowing detection of collisions before the source finishes its transmission of the 64 octets.

## VLANs
A full Layer-2-only switched network is referred to as a *flat network topology*, which constitutes a single broadcast domain—meaning every connected device sees every broadcast frame transmitted anywhere in the network. Due to the Layer 2 foundation, flat networks cannot contain redundant paths for load balancing or fault tolerance.

VLANs are one of the Layer 2 technologies created to overcome the limitations of flat switched networks. By definition, a VLAN is a single broadcast domain. Each VLAN operates as if it were a separate physical network, even though devices within the VLAN can be physically located on the same network switch.

## Trunking (VLAN Tagging)
A trunk link is used to connect switches or routers and transport more than one VLAN through a single link. Because a trunk link can transport many VLANs, a switch must identify frames with their respective VLANs as they are sent and received over a trunk link.

Frame identification or tagging assigns a unique user-defined ID (VLAN ID) to each frame transported on a trunk link. The switch at the far end of the link examines the VLAN identifier and places the frame into the correct VLAN.

VLAN identification can be performed using the 802.1Q protocol, which carries VLAN associations over trunk links. 802.1Q inserts an extra 4-byte tag in the Ethernet header of the original frame, following the source address. As a result, the frame retains its original source and destination MAC addresses. However, because the original header has been expanded, 802.1Q encapsulation forces a recalculation of the original FCS field.

The Tag header field includes the following fields:
- **TPID** (**Tag Protocol Identifier**): a 16-bit field that indicates to the receiver that an 802.1Q tag follows. This field always has a value of 0x8100.
- **Priority** (**3 bits**): 8 priority levels are defined in 802.1p that can be used for CoS.
- **CFI** (**Canonical Format Indicator**) (**1 bit**): This single bit indicates whether the MAC addresses in the MAC header are in Canonical (0) or non-canonical (1) format.
- **VID** (**VLAN identifier**) (**12 bits**): Indicates the source VLAN membership for the frame. The 12-bit field allows for VLAN values between 0 and 4095. However, VLANs 0, 1, and 4095 are reserved.

802.1Q adds to the length of an existing Ethernet frame. Because Ethernet frames cannot exceed 1518 bytes, the additional VLAN tagging information can cause the frame to become too large. Frames that barely exceed the MTU size are called baby giant frames.

802.1Q also introduces the concept of a native VLAN on a trunk. Frames belonging to this VLAN are not encapsulated with any tagging information at all, as if a trunk link was not being used.

## STP
Without loops, a network topology lacks redundancy. If any component fails, connectivity is compromised. Loops should not be viewed as misconfigurations but rather as a fundamental part of a well-designed network. However, in a Layer 2 redundant topology, several problems may arise, including:
- Broadcast storms
- Multiple frame transmissions
- Instability in MAC tables

The purpose of the Spanning Tree Protocol (STP) algorithm is to enable switches to dynamically discover a loop-free subset of the topology (a tree). STP ensures that there is just enough connectivity in the network so that, where physically possible, there is a path between every pair of LANs (the tree is spanning).

STP serves as a layer 2 loop-avoidance mechanism to resolve problems caused by redundant topologies. It achieves this by having switches transmit special messages to each other, allowing them to calculate a spanning tree.

STP forces certain ports into a standby state so that they do not listen, forward, or flood data frames. Frames are forwarded to and from ports selected for inclusion in the spanning tree. Frames are discarded upon receipt and are never forwarded to ports that are not selected for inclusion in the spanning tree.

STP provides loop resolution by performing the following steps:
1. Election of one root bridge
2. Selection of the root port on the non-root bridge
3. Selection of the designated port on each segment

When creating a loop-free logical topology, STP follows the same four-step decision sequence:
1. Lowest Root Bridge ID (BID)
2. Lowest Path Cost to Root Bridge
3. Lowest Port ID

# Conclusion
In summary, computer networking relies on both physical connections, like cables and wireless, and logical connections governed by protocols and rules. Ethernet plays a key role in establishing connections between devices, with MAC addresses identifying each device uniquely. LANs can be shared or switched, with switched LANs offering more efficient communication. Switches manage network traffic, directing frames to their destinations using various mechanisms. VLANs segment broadcast domains, while trunking enables multiple VLANs over a single link. The Spanning Tree Protocol ensures loop-free networks for stable communication. Understanding these concepts is essential for building and managing effective networks.
