---
layout: post
title:  "RubyProgramming CH3"
date:   2015-10-07 21:00:00
categories: Ruby
excerpt: study notes about RubyProgramming
---

* content
{:toc}


## 类的基础
---
*   创建一个基础的类，通常会包括一个方法：initialize。当调用new方法创建一个新的对象的时候，Ruby首先会分配一些内存来保存为初始化的对象，然后调用对象的initialize方法，并把调用new时所使用的参数传入该方法。

        1.9.3-p551 :036 > class Song
        1.9.3-p551 :037?>   def initialize(name, artist, duration)
        1.9.3-p551 :038?>     @name = name
        1.9.3-p551 :039?>     @artist = artist
        1.9.3-p551 :040?>     @duration = duration
        1.9.3-p551 :041?>   end
        1.9.3-p551 :042?> end
         => nil 
        1.9.3-p551 :043 > song = Song.new("Bicyclops", "Fleck", 260)
         => #<Song:0x83b2204 @name="Bicyclops", @artist="Fleck", @duration=260> 
        1.9.3-p551 :050 > song.inspect
         => "#<Song:0x83b2204 @name=\"Bicyclops\", @artist=\"Fleck\", @duration=260>" 

*   inspect方法(可以发送给任何对象)默认将对象的ID和实例变量格式化。
*   当需要自定义消息的时候，inspect方法并不实用。Ruby中有一个标准消息(message)to_s，它可以发送给任何一个想要输出字符串表示的对象。

        1.9.3-p551 :047 > song.to_s
         => "#<Song:0x83b2204>" 

*   在Ruby中，类永远不是封闭的(Ruby源码都是开源的)：你总是可以向一个已有的类中添加方法，这既适用于自己编写的类，也适用于标准的内建(built-in)的类。只要打开某个已有的类的类定义，就可以将指定的新内容添加进去，同时，原有的方法依然存在。当然，如果是从头编写代码，最好还是将所有的方法放在同一个类定义里面。

## 继承和消息
---

*   继承(inheritance)允许你创建一个类，做为另外一个类的精炼和特化：

        1.9.3-p551 :058 > class KaraokeSong < Song
        1.9.3-p551 :059?>   def initialize(name, artist, duration, lyrics)
        1.9.3-p551 :060?>     super(name, artist, duration)
        1.9.3-p551 :061?>     @lyrics = lyrics
        1.9.3-p551 :062?>   end
        1.9.3-p551 :063?> end


*   类定义一行中的**"< song"**告诉Ruby，KaraokeSong是Song的子类(subclass)，那么Song就是KaraokeSong的超类(superclass)或者父类。
*   Ruby方法调用会在程序初始解析的时候先查找当前类里定义的方法，若在当前类里没有找到，则将判定推迟到程序运行时进行。在那时，Ruby查看其父类中的方法，然后是祖父类，依次追溯整个祖先链。若始终没有找到合适的方法，Ruby通常会导致一个错误。
*   如果在定义一个类的时候没有指定其父类，则Ruby默认以Object类作为其父类。这意味这所有类的始祖都是Object，并且Object的实例方法对Ruby所有对象都可以用。例如to_s。我们也可以为to_s定制新特性，覆写Object里的to_s:

        1.9.3-p551 :051 > class Song
        1.9.3-p551 :052?>   def to_s
        1.9.3-p551 :053?>     "Song: #@name--#@artist (#@duration)"
        1.9.3-p551 :054?>   end
        1.9.3-p551 :055?> end
         => nil 
        1.9.3-p551 :056 > song.to_s
         => "Song: Bicyclops--Fleck (260)" 

*   Ruby关键字super，当调用super而不使用参数时，Ruby向当前对象的父类发送一个消息，要求它调用子类中的同名方法。Ruby将我们原先调用方法时的参数传递给父类的方法。

        1.9.3-p551 :064 > karaok = KaraokeSong.new("My Way", "Sinatra", 225, "And now, the...")             #当前类没有调用父类中to_s方法
         => Song: My Way--Sinatra (225) 

        1.9.3-p551 :065 > class KaraokeSong < Song
        1.9.3-p551 :066?>   def to_s
        1.9.3-p551 :067?>     super + " #@lyrics "
        1.9.3-p551 :068?>   end
        1.9.3-p551 :069?> end
         => nil 
        1.9.3-p551 :070 > karaok.to_s
         => "Song: My Way--Sinatra (225) And now, the... " 

*   Ruby是一种单继承语言，只能有一个直接的父类。类似的有Java，C#。相反的是C++，支持多继承。Ruby类可以从任何数量的**mixin**(类似于一种部分的类定义)中引入(include)功能，这提供了可控的、类似多继承的能力，而且没有多继承继承结构混乱的缺点。

## 对象和属性
---
*   一个对象的外部可见部分称其为**属性(attribute)**。

