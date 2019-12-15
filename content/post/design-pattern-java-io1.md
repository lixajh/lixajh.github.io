---
title: "Java IO中使用最多的最典型的--装饰器模式"
author: "lix"
cover: "/images/cover.jpg"
tags: ["tagA", "tagB"]
date: 2019-12-16T01:29:39+08:00
draft: false
---

Java IO中使用最多的最典型的是 装饰器模式。

<!--more-->

### Java IO中使用最多的最典型的是 装饰器模式。

#### 好处：

在由InputStream、OutputStream、Reader和Writer代表的等级结构内部，有一些流处理器可以对另一些流处理器起到装饰作用，形成新的、具有改善了的功能的流处理器。

由于java I/O库需要很多性能的各种组合，如果这些性能都是用继承来实现，那么每一种组合都需要一个类，这样就会造成大量行重复的类出现。如果采用装饰模式，那么类的数目就会大大减少，性能的重复也可以减至最少。因此装饰模式是java I/O库基本模式。装饰模式的引进，造成灵活性和复杂性的提高。因此在使用 java I/O 库时，必须理解java I/O库是由一些基本的原始流处理器和围绕它们的装饰流处理器所组成的。

InputStream有七个直接的具体子类，有四个属于FilterInputStream的具体子类，如下图所示：

![](https://raw.githubusercontent.com/lixajh/picbed/master/img/144836_s4jc_123079.jpg)

#### 原始流处理器 - 被装饰者 其中InputStream抽象类就是被装饰者和装饰器共同需要继承的Component

原始流处理器接收一个Byte数组对象、String对象、FileDescriptor对象或者不同类型的流源对象（就是前面所说的原始流源），并生成一个InputStream类型的流对象，在装饰器模式中，他们是需要装饰的原始数据。在InputStream类型的流处理器中，原始流处理器包括以下四种：

1. **ByteArrayInputStream**：为多线程的通讯提供缓冲区操作工作，接受一个Byte数组作为流的源。
2. **FileInputStream**：建立一个与文件有关的输入流。接受一个File对象作为流的源。
3. **PipedInputStream**：可以和PipedOutputStream配合使用，用于读入一个数据管道的数据。接受一个PipedOutputStream作为源。
4. **StringBufferInputStream**：将一个字符串缓冲区抓换为一个输入流。接受一个String对象作为流的源。
        与原始流处理器相对应的是链接流处理器。

#### 链接流处理器 - 装饰器，其中FilterInputStream是装饰器的抽象，实际上也不一定非要抽象，ObjectInputSteam和SequenceInputSteam就没有继承FilterInputSteam，他们仍然是一个装饰器

所谓链接流处理器就是可以接受另一个(同种类的）流对象(就是链接流源）作为流源，并对之进行功能扩展的类。InputStream类型的链接流处理器包括以下几种，它们接受另一个InputStream对象作为流源，在装饰器模式中他们就是装饰器。

1. **FilterInputStream**称为过滤输入流，它将另一个输入流作为流源。这个类的子类包括以下几种：
   - BufferInputStream：用来从硬盘将数据读入到一个内存缓冲区中，并从此缓冲区提供数据。
   - DateInputStream**：**提供基于多字节的读取方法，可以读取原始数据类型的数据。
   - LineNumberInputStream**：**提供带有行计算功能的过滤输入流。       
   - PushbackInputStream： 提供特殊的功能，可以将已读取的直接“推回”输入流中。
2. **ObjectInputStream** 可以将使用ObjectInputStream串行化的原始数据类型和对象重新并行化。
3. **SequenceInputStream**可以将两个已有的输入流连接起来，形成一个输入流，从而将多个输入流排列构成一个输入流序列。
4. 用户也可以继承FilterInputStream实现自己的装饰器，如在java.util.zip包中就有ZipInputStream间接继承了ZipInputStream。

#### 一个有意思的问题

为什么ObjectInputStream看起来明明也是一个装饰器，却没有继承FilterInputStream呢？在内网看到一个[2004年的回答](https://bbs.csdn.net/topics/60476084):

> 或许跟版本历史有关，其他filter stream都是jdk1.0加入的，这个ObjectInputStream是jdk1.1加入的。同时，在jdk1.1中加入了整个Reader/Writer体系，你会发现Reader/Writer体系中的装饰类都没有继承FilterReader/FilterWriter，而是直接继承Reader/Writer。这就是设计理念的不同。设计者或许认为FilterReader/FilterWriter应该纯粹地作为用户自己设计decorator的基类。

在国外[一本书](https://books.google.com.hk/books?id=TnU_BAAAQBAJ&pg=PA310&lpg=PA310&dq=ObjectInputStream+decorator&source=bl&ots=V_v7re3yrb&sig=ACfU3U1-CYOGbiMmvAPYknL893MMvXgvvg&hl=zh-CN&sa=X&ved=2ahUKEwjrsKXKrqvmAhWRUN4KHW4pCHMQ6AEwAXoECAoQAQ#v=onepage&q=ObjectInputStream%20decorator&f=false](https://books.google.com.hk/books?id=TnU_BAAAQBAJ&pg=PA310&lpg=PA310&dq=ObjectInputStream+decorator&source=bl&ots=V_v7re3yrb&sig=ACfU3U1-CYOGbiMmvAPYknL893MMvXgvvg&hl=zh-CN&sa=X&ved=2ahUKEwjrsKXKrqvmAhWRUN4KHW4pCHMQ6AEwAXoECAoQAQ#v=onepage&q=ObjectInputStream decorator&f=false))上说明虽然ObjectInputSteam没有继承FilterInputSteam但是他满足装饰器模式的条件(1.它直接或间接继承abstract component2.有一个构造器接收abstract component)，所以它确实是一个装饰器:

> ![](https://raw.githubusercontent.com/lixajh/picbed/master/img/20191216001323.png)

此外，stactoverflow也有[类似的讨论](https://stackoverflow.com/questions/2896992/shouldnt-objectinputstream-extend-filterinputstream),不过看起来他们也没有定论。

#### 参考文章：

- [廖雪峰Java教程 IO部分 Filter模式](https://www.liaoxuefeng.com/wiki/1252599548343744/1298364142452770)
- [Java IO中的设计模式](https://www.cnblogs.com/wxgblogs/p/5649933.html)