+++
title = 'JNCIS-Ent Switching: Switching Practice Lab'
date = 2024-02-09T09:14:31+02:00
draft = true
author = 'Ricardo Simba'
categories = ['jncis-ent']
+++

EVE-NG server:
```
rsimba@lab-srvr:~$ dpkg -l eve-ng
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-==============================================
ii  eve-ng         5.0.1-19     amd64        A new generation software for networking labs.
```

vJunosSwitch version:
```
rsimba@acc1> show version
Hostname: acc1
Model: ex9214
Junos: 23.2R1.14
```
Credentials:
username: root
password: 'nopassword'
Hosts:
```
VPCS> version

Welcome to Virtual PC Simulator, version 1.3 (0.8.1)
Dedicated to Daling.
Build time: May  7 2022 15:27:29
Copyright (c) 2007-2015, Paul Meng (mirnshi@gmail.com)
Copyright (c) 2021, Alain Degreffe (alain.degreffe@eve-ng.net)
All rights reserved.

VPCS is free software, distributed under the terms of the "BSD" licence.
Source code and license can be found at vpcs.sf.net.
For more information, please visit wiki.freecode.com.cn.
Modified version for EVE-NG.
```

DHCP Servers:
```
user@ubuntu22-server:~$ uname -a
Linux ubuntu22-server 5.15.0-27-generic #28-Ubuntu SMP Thu Apr 14 04:55:28 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```
credentials:
username: user
password: Test123
