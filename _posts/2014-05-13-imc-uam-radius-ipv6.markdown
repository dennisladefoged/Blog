---
layout: post
title: IMC UAM Radius and IPv6
date: 2014-05-13 08:54:23
type: post
slug: IMC-UAM-Radius-and-IPv6
categories:
- HP IMC
tags:
- HP networking
- IMC
- IPv6
- radius
- uam
author: dennis
---
Yesterday while installing IMC for an POC (proof of concept) I stumbled on a problem with configuring radius on the UAM module. I had everything lined up and configured, but nothing was working!
<!--more-->
My access device which was a MSM controller was sending radius requests towards the IMC radius server on 1812, but was not receiving any response. I fired Wireshark op un the IMC server (a windows 2012 R2) and could see the radius request landing on the NIC. I double checked everything and the configuration was sound. I then proceeded to check if the IMC server was listening on TCP port 1812 and 1813 (std. radius ports) with the command "netstat -an". Netstat reported that IMC was indeed not listening on any radius ports, not even the Cisco proprietary ports (1645,1646).
Next step was the IMC UAM log which recides in this path "C:\Program Files\iMC\uam\log\". And the following error filled the log:
```yaml
[ERROR (1)] ; [38072] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; Fail to bind Socket with Port for Receive Thread[isIPv6=0].
[WARNING (2)] ; [44440] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; Receive Thread Quit Abnormally.
[ERROR (1)] ; [38072] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; ip:168299340, port:1812
[WARNING (2)] ; [38072] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; Receive Thread Quit Abnormally[isIPv6=0].
[WARNING (2)] ; [38072] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; Receive Thread Quit Abnormally.
[WARNING (2)] ; [45480] ; UAM ; $SYS$ ; (NULL) ; (NULL) ; (NULL) ; The receive threads are running abnormally.
```
The error is hinting that UAM cannot listen on the server´s network adapter because IPv6 is enabled. So i proceeded with disabling IPv6 on the network adapter:
"http://networkinglife.dk/wp-content/uploads/2014/05/net_adapter_disable_ipv6.png"><img class="aligncenter size-medium wp-image-125" src="{{ site.baseurl }}/assets/net_adapter_disable_ipv6-238x300.png" alt="net_adapter_disable_ipv6" width="238" height="300" />
I then rebooted the IMC server through the Intelligent Deployment Monitoring Agent and Voila! UAM was now listening on ports TCP 1812 and 1813.
Summary
Short story... if UAM radius is not listening on any ports and spewing errors... disable IPv6. I have contacted HP with this bug and have not received any reply yet.