### 访问对象的状态
---
*   默认状况下，对象内部的状态是私有的，即其它对象无法访问当前对象的实例变量。我们可以**定义一些方法**来访问及操作对象的状态：

        1.9.3-p551 :071 > class Song
        1.9.3-p551 :072?>   def name
        1.9.3-p551 :073?>     @name
        1.9.3-p551 :074?>   end
        1.9.3-p551 :075?>   def artist
        1.9.3-p551 :076?>     @artist
        1.9.3-p551 :077?>   end
        1.9.3-p551 :078?>   def duration
        1.9.3-p551 :079?>     @duration
        1.9.3-p551 :080?>   end
        1.9.3-p551 :081?> end
         => nil 
        1.9.3-p551 :082 > song.name
         => "Bicyclops" 
        1.9.3-p551 :083 > song.artist
         => "Fleck" 
        1.9.3-p551 :084 > song.duration
         => 260 

*   同时，Ruby也提供一种方便的快捷方式：attr_reader创建这些访问方法：

        1.9.3-p551 :085 > class KaraokeSong
        1.9.3-p551 :086?>   attr_reader :lyrics, :name, :artist
        1.9.3-p551 :087?>   end
         => nil 
        1.9.3-p551 :088 > karaok.lyrics
         => "And now, the..." 
        1.9.3-p551 :089 > karaok.name
         => "My Way" 
        1.9.3-p551 :090 > karaok.artist
         => "Sinatra" 

*   :lyrics 是一个表达式，它返回对应lyrics的一个Symbol对象。

### 可写的对象
---
*   通过创建一个名字以等号结尾的方法实现某个属性的可写：

        1.9.3-p551 :093 > class Song
        1.9.3-p551 :094?>   def duration=(new_duration)
        1.9.3-p551 :095?>     @duration = new_duration
        1.9.3-p551 :096?>     end
        1.9.3-p551 :097?>   end
         => nil 
        1.9.3-p551 :098 > song.duration
         => 260 
        1.9.3-p551 :099 > song.duration = 342
         => 342 
        1.9.3-p551 :100 > song.duration
         => 342 

*   同样，可以用attr_writer快捷设置可写属性：

        1.9.3-p551 :101 > class Song
        1.9.3-p551 :102?>   attr_writer :name
        1.9.3-p551 :103?>   end
         => nil 
        1.9.3-p551 :104 > song.name
         => "Bicyclops" 
        1.9.3-p551 :105 > song.name = "Lemontree"
         => "Lemontree" 
        1.9.3-p551 :106 > song.name
         => "Lemontree" 

### 虚拟属性
---
*   所谓的虚拟属性就是利用属性方法在类内部创建了一个虚拟的实例变量，而实际上在内部并没有对应的实例变量。

        1.9.3-p551 :107 > class Song
        1.9.3-p551 :108?>   def duration_in_minute=(new_duration)
        1.9.3-p551 :109?>     @duration = (new_duration*60).to_i
        1.9.3-p551 :110?>   end
        1.9.3-p551 :111?> end
        1.9.3-p551 :112 > song.duration_in_minute=4.2
         => 4.2 
        1.9.3-p551 :113 > song.duration
         => 252 

## 类变量和类方法
---
*   实例变量是针对对象定义的，对于同一个类中的不同对象实例变量在多数情况下会取到不同的值，而且互相之间不能访问(除非添加访问权限)。而类变量则可以被类的所有对象共享，对于一个给定的类来说，类变量仅仅存在一份拷贝。类方法则是不束缚于任何对象的方法，如new。

### 类变量
---
*   类变量由@@开头，在使用前必须被初始化。

        1.9.3-p551 :114 > class Song
        1.9.3-p551 :115?>   @@plays=0
        1.9.3-p551 :116?> end

### 类方法
---
*   类方法和实例方法通过它们的定义区分开来，最常用的是通过在方法名之前放置类名以及一个句点，来定义类方法。同时还有很多方法定义类方法。

        1.9.3-p551 :117 > class Demo
        1.9.3-p551 :118?>   def Demo.meth1
        1.9.3-p551 :119?>     # ...
        1.9.3-p551 :120 >   end
        1.9.3-p551 :121?>   def self.meth2
        1.9.3-p551 :122?>     # ...
        1.9.3-p551 :123 >   end
        1.9.3-p551 :124?>   class << self
        1.9.3-p551 :125?>     def meth3
        1.9.3-p551 :126?>       # ...
        1.9.3-p551 :127 >     end
        1.9.3-p551 :128?>   end
        1.9.3-p551 :129?> end

### 单件与其它构造函数
---
*   通过把原方法标为私有(private)，可以阻止所有人访问这个方法。

        1.9.3-p551 :130 > class Song
        1.9.3-p551 :131?>   private_class_method :new
        1.9.3-p551 :132?>   end
         => Song 
        1.9.3-p551 :133 > song1 = Song.new("Hello", "Justin", 432)
        NoMethodError: private method `new' called for Song:Class
            from (irb):133
            from /usr/local/rvm/rubies/ruby-1.9.3-p551/bin/irb:12:in `<main>'

