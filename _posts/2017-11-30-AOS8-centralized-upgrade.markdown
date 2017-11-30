---
layout: post
title: ArubaOS 8 centralized upgrade
slug: AOS8-centralized-upgrade
date: 2017-11-30
comments: true
categories:
- Aruba
tags:
- Aruba
- AOS8
- Upgrade
- Live Upgrade
author: Dennis Ladefoged
---
In ArubaOS 8 we have several methods of upgrading the wireless infrastructure. Alle these methods have one thing in commmon; they need a repository for the new image to be downloaded. Historically this has been done using TFTP, FTP, FTPS or SCP from a dedicated server or the engineers own laptop.
Starting from AOS 8.2 the Mobility Master or a Mobility Controller can be configured as a SCP server. Facilitating a central respository for image upgrades.
<!--more-->
### Typical use cases
* Upload/download configuration files to/from nodes
* Download log & crash files from nodes
* Download image from Mobility Master to a number of nodes (cluster for example)

### Configuration

Enabling:

```sh
(VMM-active) [mynode] (config) #service
dhcp                  Enable DHCP service
dhcpv6                Enable DHCPv6 service
scp                   Enable scp copying from/to controller
```

Disabling:

```sh
(VMM-active) [mynode] (config) #no service
dhcp                  Enable DHCP service
dhcpv6                Enable DHCPv6 service
scp                   Enable scp copying from/to controller
```

Verification:

```sh
(VMM-active) [mynode] #show scp

service scp is disabled

(VMM-active) [mynode] #show scp

service scp is enabled

```

### Functionality

Upload file from Linux client to node:

```sh
[LINUX_CLIENT ~]# scp linux.cfg admin@10.0.0.2:linuxToMM.cfg
admin@10.0.0.2's password:
linux.cfg 

(VMM-active) [mynode] # dir
-rw-------    1 root     root         2699 Aug 24 00:11 linuxToMM.cfg
```

Download file from node til Linux client:

```sh
[LINUX_CLIENT ~]# scp admin@10.0.0.2:default.cfg .
admin@10.0.0.2's password:
default.cfg                                100%   48KB  47.7KB/s   00:00
```

Upload a file from node to a Mobility Master:

```sh
(VMC1) #copy flash: md.tar scp: 10.0.0.2 admin md.cfg
Password:******

Secure file copy:
Press 'q' to abort.
............................................
File uploaded successfully

(VMM-active) [mynode] (config) #dir
-rw-r--r--    1 root     root        12024 Jun  6 19:07 md.cfg
```

Download a file from a Mobility Master to node:

```sh
(VMC1) #copy scp: 10.0.0.2 admin default.cfg flash: MMtoMD.cfg
Password:******

Press 'q' to abort.

Secure File Copy:....
(VMC1) #dir
-rw-r--r--    1 root     root        48806 Aug 23 17:39 MMtoMD.cfg
```

### Cluster live upgrade using SCP server on Mobility Master

With ArubaOS 8 we have the possibility of utilizing a feature called "Live Upgrade". With this feature it is possible to do in-service upgrades of a entire cluster of controllers and connected APs with minimal service outage. I will do a in-depth blog post about live-upgrade at a later time, so stay tuned :-)

For this post I will just show how to utilize the Mobility Master as a SCP server in a Live Upgrade.

First, upload the image to the Mobility Master using SCP. This needs to be done from CLI, so no WinSCP, only CLI from Linux or other means.

Second, on the Mobility Master UI, find the corresponding folder where the cluster recides. In my case _DC_. Go under _Configuration_ -> _Tasks_ and click on _Upgrade Cluster_

![step1](/assets/2017/11/step1_cluster_upgrade.png)

Third, Choose the cluster that needs to be upgraded

[![step2](/assets/2017/11/step2_cluster_upgrade.png)](/assets/2017/11/step2_cluster_upgrade.png)

Fourth, we need to point to the Mobility Master IP or VIP if you have two MM´s in redundancy, give the correct image version number and point to the default path ".".

![step3](/assets/2017/11/step3_cluster_upgrade.png)

Live upgrade will execute the entire upgrade for you and download the image from the Mobility Master and not a external server or your laptop. So kick up your legs and enjoy :-P

* Download of software on all cluster members
* Preload of software on all connected APs
* Partitioning of APs
* Roaming of clients between AP partitions so upgrading does not give service interruption for clients