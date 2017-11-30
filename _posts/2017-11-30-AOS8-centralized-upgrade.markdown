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

Enabling

```sh
(Aruba7220) [mynode] (config) #service
dhcp                  Enable DHCP service
dhcpv6                Enable DHCPv6 service
scp                   Enable scp copying from/to controller
```

Disabling

```sh
(Aruba7220) [mynode] (config) #no service
dhcp                  Enable DHCP service
dhcpv6                Enable DHCPv6 service
scp                   Enable scp copying from/to controller
```

### Functionality
