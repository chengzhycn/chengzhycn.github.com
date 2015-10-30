---
layout: post
title:  "CloneZilla 介绍与使用"
date:   2015-10-30 16:17:00
categories: Linux
excerpt:    Linux Backup
---

* content
{:toc}

## 简介
---
CloneZilla（中文译名：再生龙）是一款台湾开发的类似于windows平台下ghost备份的软件。它支持Linux以及windows下磁盘备份与恢复，支持将一个备份镜像恢复到多台相似环境的主机上，方便环境的快速部署。目前CloneZilla有Live和SE两个版本，Live版主要针对单台主机进行操作，而SE可以通过内部网络同时对多台支持PXE的主机进行操作，但需要与它们实验室开发的DRBL集中管理环境相结合使用（CloneZilla SE就是DRBL里一个套件）。

官方网站：[http://clonezilla.org](http://clonezilla.org/)

下载链接：

*   Live版：[http://clonezilla.org/downloads.php](http://clonezilla.org/downloads.php)
*   SE版：[http://drbl.org/download/](http://drbl.org/download/)

## 主要缺陷
---
*   CloneZilla在对分区进行还原时目标分区需要与原分区文件格式、分区顺序保持一致，同时，目标分区的大小必须大于或等于原分区的大小。
*   由于镜像格式限制，CloneZilla不能单独还原镜像中的一个或多个文件，必须同时还原整个镜像（Linux内置的rsync远程快速文件备份等工具可以实现此项功能）。
*   若需要刻盘的话，CloneZilla暂不支持多个CD/DVD的刻录，即整个镜像必须得刻录在同一个光盘上。这对较大的备份镜像是一个限制。不过现在硬盘存储发展迅速，U盘、移动硬盘或者PXE都能很好的解决这个问题。

虽然CloneZilla有着很多缺陷，但是结合其优点仍不失为一种优秀的容灾解决方案。

## Live版安装
---
从官方网站上下载安装镜像，选择基于Ubuntu的分支，U盘启动备份选择zip格式，光盘启动备份选择iso格式（下载可能出现404 not found，需翻墙）。

光盘版直接使用光盘刻录软件刻录即可。

U盘版下载[Tuxboot](http://sourceforge.net/projects/tuxboot/files/latest/download?source=files)，选择预下载好的ISO file（也可以直接在Tuxboot里下载），选定刻录的U盘盘符，即可开始刻录。

![tuxboot1]({{"/pics/tuxboot1.png"}} "tuxboot截图")

![tuxboot2]({{"/pics/tuxboot2.png"}} "tuxboot截图")

## 硬盘备份
---
启动盘制作完成后，插入需要备份的主机，进入BIOS设置为U盘启动，进入CloneZilla界面，根据提示选择相应的选项。

![CloneZilla1]({{"/pics/CloneZilla1.png"}} "CloneZilla截图")

![CloneZilla2]({{"/pics/CloneZilla2.png"}} "CloneZilla截图")

![CloneZilla3]({{"/pics/CloneZilla3.png"}} "CloneZilla截图")

![CloneZilla4]({{"/pics/CloneZilla4.png"}} "CloneZilla截图")

选择设定固定IP地址后，手动配置本机和服务器的IP信息（根据实际情况自己设置）。

*   本机网卡IP地址：192.168.99.214
*   本机子网掩码：255.255.255.0
*   网关：192.168.99.1
*   域名服务器：默认（不重要）
*   服务器FQDN：192.168.99.211
*   远程SSH端口：22
*   服务器账号：root
*   镜像路径：/home

配置完成后，通过SSH与远程服务器相连接，会要求输入该服务器的root密码。连接后，因为并未备份过，会提示当前目录下无挂载镜像，跳过即可。

接下来选择初学模式，选择所需备份的硬盘，储存本机硬盘位镜像文件。其它选项均默认即可。完成镜像备份。

![CloneZilla5]({{"/pics/CloneZilla5.png"}} "CloneZilla截图")

![CloneZilla6]({{"/pics/CloneZilla6.png"}} "CloneZilla截图")

## 硬盘还原
---
还原时进入CloneZilla和连接远程SSH服务器与备份时步骤一样。但是因为选择的目录有了之前备份的镜像，在选定模式时会发现比备份时多出了还原镜像文件的选项（还有一些对镜像文件处理的选项）。

![CloneZilla7]({{"/pics/CloneZilla7.png"}} "CloneZilla截图")

选择“还原镜像到本机硬盘”，接下来选择需要还原的镜像以及目标硬盘，一路确定即可完成硬盘的还原。

![CloneZilla8]({{"/pics/CloneZilla8.png"}} "CloneZilla截图")

值得注意的是，若目标硬盘不是原硬盘，**则目标硬盘分区的大小不得小于原硬盘中分区大小，不管有没有使用。同时，目标硬盘的分区格式，分区的顺序也必须与原硬盘保持一致！！！**

## Linux下优秀的21种备份工具
---
Backup Tool (Graphical User Interface)

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>Areca Backup</td>
      <td>File backup software developed in Java</td>
   </tr>
   <tr>
      <td>BackupPC</td>
      <td>High-performance, enterprise-grade system for backing up PCs</td>
   </tr>
   <tr>
      <td>Bacula</td>
      <td>Network backup, recovery and verification</td>
   </tr>
   <tr>
      <td>fwbackups</td>
      <td>Feature-rich backup sofware</td>
   </tr>
   <tr>
      <td>Keep</td>
      <td>Removed (was a Backup system for KDE) </td>
   </tr>
   <tr>
      <td>Simple Backup Solution</td>
      <td>Set of backend backup daemon and Gnome GUI frontends</td>
   </tr>
</table>

Backup Tool (Command-line)

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>afbackup</td>
      <td>Client-Server Backup System (GUI is also available)</td>
   </tr>
   <tr>
      <td>AMANDA</td>
      <td>Advanced Maryland Automatic Network Disk Archiver</td>
   </tr>
   <tr>
      <td>Cedar Backup</td>
      <td>Local and remote backups to CD or DVD media</td>
   </tr>
   <tr>
      <td>Duplicity</td>
      <td>Encrypted bandwidth-efficient backup</td>
   </tr>
   <tr>
      <td>Dump / restore</td>
      <td>Dump and restore utilities for ext2/ext3 filesystems</td>
   </tr>
   <tr>
      <td>tar</td>
      <td>Tar archiving utility</td>
   </tr>
</table>

Snapshot backups

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>FlyBack</td>
      <td>Equivalent of OS X's Time Machine</td>
   </tr>
   <tr>
      <td>Time Vault</td>
      <td>Snapshotting daemon</td>
   </tr>
</table>

Synchronisation

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>rsnapshot</td>
      <td>Local and remote filesystem snapshot utility</td>
   </tr>
   <tr>
      <td>rsync</td>
      <td>Fast remote file copy program</td>
   </tr>
</table>

Disaster Recovery / Disk Cloning

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>Clonezilla</td>
      <td>Offers similar functionality to Symantec Ghost</td>
   </tr>
   <tr>
      <td>Mondo Rescue</td>
      <td>A powerful disaster recovery suite</td>
   </tr>
   <tr>
      <td>PartImage</td>
      <td>Backup partitions into a compressed image file</td>
   </tr>
   <tr>
      <td>PING</td>
      <td>Also offers similar functionality to Symantec Ghost</td>
   </tr>
</table>

Specialist

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>Zmanda</td>
      <td>Perl-based utility to automate backup and recovery of MySQL databases</td>
   </tr>
</table>