*   通过类方法使用类变量，可以让整个类覆写同一个对象实例，即单件。
*   使用类方法作为伪构造函数(pseudo-constructors)，可以让编程更加简洁。

        1.9.3-p551 :145 > class Shape
        1.9.3-p551 :146?>   def initialize(num_sides, perimeter)
        1.9.3-p551 :147?>     # ...
        1.9.3-p551 :148 >       end
        1.9.3-p551 :149?>   def Shape.triangle(side_length)     #伪构造
        1.9.3-p551 :150?>     Shape.new(3, side_length*3)
        1.9.3-p551 :151?>     end
        1.9.3-p551 :152?>   def Shape.square(side_length)       #伪构造
        1.9.3-p551 :153?>     Shape.new(4, side_length*4)
        1.9.3-p551 :154?>     end
        1.9.3-p551 :155?>   end

## 访问控制
---
*   设计类的接口时，要着重考虑**类向外暴露的程度**。如果允许了过度的访问，会增加应用中耦合的风险——类的用户可能会严重依赖于类实现的细节，而不是逻辑性的接口。
*   在Ruby中，对象是默认私有的，要想改变对象的访问权限得通过改变它的方法。因此，**控制对方法的访问，就控制了对对象的访问**。
*   一个经验法则是，**永远不要暴露会导致对象处于无效状态下的方法**。Ruby对方法提供了三种权限：
    -   **Public** 方法可以被任何人调用，没有访问权限控制。方法默认是public的(initialize除外，它是private的)。
    -   **Protected** 方法只能被定义了该方法的类及其子类的对象所调用。整个家族均可访问。
    -   **Private** 方法不能被明确的接收者调用，其接收者只能是self。这意味着private方法只能在当前**对象**的上下文中被调用；你不能调用另一个对象的private方法，即便与调用者属于同一个类。
*   访问控制是在程序运行时动态判定的，只有当代码执行一个受限的方法，才会返回一个违规。

        1.9.3-p551 :156 > class Song
        1.9.3-p551 :157?>   def method1
        1.9.3-p551 :158?>   end
        1.9.3-p551 :159?>   protected
        1.9.3-p551 :160?>     def method2
        1.9.3-p551 :161?>     end
        1.9.3-p551 :162?>   private
        1.9.3-p551 :163?>     def method3
        1.9.3-p551 :164?>     end
        1.9.3-p551 :165?>   public
        1.9.3-p551 :166?>     def method4
        1.9.3-p551 :167?>     end
        1.9.3-p551 :168?> end
         => nil 
        1.9.3-p551 :169 > song.method1
         => nil 
        1.9.3-p551 :170 > song.method2
        NoMethodError: protected method `method2' called for Song: Lemontree--Fleck (252):Song
            from (irb):170
            from /usr/local/rvm/rubies/ruby-1.9.3-p551/bin/irb:12:in `<main>'
*   ps：不知道为啥protected方法不能访问，待解决???......

### 指定访问控制
---
*   通过将方法名作为参数列表传入访问控制函数来设置它们的访问级别。

        1.9.3-p551 :198 > class Song
        1.9.3-p551 :199?>   public :method1, :method2
        1.9.3-p551 :200?>   protected :method3
        1.9.3-p551 :201?>   private :method4
        1.9.3-p551 :202?> end

## 变量
---
*   变量保存的是对象引用而不是对象本身。因此，改变对象的值会导致所有引用它的变量的值发生改变(赋值别名对象)。

        1.9.3-p551 :203 > person1 = "Tim"
         => "Tim" 
        1.9.3-p551 :204 > person2 = person1
         => "Tim" 
        1.9.3-p551 :205 > person1[1] = 'a'
         => "a" 
        1.9.3-p551 :206 > person1
         => "Tam" 
        1.9.3-p551 :207 > person2
         => "Tam" 

*   为了避免赋值别名对象，可以通过使用String的dup方法来避免创建别名，它会创建一个新的、具有相同内容的String对象。

        1.9.3-p551 :208 > person3 = person1.dup
         => "Tam" 
        1.9.3-p551 :209 > person1[0] = 'J'
         => "J" 
        1.9.3-p551 :210 > person1
         => "Jam" 
        1.9.3-p551 :211 > person2
         => "Jam" 
        1.9.3-p551 :212 > person3
         => "Tam" 

*   或者可以通过冻结一个对象来阻止其它人对其进行改动。试图更改一个被冻结的对象会引发(raise)一个TypeError异常(**实操显示是RuntimeError...=.=!**)。

        1.9.3-p551 :213 > person1.freeze
         => "Jam" 
        1.9.3-p551 :215 > person2[2] = 'g'
        RuntimeError: can't modify frozen String
            from (irb):215:in `[]='
            from (irb):215
            from /usr/local/rvm/rubies/ruby-1.9.3-p551/bin/irb:12:in `<main>'


