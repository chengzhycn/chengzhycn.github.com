---
layout: post
title:  "vfscanf.c: No such file or directory."
date:   2016-04-05 22:23:00
categories: C/C++
excerpt:    Segmentation fault
---

* content
{:toc}

在运行如下程序时遇到了segmentation fault的问题：

    char  * str;
    for (int ix = 0; ix < count; ix++){
        switch (buf_o[ix][0]){
            case '[':
                secNum++;
                sscanf (buf_o[ix], "%*1c%[^]]", str);
                //strcpy (n->dstName[secNum], str);
                n->dstName[secNum] = strdup(str);
                break;
            case 'e':
                sscanf (buf_o[ix], "%*[^=]=%s", str);
                //strcpy (n->dstEid[secNum], str);
                n->dstEid[secNum] = strdup(str);
                break;
            case 'i':
                sscanf (buf_o[ix], "%*[^=]=%s", str);
                //strcpy (n->dstIp[secNum], str);
                n->dstIp[secNum] = strdup(str);
                break;
            default:
                break;
        }
        printf("dstname[%d]=%s\n", secNum, n->dstName[secNum]);
        
用clang编译能完整运行，但是发现n->dstName[0]的值会随着每次运行而改变：

    [node1]
    dstname[0]=node1
    eid=ipn1:1
    dstname[0]=ipn1:1
    ip=10.0.0.1
    dstname[0]=10.0.0.1
    [node2]
    dstname[1]=node2
    eid=ipn2:1
    dstname[1]=ipn2:1
    ip=10.0.2.1
    dstname[1]=10.0.2.1
    33333333333
    2222333333
    11111111111
    10.0.2.1, ipn1:1, 10.0.0.1
    node2, ipn2:1, 10.0.2.1

改用gcc编译运行，在输出第一个[node1]后，会直接报段错误：

    [node1]
    Segmentation fault

用gdb加断点调试后，发现报错原因：

    Program received signal SIGSEGV, Segmentation fault.
    0x00007ffff7a6c61e in _IO_vfscanf_internal (s=s@entry=0x7fffffffd840, format=format@entry=0x401278 "%*1c%[^]]", 
        argptr=argptr@entry=0x7fffffffd968, errp=errp@entry=0x0) at vfscanf.c:2895
    2895	vfscanf.c: No such file or directory.

在stackoverflow上找到类似问题，怀疑有可能是指针str未分配内存空间导致，遂将

    char  * str;
    改写为
    char    str[MAXLINELEN];
    
再次编译运行，问题解决。综上，该段错误应该是访问了非法的内存地址导致的，因此使用指针要非常小心，另外加强对C语言存储空间分配的学习。

