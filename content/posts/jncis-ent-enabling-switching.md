+++
title = 'JNCIS-Ent: Configuring Access Ports'
date = 2024-02-12T09:54:15+02:00
categories = ['jncis-ent']
author = 'Ricardo Simba'
+++

Usually, a switch has two main types of ports for data traffic: access ports and trunk ports. Access ports are used to connect end devices directly to the switch and they typically belong to a single VLAN and carries traffic only for that VLAN.

In this post, we'll learn how to set up an access port on a Juniper switch. We'll explore the different ways you can configure it to make sure devices can connect and communicate smoothly.

# Lab Topology
In this lab, we'll use a basic topology to reach our objectives.
![Topology](/images/junos_access_ports.jpg)

Our Goals:
- Configure access ports using various methods and verify.
- Ensure the hosts can communicate with each other.

# Configuration
When configuring an interface on a device with Junos OS, there are two important things to consider:
- **Unit** Parameter: This is used to specify logical units or subinterfaces within a physical interface. Each unit represents a separate logical interface that can have its own configuration settings. 
- **Address Family** Parameter: This is used to specify the address family for a unit and subinterface. 

In the output below, you can see that the interface is up and running, but it's not configured for any specific protocol by default. You have to explicitly configure an interface to function as access for traffic to flow.
```
rsimba@acc1> show interfaces terse | except ".1"
Interface               Admin Link Proto    Local                 Remote
ge-0/0/0                up    up
ge-0/0/1                up    up
ge-0/0/2                up    up
```
Junos OS offers three ways to configure access ports:
- **Individual Interface**: You can configure settings for each interface separately.
- **Interface Range**: Configure settings for multiple interfaces at once by specifying a range.
- **Combination**: Use both individual and range configurations as needed.

## Individual Interface Configuration
When using this option, you configure both the unit and address family parameters on a single interface:
```
rsimba@acc1# show | compare
[edit interfaces]
+   ge-0/0/0 {
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```

## Interface Range Configuration
In cases where you have many interfaces needing the same settings, you can use Junos OS's interface range configuration feature. You do this by using either `member` or `member-range` commands.
```
rsimba@acc1# set member?
Possible completions:
> member               Interfaces belonging to the interface range
> member-range         Interfaces range in <start-range> to <end-range> format
```
In the configuration below, we use the `edit` command to enter the interface range settings. Then, we add specific interfaces to this range using the `member` command. After that, we define the address family just once, and it's automatically applied to all interfaces within that range.
```
rsimba@acc1# show | compare
[edit interfaces]
+   interface-range dhcp-interfaces {
+       member ge-0/0/6;
+       member ge-0/0/7;
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```
In the configuration below, we used the `set` command to create a name for the interface range and include a range of members. We did this in just one line by using the `member-range` command.
```
rsimba@acc1# show | compare
[edit interfaces]
+   interface-range dhcp-interfaces {
+       member-range ge-0/0/6 to ge-0/0/7;
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```
## Combination of Individual and Interface Range Configuration
You can also mix both the `member` and `member-range` commands within a single interface range name, as shown below. You can use either the `edit` or `set` command for added flexibility.
```
rsimba@acc1# show | compare
[edit interfaces]
+   interface-range access-interfaces {
+       member ge-0/0/1;
+       member-range ge-0/0/6 to ge-0/0/7;
+       unit 0 {
+           family ethernet-switching;
+       }
+   }
```

## Verification
Our goal was to activate basic layer 2 features on a group of interfaces. We accomplished this using various configuration methods. Now, we can observe that a physical interface not only has both the **Admin** and **Link** states *up* but also has a **unit** and **address family** configured. This configuration allows the interface to operate as an access interface, ready to send and receive data.
```
rsimba@acc1# run show interfaces terse | except "inet|.1"
Interface               Admin Link Proto    Local                 Remote
ge-0/0/0                up    up
ge-0/0/0.0              up    up   eth-switch
ge-0/0/1                up    up
ge-0/0/1.0              up    up   eth-switch
ge-0/0/6                up    up
ge-0/0/6.0              up    up   eth-switch
ge-0/0/7                up    up
ge-0/0/7.0              up    up   eth-switch
```
You can use different `show` commands options to verify and get more information about an interface configuration and operation, for example:
```
rsimba@acc1# run show interfaces ?
  detail               Display detailed output
  diagnostics          Show interface diagnostics information
  extensive            Display extensive output
```

With the configuration above, we can see that **Host1** is now able to communicate with **Host2**:
```
VPCS> ping 192.168.10.2
84 bytes from 192.168.10.2 icmp_seq=1 ttl=64 time=1.128 ms
^C
```

We can also confirm that switch **acc1** has learned the MAC addresses of the connected hosts:
```
rsimba@acc1# run show ethernet-switching table

MAC flags (S - static MAC, D - dynamic MAC, L - locally learned, P - Persistent static, C - Control MAC
           SE - statistics enabled, NM - non configured MAC, R - remote PE MAC, O - ovsdb MAC
           GBP - group based policy, B - Blocked MAC)

Ethernet switching table : 4 entries, 4 learned
Routing instance : default-switch
    Vlan                MAC                 MAC         Age   GBP     Logical                NH        RTR
    name                address             flags             Tag     interface              Index     ID
    default             00:50:00:00:0a:00   D             -            ge-0/0/7.0             0         0
    default             00:50:00:00:0b:00   D             -            ge-0/0/6.0             0         0
    default             00:50:79:66:68:06   D             -            ge-0/0/0.0             0         0
    default             00:50:79:66:68:07   D             -            ge-0/0/1.0             0         0
```
Switch **acc1** has the same information in the Layer 2 forwarding table, allowing it to move traffic from one interface to another:
```
rsimba@acc1# run show route forwarding-table family ethernet-switching
Routing table: default-switch.bridge
VPLS:
Destination        Type RtRef Next hop           Type Index    NhRef Netif
default            perm     0                    dscd      535     1
ge-0/0/0.0         intf     0                    ucst      572     3 ge-0/0/0.0
ge-0/0/1.0         intf     0                    ucst      582     3 ge-0/0/1.0
ge-0/0/6.0         intf     0                    ucst      583     4 ge-0/0/6.0
ge-0/0/7.0         intf     0                    ucst      584     4 ge-0/0/7.0

Routing table: default-switch.bridge
Bridging domain: default.bridge
VPLS:
Enabled protocols: Bridging, ACKed by all peers,
Destination        Type RtRef Next hop           Type Index    NhRef Netif
00:50:00:00:0a:00/48 user     0                  ucst      584     4 ge-0/0/7.0
00:50:00:00:0b:00/48 user     0                  ucst      583     4 ge-0/0/6.0
0x30005/51         user     0                    comp      575     2
0x30003/51         user     0                    comp      576     2
```

# Conclusion
In this blog post, we explored the process of configuring access ports on a Juniper switch using various methods. We also learned how to validate these configurations.

Happy labbing!

