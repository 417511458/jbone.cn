---
title: JDK、JRE、JVM的概念和关系你真搞懂了吗？《专家解题1000例》
type: interview
order: 1
---

面试官：”请说下 `Java` 中的 `JDK` 、`JRE` 、`JVM` 这些基本概念和关系！“

此题是考察 Java 程序员对 `Java` 中 `JDK` 基本结构的理解和掌握程度，属于 Java 程序员必须掌握的基本知识。

可惜的是，很多开发经验很丰富的 Java 程序员突然遇到此题时，也不能很清晰的回答出来。原因可能是由于基础不牢固，或者随着开发经验的增长，早就忘了这些基础知识。

本文就依据官网的 `JDK` 结构图，详细解读 `JDK` 、`JRE` 和 `JVM` 的概念和基本关系。

# JDK 结构

下面是 `JDK` 官方的结构图。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/61d3e419b3154f89a0ee00459562b9ed~tplv-k3u1fbpfcp-watermark.image?)

从此图中，可以很清晰的看出，`JDK` 包括 `Java Language`、`Tools` 和 `JRE`。`JRE` 包括 `JVM` 和其它类库。

**所以说，从结构上看，`JDK`、`JRE` 和 `JVM` 由大到小的包含关系。**

# 基本概念和作用

下面看`JDK`、`JRE` 和 `JVM`的概念和作用。

## JDK
JDK 全称 `Java Development Kit`，即Java开发者工具包，它是支撑Java程序的所有基础设施（Java编译器、Java运行时环境、Java类库等）的超集，它提供了编译和运行 Java 程序所需的环境、工具和资源。

## JRE

JRE 全称 `Java Runtime Environment`，即Java运行时环境，它的主要作用是解释和运行Java 字节码文件。从这个定位可以看出，如果只是运行 Java 程序，只需要安装 JRE 就够了，而如果是开发，就必须要安装 JDK 。

## JVM

JVM 全称 `Java Virtural Machine`，即常说的 Java 虚拟机。它是 Java 实现跨平台最核心的部分，负责解释执行字节码文件。

虚拟机，顾名思义是虚拟的计算机。Java 虚拟机，即 Java 虚拟计算机。它就像一个计算机一样，运行在真实的计算机上，并在 Java 虚拟机之上通过解释和执行字节码文件，运行 Java 程序。

Java程序的编译和运行过程，如下图所示：

![Java程序运行过程.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2e27e3105638432b9be484c4983ab2fe~tplv-k3u1fbpfcp-watermark.image?)

当使用 Java 编译器编译 Java 文件时，生成的是与平台无关的字节码，这些字节码是 JVM 可识别、解释和运行的专用文件。就这样，Java 通过在不同的计算机平台上实现支持不同平台的 JVM ，就实现了跨平台的目的，一次编码，各平台都可以运行。

# 总结

本文通过 JDK 官网的结构图，剖析了`JDK`、`JRE` 和 `JVM`三者之间的关系。从结构上看，`JDK`、`JRE` 和 `JVM` 是由大到小的包含关系。从功能定位上看，`JDK`是支撑 Java 程序的基础设施的超集（这里的基础设施，指的是Java实现的部分，不包括计算机软硬件等其他部分）；`JRE` 是Java 程序的运行环境； `JVM` 是 Java 虚拟的计算机， 是 Java 程序和平台之间的桥梁，是跨平台的核心组件。




