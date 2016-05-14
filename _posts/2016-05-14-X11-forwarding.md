---
layout: post
title:  "X11 forwarding request failed on channel 0"
date:   2016-05-14 23:23:00
categories: Linux
excerpt:    Linux packets transport
---

* content
{:toc}

使用ssh -X远程登录ubuntu15.04虚拟机时会出现错误：

    X11 forwarding request failed on channel 0
    
查看$DISPLAY环境变量没有显示：

    root@inner23:~# echo $DISPLAY

    root@inner23:~#

网上大多数给出的解决方案是修改sshd配置文件：

    vim /etc/ssh/sshd_config
    X11Forwarding yes
    
查看后发现，X11Forwarding已经是yes，说明不是这个问题。退出后重新登录查看ssh的debug信息，有一行：

    debug1: Requesting X11 forwarding with authentication spoofing.
    
google之，发现原因是因为x11使用ipv6连接，需要配置一个本地回环的ipv6地址。但是该虚拟机没有配置ipv6：

    lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:7687 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7687 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:877785 (877.7 KB)  TX bytes:877785 (877.7 KB)

给出解决办法：

    1. add '-4' to the options passed to sshd in /etc/default/ssh
    2. add 'AddressFamily inet' in /etc/ssh/sshd_config
    
重启ssh服务即可。

参考链接：

[http://www.linuxquestions.org/questions/linux-networking-3/x-forwarding-though-ssh-not-working-$display-not-set-879365/](http://www.linuxquestions.org/questions/linux-networking-3/x-forwarding-though-ssh-not-working-$display-not-set-879365/)

[https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=595014](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=595014)

[http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=422327](http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=422327)

    