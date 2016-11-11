---
layout: post
title:  "网络编程中的数据结构与api"
date:   2016-11-11 11:00:00
categories: C/C++
excerpt:    网络编程中的数据结构与api
---

* content
{:toc}

在网络编程中，网络层数据结构存储了网络传输的地址族，目的ip地址，目的端口号等重要信息，socket API为程序员提供了通用且方便的接口去实现发送、接收等网络包处理操作。

## 数据结构

### 通用地址结构体

#### struct sockaddr

    /* Structure describing a generic socket address.  */
    struct sockaddr
    {
        __SOCKADDR_COMMON (sa_);    /* Common data: address family and length.  */
        char sa_data[14];           /* Address data.  */
    };

`sockaddr` 结构体共16个字节，是socket编程中标准的地址结构体。几乎所有的socket API均使用`sockaddr`作为其地址信息存储结构。但由于定义它的时候还处于IPv4的年代，并没有预料到会有IPv6的诞生，`sockaddr`大小只有16字节，是无法存储IPv6 128位的IP地址的，因此在socket扩展中加入了第二种通用地址结构。

#### struct sockaddr_storage

    /* Structure large enough to hold any socket address (with the historical
        exception of AF_UNIX).  */
    #define __ss_aligntype	unsigned long int
    #define _SS_PADSIZE \
        (_SS_SIZE - __SOCKADDR_COMMON_SIZE - sizeof (__ss_aligntype))

    struct sockaddr_storage
    {
        __SOCKADDR_COMMON (ss_);	/* Address family, etc.  */
        char __ss_padding[_SS_PADSIZE];
        __ss_aligntype __ss_align;	/* Force desired alignment.  */
    };

相比`sockaddr`，`sockaddr_storage`结构体的内存空间扩展到了128字节（`__SOCKADDR_COMMON`，`_SS_SIZE` 等通用结构体成员的宏定义在 `<bits/sockaddr.h>` 内），足以存储IPv6地址。因此，在IPv6网络编程时，绝大多数情况下使用 `sockaddr_storage` 存储网络地址信息，同时为了与老代码保持一致性，在调用socket API时还需使用强制类型转换转换为 `sockaddr`。

以上两种通用地址结构的原始定义位于 `<bits/socket.h>` 内，在使用时，包含头文件 `<sys/socket.h>` 即可。另外在Ubuntu 64位系统中，系统头文件并不是位于 `/usr/include/sys`，而是 `/usr/include/x86_64-linux-gnu/sys`。至于编译器怎么将 `#include <sys/socket.h>` 定向到 `/usr/include/x86_64-linux-gnu/sys/socket.h` 的，目前还没弄明白=，=。

#### struct addrinfo

    struct addrinfo{  
        int ai_flags;                         /* AI_PASSIVE,AI_CANONNAME,AI_NUMERICHOST */  
        int ai_family;                        /* AF_INET,AF_INET6 */  
        int ai_socktype;                   /* SOCK_STREAM,SOCK_DGRAM */  
        int ai_protocol;                   /* IPPROTO_IP, IPPROTO_IPV4, IPPROTO_IPV6 */  
        size_t ai_addrlen;               /* Length */  
        char *ai_cannoname;         /* */  
        struct sockaddr *ai_addr;   /* struct sockaddr */  
        struct addrinfo *ai_next;      /* pNext */  
    }  

`addrinfo` 是用于 `getaddrinfo` 函数中存储地址信息的。在IPv6编程中，`getaddrinfo` 取代了原有的 `gethostbyaddr`, `gethostbyname`, `getservbyname`, `getservbyaddr`等函数，通过主机名或者ip地址获取相应的地址信息。

### IPv4

#### 头文件

    #include <sys/socket.h>
    #include <netinet/in.h>
    #include <netinet/ip.h> /* superset of previous */

netinet 还是位于 `/usr/include` 内。

#### 地址结构

    struct sockaddr_in {           /* 16 bytes */
        sa_family_t    sin_family; /* address family: AF_INET */
        in_port_t      sin_port;   /* port in network byte order */
        struct in_addr sin_addr;   /* internet address */
    };

    /* Internet address. */
    struct in_addr {
        uint32_t       s_addr;     /* address in network byte order */
    };

`AF_INET` 值为2。

### IPv6

#### 头文件

    #include <sys/socket.h>
    #include <netinet/in.h>

#### 地址结构

    struct sockaddr_in6 {              /* 28 bytes */
        sa_family_t     sin6_family;   /* AF_INET6 */
        in_port_t       sin6_port;     /* port number */
        uint32_t        sin6_flowinfo; /* IPv6 flow information */
        struct in6_addr sin6_addr;     /* IPv6 address */
        uint32_t        sin6_scope_id; /* Scope ID (new in 2.4) */
    };

    struct in6_addr {
        unsigned char   s6_addr[16];   /* IPv6 address */
    };

`AF_INET6` 值为10。

## socket API

### 头文件

    #include <sys/socket.h>

### 基本函数

- socket
- bind
- listen
- connect
- read/write
- send/recv
- sendto/recvfrom
- close
- getsockopt
- setsockopt

### IPv4 only函数

- gethostbyaddr
- gethostbyname
- inet_aton
- inet_ntoa

### IPv4/v6 通用函数

- getpeername
- getsockname
- getaddrinfo
- inet_ntop
- inet_pton
- htons/htonl
- ntohs/ntohl

## IPv4 to IPv4/v6

通常我们会遇到一些历史遗留代码不支持IPv6通信，因此我们需要对原代码进行一定的改造，让其支持IPv6。

Tips:

- 用 `getaddrinfo()` 来获取 `struct sockaddr` 的信息，而不是手动填写这个数据结构。这样就可以忽略 IP 的版本
- 找出全部与 IP 版本相关的代码，试着用一个有用的函数将它们封装起来
- 用 `addrinfo` 内 `ai_family` 对地址族进行判断，用来对那些跟 IP 版本相关的代码进行处理
- 通配地址 `INADDR_ANY` 和 `in6addr_any` 区别: 前者是一个宏定义，而后者是一个结构体

        #define INADDR_ANY      ((in_addr_t) 0x00000000) 
        extern const struct in6_addr in6addr_any;   /* :: */

- 在声明 `struct in6_addr` 时，`IN6ADDR_ANY_INIT` 的值可以做为初始值

        struct in6_addr ia6 = IN6ADDR_ANY_INIT;

- 使用 `inet_pton()` 替换 `inet_aton()` 或 `inet_addr()`
- 使用 `inet_ntop()` 替换 `inet_ntoa()`
- 使用 `getaddrinfo()` 取代 `gethostbyname()`
- 使用 `getnameinfo()` 取代 `gethostbyaddr()`
- 不要用 `INADDR_BROADCAST` 了，多使用 `IPv6 multicast` 来替换(这条还没使用过☺)

## 参考文献

- Linux man doc
- [http://www.cnblogs.com/tanghuimin0713/p/3425936.html](http://www.cnblogs.com/tanghuimin0713/p/3425936.html)
- [http://askubuntu.com/questions/414110/wheres-my-usr-include-sys-directory](http://askubuntu.com/questions/414110/wheres-my-usr-include-sys-directory)
- [http://blog.csdn.net/u012654882/article/details/51803228](http://blog.csdn.net/u012654882/article/details/51803228) 
- [http://beej.us/guide/bgnet/output/html/multipage/ip4to6.html](http://beej.us/guide/bgnet/output/html/multipage/ip4to6.html)