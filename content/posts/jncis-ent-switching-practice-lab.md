+++
title = 'JNCIS-Ent: Switching Practice Lab'
date = 2024-02-09T09:14:31+02:00
author = 'Ricardo Simba'
tags = ['jncis-ent', 'switching']
+++

The Enterprise Routing and Switching Specialist (JNCIS-Ent) exam enables network professionals to *demonstrate* their competence with networking technologies in general and Juniper's routing and switching platforms. This means that besides studying the theory of different exam topics, you will also need hands-on practice Junos OS.

As mentioned earlier, the JNCIS-Ent exam evaluates candidates on both routing and switching technologies. This is reflected in its exam objectives. For instance, the switching section of the exam includes topics such as:
- Layer 2 Switching or VLANs.
- Spanning Tree.
- Port Security.
- High Availability.

These topics are practiced and tested using Junos OS version 23.1. This is significant because using a different software version might lead to variations in configuration hierarchy levels or commands compared to what you'll encounter in the exam.

# Lab Topology
My plan was to set up a lab where I could practice the Switching topics included in the exam using various design scenarios. The following topology accomplishes exactly that.

![JNCIS-Ent Switching Lab Topology](/images/jncis-ent_switching.jpg)

I use EVE-NG Community Edition to emulate the topology above, and here are the hardware specifications of the host as well as the software version.

## Server Specs and Software Versions
I have an old IBM x3650 M4 server with enough CPU power and memory to comfortably set up and run different labs.

## EVE-NG Version
I have installed the free version of EVE-NG, known as the Community Edition, on the server:
```
rs@lab-srvr:~$ dpkg -l eve-ng
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-==============================================
ii  eve-ng         5.0.1-19     amd64        A new generation software for networking labs.
```

## Junos OS Version
When it comes to practicing and testing Junos OS, Juniper has made it easy by offering free virtual Junos OS options. For switches, there's vJunos-switch, and for routers, there's vJunosEvo. You can download any of these software and run them on your preferred network emulator:
```
rs@acc1> show version
Hostname: acc1
Model: ex9214
Junos: 23.2R1.14
```

## Hosts
I wanted a simple and quick solution that emulate or simulate PCs so I could properly test/validate the different technology configuration. EVE-NG comes with a simple Virtual PC Simulator or VPCS which is more than enough for my purpose:
```
VPCS> version
Welcome to Virtual PC Simulator, version 1.3 (0.8.1)
Build time: May  7 2022 15:27:29
All rights reserved.

VPCS is free software, distributed under the terms of the "BSD" licence.
Source code and license can be found at vpcs.sf.net.
For more information, please visit wiki.freecode.com.cn.
Modified version for EVE-NG.
```
## DHCP Servers
I also wanted to include at least two DHCP servers in the lab to check and confirm some Layer 2 security features like DHCP Snooping and DAI.
```
user@ubuntu22-server:~$ uname -a
Linux ubuntu22-server 5.15.0-27-generic #28-Ubuntu SMP Thu Apr 14 04:55:28 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

# Conclusion
In summary, getting ready for the JNCIS-Ent exam means understanding both theory and hands-on practice with Juniper's networking tools. With EVE-NG Community Edition and tools like vJunos-switch and VPCS, you can create a virtual lab environment to test your skills. By covering topics like Layer 2 switching and VLANs, you'll be better prepared for success on exam day. So, dive into your studies, practice with real-world scenarios, and you'll be ready to ace the JNCIS-Ent exam!
