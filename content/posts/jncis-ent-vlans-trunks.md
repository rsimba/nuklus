+++
title = 'JJNCIS-Ent: VLANs and Trunk Ports'
date = 2024-02-14T09:40:27+02:00
draft = true
author = 'Ricardo Simba'
categories = ['jncis-ent', 'switching']
+++

A **VLAN** is a logical network created within a physical network infrastructure. It allows devices in separate physical locations to be grouped together as if they are on the same network, even though they may be physically dispersed. A **trunk port** is a port type that allows transmission of data for multiple VLANs across a single physical link.

Today we are going to explore both VLANs and trunk ports in the context of Juniper devices.

# Lab Topology
![Lab Topology](/images/jncis_vlans_trunks.jpg)

The main objective of this lab is to make sure that the hosts in the same VLAN can communicate with each other. For this to happen:
- VLANs must be created on the switches.
- Access ports must be properly configured as members of the appropriate VLANs to allow the hosts to communicate with each other.
- Trunk ports must be properly configured between the switches to allow the VLANs to be transmitted across the network.

# Configuration
## VLANs Configuration
On Junos switches VLANs are configured under the `[edit vlans]` hierarchy and each VLAN is associated with four parameters: a **Routing instance**, a **VLAN name**, a **Tag**, and the **Interfaces** associated to that VLAN. The switch factory-default configuration associates all installed interfaces with the **default** VLAN which uses the 802.1Q Tag of **1** (the tag value can be changed to any number).

```
rsimba@acc2# run show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1
```
When configuring user-defined VLANs, you define both the **VLAN name** and the **VLAN ID**:
```
rsimba@acc2# show | compare
[edit]
+  vlans {
+      vl10 {
+          vlan-id 10;
+      }
+      vl20 {
+          vlan-id 20;
+      }
+  }
```
### Verification
Once created, by default the user-defined VLANs are associated with the **Default-switch** routing instance:
```
rsimba@acc2# run show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1

default-switch          vl10                  10

default-switch          vl20                  20
```
## Access Ports Configuration
As described before, an access port is a port that is a member of a single VLAN and carries traffic only for that VLAN. And from the output commands above, by default a switch port is not associated to any VLAN and in order for the interface to operate as an access port you need to configure the interface **unit**, the **address family**, the **interface mode** of operation, and the **VLAN membership**. There are two different options to accomplish this configuration:
- **Option 1**: Configure the interface association with VLANs under the `[edit vlans]` hierarchy which is a two step process. First you create a **VLAN** and associate the interface to it:
```
rsimba@acc1# show | compare
[edit]
+  vlans {
+      vl10 {
+          vlan-id 10;
+          interface ge-0/0/0.0;
+      }
+      vl20 {
+          vlan-id 20;
+          interface ge-0/0/1.0;
+      }
+  }
```
Second, you configure the address family in the appropriate interface **unit**. This configuration takes into consideration the fact that on switches running Junos OS Layer 2 interfaces default to access-mode, and when you configure explicitly an interface as port access mode you will get an error when committing the configuration.
```
rsimba@acc1# show | compare
[edit interfaces]
+   ge-0/0/1 {
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```
- **Option 2**: Configure the interface association with the VLANs under the `[edit interfaces]` hierarchy where you first create a VLAN and then configure the interface by defining the unit number, address family, interface mode, and VLAN membership:
```
rsimba@acc2# show | compare
[edit interfaces]
+   ge-0/0/0 {
+       unit 0 {
+           family ethernet-switching {
+               interface-mode access;
+               vlan {
+                   members vl10;
+               }
+           }
+       }
+   }
```
### Verification
On **acc1** switch we used the first option to configure the access ports and on **acc2** switch we used the second option. As you can from the output below, both options accomplish the same task.

**acc1** switch:
```
rsimba@acc1# run show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1

default-switch          vl10                  10
                                                           ge-0/0/0.0*
default-switch          vl20                  20
                                                           ge-0/0/1.0*
```
**acc2** switch:
```
rsimba@acc2# run show vlans
Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1

default-switch          vl10                  10
                                                           ge-0/0/0.0*
default-switch          vl20                  20
                                                           ge-0/0/1.0*
```
Note that the `*` symbol indicates that the interface is a member of the VLAN.

## Trunk Ports Configuration

### Native VLAN

