---
layout: post
title: "Java应用中是否必须给Serializable Class加Version id"
"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## 问题:

**当Sonar报****"Serializable" classes should have a version id的critical警报时，是否可以忽略？**

## 简单答案

如果给每一个实现serializable的类及其子类加serialVersionUID不麻烦并且不影响代码优雅性的话，加上就好。

如果不想加，那么就要看没有serialVersionUID是否会在运行时出现错误。

## 长版

**Stack overflow/Java doc**

如果不显示声明serialVersionUID，则序列化运行时会自动生成一个serialVersionUID，但是自动生成的serialVersionUID对.class文件的内容非常敏感，可能会因编译器的实现不同而不同，从而导致在反序列化时产生InvalidClassExceptions.

“If a serializable class does not explicitly declare a serialVersionUID, then the serialization runtime will calculate a default serialVersionUID value for that class based on various aspects of the class, as described in the Java(TM) Object Serialization Specification. However, it is *strongly recommended* that all serializable classes explicitly declare serialVersionUID values, since the default serialVersionUID computation is highly sensitive to class details that may vary depending on compiler implementations, and can thus result in unexpected InvalidClassExceptions during deserialization. Therefore, to guarantee a consistent serialVersionUID value across different java compiler implementations, a serializable class must declare an explicit serialVersionUID value. It is also strongly advised that explicit serialVersionUID declarations use the private modifier where possible, since such declarations apply only to the immediately declaring class--serialVersionUID fields are not useful as inherited members. Array classes cannot declare an explicit serialVersionUID, so they always have the default computed value, but the requirement for matching serialVersionUID values is waived for array classes. ”

Ref: https://stackoverflow.com/questions/285793/what-is-a-serialversionuid-and-why-should-i-use-it

Ref: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/io/Serializable.html

## 定义

**序列化**（serialization）在[计算机科学](https://zh.wikipedia.org/wiki/計算機科學)的数据处理中，是指将[数据结构](https://zh.wikipedia.org/wiki/資料結構)或对象状态转换成可取用格式（例如存成文件，存于缓冲，或经由网络中发送），以留待后续在相同或另一台计算机环境中，能恢复原先状态的过程。在数据储存与发送的部分是指将一个[对象](https://zh.wikipedia.org/wiki/对象_(计算机科学))存储至一个[存储介质](https://zh.wikipedia.org/w/index.php?title=儲存媒介&action=edit&redlink=1)，例如[文件](https://zh.wikipedia.org/wiki/檔案)或是[存储器缓冲](https://zh.wikipedia.org/w/index.php?title=記憶體緩衝&action=edit&redlink=1)等，或者透过网络发送数据时进行编码的过程，可以是[字节](https://zh.wikipedia.org/wiki/字节)或是[XML](https://zh.wikipedia.org/wiki/XML)等格式。而[字节](https://zh.wikipedia.org/wiki/字节)的或[XML](https://zh.wikipedia.org/wiki/XML)编码格式可以还原完全相等的[对象](https://zh.wikipedia.org/wiki/对象_(计算机科学))。这程序被应用在不同[应用程序](https://zh.wikipedia.org/wiki/應用程式)之间发送[对象](https://zh.wikipedia.org/wiki/对象_(计算机科学))，以及服务器将[对象](https://zh.wikipedia.org/wiki/对象_(计算机科学))存储到[文件](https://zh.wikipedia.org/wiki/檔案)或[数据库](https://zh.wikipedia.org/wiki/資料庫)。相反的过程又称为[反序列化](https://zh.wikipedia.org/w/index.php?title=反序列化&action=edit&redlink=1)。

## 序列化和反序列化过程举例

<div style="text-align:center"><img src ="/assets/images/same-compiler-create-jar.png.png" style="height:300px;" /></div>
<div style="text-align:center"><img src ="/assets/images/different-compiler-create-jars.png" style="height:300px;" /></div>

由以上可见，
只要序列化和非序列化使用的jar包相同，并使用的JDK版本一致，则不可能产生序列化产生的文件在反序列化时出错。

只要序列化和非序列化使用的jar包相同，即使使用的JDK版本不一致，也基本不可能产生序列化产生的文件在反序列化时出错，因为序列化和反序列化的库已经稳定，在不同版本的jdk中非常低概率有差异。

## 结论

如果序列化的产物是缓存，序列化和反序列化一定在同一个进行时的process，使用的jar包是同一个，不存在反序列化时因为自动生成不同serialVersionUID而导致反序列化失败的问题。

如果序列化的产物是xml格式文件，由于是标准格式的文件，只要序列化和反序列化使用的jar包没有错误，序列化产生的xml文件在反序列化时至少不会产生InvalidClassException。（试一下）

如果序列化的产物是字节的方式，有可能出现因编译方式不同而导致的反序列化产生InvalidClassException问题。

## 相关定义

**Java编译器**：JAVA编译器（javac.exe）的作用是将java源程序编译成中间代码字节码（Byte code）文件，是最基本的开发工具。

**字节码文件**：字节码文件（Byte code）是一种和任何具体机器环境及操作系统环境无关的中间代码，它是一种二进制文件。如.class文件，或者包含在jar包，war包等包中的.class文件。

Java解释器： Java解释器负责将字节码文件翻译成具体硬件环境和操作系统平台下的机器代码，以便执行。因此Java程序不能直接运行在现有的操作系统平台上，它必须运行在被成为Java虚拟机（JVM）的软件平台之上。

JDK: The **Java Development Kit** (**JDK**) is an implementation of either one of the [Java Platform, Standard Edition](https://en.wikipedia.org/wiki/Java_Platform,_Standard_Edition), [Java Platform, Enterprise Edition](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition), or [Java Platform, Micro Edition](https://en.wikipedia.org/wiki/Java_Platform,_Micro_Edition) platforms[[1\]](https://en.wikipedia.org/wiki/Java_Development_Kit#cite_note-1) released by [Oracle Corporation](https://en.wikipedia.org/wiki/Oracle_Corporation) in the form of a binary product aimed at [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) developers on [Solaris](https://en.wikipedia.org/wiki/Solaris_(operating_system)), [Linux](https://en.wikipedia.org/wiki/Linux), [macOS](https://en.wikipedia.org/wiki/MacOS) or [Windows](https://en.wikipedia.org/wiki/Windows). The JDK includes a private JVM and a few other resources to finish the development of a Java Application
