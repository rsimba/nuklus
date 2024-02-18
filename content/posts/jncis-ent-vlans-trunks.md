+++
title = 'JNCIS-Ent: VLANs and Trunk Ports'
date = 2024-02-14T09:40:27+02:00
author = 'Ricardo Simba'
categories = ['jncis-ent', 'switching']
+++

A **VLAN** is a logical network created within a physical network infrastructure. It allows devices in separate physical locations to be grouped together as if they are on the same network, even though they may be physically dispersed. A **trunk port** is a port type that allows transmission of data for multiple VLANs across a single physical link.

Today we are going to explore both VLANs and trunk ports in the context of Juniper devices.

# Lab Topology
![Lab Topology](/images/jncis_vlans_trunks.jpg)
The primary goal of this lab is to ensure that hosts within the same VLAN can communicate with each other. To achieve this:
- We need to create VLANs on the switches.
- Access ports must be correctly configured to belong to the relevant VLANs, enabling communication among hosts.
- Trunk ports must be appropriately configured between the switches to facilitate the transmission of VLANs across the network.

# Configuration
## VLANs Configuration
On Junos switches, VLANs are configured within the `[edit vlans]` hierarchy. Each VLAN is associated with four parameters: a **routing instance**, a **VLAN name**, a **tag**, and the **interfaces** associated with that VLAN. The default factory configuration assigns all installed interfaces to the default VLAN, which typically uses the 802.1Q tag of 1 (although this tag value can be changed to any number).
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
As mentioned earlier, an access port belongs to a single VLAN and only carries traffic for that VLAN. From the command outputs provided above, it's evident that by default, a switch port isn't assigned to any VLAN. To configure the interface to function as an access port, you need to set up the interface unit, address family, interface mode of operation, and VLAN membership. There are two ways to do this:
- **Option 1**: Configure the interface's association with VLANs under the `[edit vlans]` hierarchy. This involves a two-step process. First, you create a VLAN and then associate the interface with it.
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
Next, you configure the address family within the suitable interface unit. It's important to note that on switches running Junos OS, Layer 2 interfaces are set to access mode by default. If you explicitly configure an interface as a port access mode, you'll encounter an error when committing the configuration:
```
rsimba@acc1# show | compare
[edit interfaces]
+   ge-0/0/1 {
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```
- **Option 2**: Configure the interface association with VLANs under the `[edit interfaces]` hierarchy. First, create a VLAN, then define the interface by specifying the unit number, address family, interface mode, and VLAN membership.
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
On **acc1** switch, we used the first option to configure the access ports, while on **acc2** switch, we opted for the second option. As you can see from the output below, both options achieve the same task.

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
Trunk ports are vital for transmitting traffic across multiple VLANs via a single physical link. However, our current network setup lacks communication between hosts on the same VLAN. This occurs because hosts on the same switch are assigned to different VLANs and subnets. To enable communication between these hosts, we must establish a trunk port between the **acc1** and **acc2** switches.

To configure a trunk port, set the interface mode as **trunk** and then assign the VLANs you want to transmit through that interface:
```
rsimba@acc1# show | compare
[edit interfaces]
+   ge-0/0/5 {
+       unit 0 {
+           family ethernet-switching {
+               interface-mode trunk;
+               vlan {
+                   members [ vl10 vl20 ];
+               }
+           }
+       }
+   }
```
Optionally, you can use the keyword **all** to associate all configured VLANs with a given trunk port. Note that the **acc2** switch has the exact same configuration.

### Verification
We can see below that the trunk port is properly configured and associated with all the defined VLANs:
```
rsimba@acc2# run show vlans

Routing instance        VLAN name             Tag          Interfaces
default-switch          default               1

default-switch          vl10                  10
                                                           ge-0/0/0.0*
                                                           ge-0/0/5.0*
default-switch          vl20                  20
                                                           ge-0/0/1.0*
                                                           ge-0/0/5.0*
```
With the above configuration in place, the hosts in the same VLAN can now communicate with each other. For example, Host4 is now able to ping Host2, which are on the same VLAN/subnet:
```
Host4> ping 172.16.20.2
84 bytes from 172.16.20.2 icmp_seq=1 ttl=64 time=2.040 ms
84 bytes from 172.16.20.2 icmp_seq=2 ttl=64 time=2.423 ms
^C
```

Same thing from Host3 to Host1:
```
Host3> ping 192.168.10.1
84 bytes from 192.168.10.1 icmp_seq=1 ttl=64 time=2.060 ms
84 bytes from 192.168.10.1 icmp_seq=2 ttl=64 time=2.659 ms
^C
```
All MAC addresses are being learned by the switches and properly associated with the appropriate VLANs.
```
rsimba@acc1# run show ethernet-switching table

MAC flags (S - static MAC, D - dynamic MAC, L - locally learned, P - Persistent static, C - Control MAC
           SE - statistics enabled, NM - non configured MAC, R - remote PE MAC, O - ovsdb MAC
           GBP - group based policy, B - Blocked MAC)

Ethernet switching table : 4 entries, 4 learned
Routing instance : default-switch
    Vlan                MAC                 MAC         Age   GBP     Logical                NH        RTR
    name                address             flags             Tag     interface              Index     ID
    vl10                00:50:79:66:68:06   D             -            ge-0/0/0.0             0         0
    vl10                00:50:79:66:68:08   D             -            ge-0/0/5.0             0         0
    vl20                00:50:79:66:68:07   D             -            ge-0/0/1.0             0         0
    vl20                00:50:79:66:68:09   D             -            ge-0/0/5.0             0         0
```

# Conclusion
In this blog post, we explored configuring VLANs and trunk ports on Juniper switches for efficient network communication. We learned how to set up access ports for single VLAN usage and trunk ports for multiple VLAN traffic. By configuring these ports correctly, we enabled seamless communication between hosts within the same VLAN and ensured proper association of MAC addresses.

