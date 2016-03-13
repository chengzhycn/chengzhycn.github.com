---
layout: post
title:  "高速率http实时流量分析"
date:   2015-10-30 16:17:00
categories: Linux
excerpt:    Linux Site Reliability Engineering
---

* content
{:toc}

## 安装PF_RING
PF_RING是一个高速率包捕获，过滤和分析工具。

### 安装libpcap

编译PF_RING里面的libpcap时会报错
    
    No rule to make target `npcap_utils.o', needed by `libpcap.so'.  Stop.
    
需要安装libpcap库。

    wget http://www.tcpdump.org/release/libpcap-1.7.4.tar.gz
    tar xvf libpcap-1.7.4.tar.gz
    cd libpcap-1.7.4/
    ./configure
    make
    make install
    
### 下载PF_RING源码

可以从sourceforge上下载的最新PF_RING发行版，也可以从git repo中克隆。
    
github网址：[https://github.com/ntop/PF_RING](https://github.com/ntop/PF_RING)

sourceforge链接：[https://sourceforge.net/projects/ntop/files/PF_RING/](https://sourceforge.net/projects/ntop/files/PF_RING/)

### 安装PF_RING
    
    cd PF_RING/userland/lib
    ./configure --prefix=/opt/pfring
    make install
    
    cd ../libpcap
    ./configure --prefix=/opt/pfring
    make install
    
    cd ../tcpdump-4.1.1
    ./configure --prefix=/opt/pfring
    make install

    cd ../../kernel
    ./configure
    make
    make install

    modprobe pf_ring enable_tx_capture=0 min_num_slots=32768
    
### 直接从软件库内安装PF_RING
更为简便的方法是直接从官方支持的软件库内安装ntop系列套件，包括了pfring, nprobe, ntopng, ntopng-data, n2disk, nbox。需要注意的是，pfring的ZC/DNA库和nprobe、n2disk需要在官网上购买商业许可，如果是学生或者是研究机构，可以给ntop发送邮件获得教育许可。

Ubuntu/Debian:

    12.04LTS
        wget http://apt-stable.ntop.org/12.04/all/apt-ntop-stable.deb
        sudo dpkg -i apt-ntop-stable.deb
    14.04LTS
        wget http://apt-stable.ntop.org/14.04/all/apt-ntop-stable.deb
        sudo dpkg -i apt-ntop-stable.deb
        
    安装工具ntop系列套件    
        apt-get clean all
        apt-get update
        apt-get install pfring nprobe ntopng ntopng-data n2disk nbox
    
    如果需要使用ZC/DNA模块，需要安装相应的网卡驱动：
        apt-get install pfring-drivers-zc-dkms
        apt-get install pfring-drivers-dna-dkms
        
RedHat/CentOS:

    创建repo文件（将Ｘ替代为６（centos6）或者７（centos7））：
        # cat /etc/yum.repos.d/ntop.repo
        [ntop]
        name=ntop packages
        baseurl=http://packages.ntop.org/centos-stable/$releasever/$basearch/
        enabled=1
        gpgcheck=1
        gpgkey=http://packages.ntop.org/centos-stable/RPM-GPG-KEY-deri
        [ntop-noarch]
        name=ntop packages
        baseurl=http://packages.ntop.org/centos-stable/$releasever/noarch/
        enabled=1
        gpgcheck=1
        gpgkey=http://packages.ntop.org/centos-stable/RPM-GPG-KEY-deri
        
        # cat /etc/yum.repos.d/epel.repo 
        [epel]
        name=Extra Packages for Enterprise Linux X - $basearch
        mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-X&arch=$basearch
        failovermethod=priority
        enabled=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-X
        
        # cd /etc/yum.repos.d/
        # wget https://copr.fedoraproject.org/coprs/saltstack/zeromq4/repo/epel-X/saltstack-zeromq4-epel-X.repo
        
        yum erase zeromq3 (Do this once to make sure zeromq3 is not installed)
        yum clean all
        yum update
        yum install pfring n2disk nprobe ntopng ntopng-data nbox
        
    如果需要使用ZC/DNA模块，需要安装相应的网卡驱动：
        yum install pfring-drivers-zc-dkms
        yum install pfring-drivers-dna-dkms

    
如果安装失败，可以下载源码包之后通过源码安装。

## 使用n2disk进行高速抓包

### TODO

## 安装bro

### 安装依赖库

    yum install cmake make gcc gcc-c++ flex bison libpcap-devel openssl-devel python-devel swig zlib-devel
    
### 下载bro源码

从官网上可以获取最新的bro release源码，也可以从其git repo里拷贝最新的development version。

官方下载链接：[https://www.bro.org/download/index.html](https://www.bro.org/download/index.html)

github网址：[https://github.com/bro/bro](https://github.com/bro/bro)
    
### 编译安装bro

    ./configure --with-pcap=/opt/pfring
    make
    make install

确认bro已经正确链接到PF_RING libpcap libraries:

    ldd /usr/local/bro/bin/bro | grep pcap
      libpcap.so.1 => /opt/pfring/lib/libpcap.so.1 (0x00007fa6d7d24000)

添加环境变量

    echo 'export PATH=/usr/local/bro/bin:$PATH' >> ~/.bash_profile
    source ~/.bash_profile

至此，bro的基本环境配置完成，安装根目录为
    
    $prefix=/usr/local/bro
    
## 使用bro

### TODO