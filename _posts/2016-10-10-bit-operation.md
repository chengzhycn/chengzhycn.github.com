---
layout: post
title:  "位操作的一些小技巧"
date:   2016-10-10 20:23:00
categories: C/C++
excerpt:    位操作的一些小技巧
---

* content
{:toc}

在阅读《深入理解计算机系统》时，发现一些位操作有趣的技巧，记录如下：

## 交换x y的值

对于任一位向量 `a` ，有 `a^a=0`。应用这一属性，可以有：

    void inplace_swap(int *x, int *y) {
        *y = *x ^ *y;
        *x = *x ^ *y;
        *y = *x ^ *y;
    }

虽然没有性能上的优势，有助于熟悉位操作的一些细节。

## 利用移位操作提高程序性能

起因是npm内left-pad，该模块功能就是在字符串前padding一些东西到一定的长度。可以简化为将0快速增长到指定数字。原作者在接收到长度变量后选择用while循环一个个加，效率太低。而利用移位操作可以更迅速地实现该功能。

    // 原代码
    void leftpad(int length) {
        int i = length;
        int tmp = 0;
        while (i++ != length) {
            tmp++;
        }
    }
    
    // 改良代码
    void leftpad(int length) {
        int i = length;
        int tmp = 0;
        int tmp2 = 1;
        while (!i) {
            if (i & 1)
                tmp += tmp2;
            i >>= 1;
            tmp *= 2;   // 让tmp增长更快 
        }
    }    