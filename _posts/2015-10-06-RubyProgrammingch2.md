---
layout: post
title:  "RubyProgramming CH2"
date:   2015-10-06 21:35:54
categories: Ruby
excerpt: study notes about RubyProgramming
---

* content
{:toc}

## 基本概念
---
*   类 class
*   状态 state
*   方法 method
    -   def定义 end结束
*   实例 instances
*   对象 object == 类的实体 class instance
*   对象标识符 object ID 唯一
*   实例变量 instance variables
*   实例方法 instance method

## ruby变量命名规则
---
*   局部变量、方法参数、方法名称必须以小写字母或下划线开始。
*   全局变量都有$为前缀。
*   实例变量以@开始。
*   类变量以@@开始。
*   类名称、模块名称和常量必须以一个大写字母开始。
*   包含多个单词的实例变量名称在词与词之间使用下划线连接。
*   包含多个单词的类变量名称使用混合大小写（每个单词首字母大写）。
*   方法名称可以以?、!、=字符结束。

## 数组和散列表
---
*   数组(arrays)用[]，散列表(hashes)用{}。
*   数组用整数做索引，散列表支持以任何对象做为键(key)。
*   散列表也用方括号表示法包括键来进行索引。

## 控制结构
---
*   如果if 和 while 语句的程序体只是一个表达式，可以使用Ruby的**语句修饰符**。
   
        if radiation > 3000  
            puts "Danger, will Robinson"  
        end

*   用语句修饰符表示：

        puts "Danger, will Robinson" if radiation > 3000

## 正则表达式
---
*   在Ruby中，通常在斜线间创建正则表达式：

        /Perl|Python/

*   在Ruby中，正则表达式也是一个对象，类型名为 Regexp：

        1.9.3-p551 :008 > p = /Perl|Python/
         => /Perl|Python/   
        1.9.3-p551 :009 > p.class
         => Regexp 

*   =~匹配操作符可以用正则表达式匹配字符串。如果在字符串中发现模式，=~返回模式的开始位置，否则返回nil。!~ 是不匹配操作符：

        1.9.3-p551 :014 > name = 'Perl'
         => "Perl" 
        1.9.3-p551 :015 > name =~ p
         => 0 
        1.9.3-p551 :016 > name = 'Ruby'
         => "Ruby" 
        1.9.3-p551 :017 > name =~ p
         => nil 
        1.9.3-p551 :018 > name !~ p
         => true 

*   正则表达式匹配到的部分，可以使用Ruby的其中一种替换方法，替换为其他文本：

        1.9.3-p551 :019 > line = 'Perl, Python, Perl and Python'
         => "Perl, Python, Perl and Python" 
        1.9.3-p551 :020 > line.sub(/Perl/, 'Ruby')          #替换第一个匹配字符
         => "Ruby, Python, Perl and Python" 
        1.9.3-p551 :021 > line.gsub(/Python/, 'Ruby')       #替换所有的匹配字符
         => "Perl, Ruby, Perl and Ruby" 
        1.9.3-p551 :022 > line.gsub(/Python|Perl/, 'Ruby')
         => "Ruby, Ruby, Ruby and Ruby" 

## Block和迭代器
---
*   Block是一种可以和方法调用相关联的代码块，可以实现**回调，传递一组代码，实现迭代器**，是Ruby的一个独特特性。
*   约定俗成，单行block用花括号，多行block用do/end。

        { puts "Hello"}                 

        do                              
          club.enroll(person)
          person.socialize
        end

*   把block放在含有方法调用的源码行的结尾处，就能实现调用，如果方法有参数，它们出现在block之前。

        greet { puts "Hi" }
        verbose_greet("Dave", "loyal customer") { puts "Hi" }

*   在Ruby库中大量使用了block来实现迭代器。

## 读写文件
---
*   puts输出它的参数，并在每个参数后面添加回车换行符；print输出它的参数，但不会添加回车换行符；printf在一个格式化字符串控制下打印它的参数，类似于C中的printf：

        1.9.3-p551 :026 > printf("Number : %5.2f, \nString: %s\n", 1.23, "hello")
        Number :  1.23, 
        String: hello

*   gets从程序的标准输入流中读取下一行。

## 其它
---
*   Ruby对单引号字符串处理得很少，除了极少一些例外，键入到字符串字面量的内容就构成了这个字符串的值；而对双引号字符串有更多的处理。首先，它寻找反斜线开始的序列，并用二进制值替换它们；字符串内的表达式内插 #{表达式}序列会被表达式的值给替换。
*   Ruby方法缩返回的值，是最后一个被求值的表达式的值。

        1.9.3-p551 :027 > $greeting = 'hello'
         => "hello" 
        1.9.3-p551 :028 > @name = 'sir'
         => "sir" 
        1.9.3-p551 :029 > puts "#greeting, #name"
        #greeting, #name
         => nil 
        1.9.3-p551 :030 > puts "#$greeting, #@name"
        hello, sir
         => nil 
        1.9.3-p551 :031 > puts '#$greeting, #@name'
        #$greeting, #@name
         => nil 









