# Java语言概述

## jdk、jvm、jre

### 什么是 JDK?

JDK 的英文全称是 Java Development Kit。JDK是用于制作程序和Java应用程序的软件开发环境。Java 开发人员可以在 Windows、macOS、Solaris 和 Linux 上使用，是一个跨平台编程语言。JDK 帮助他们编写和运行 Java 程序。可以在同一台计算机上安装多个 JDK 版本。

### 什么是 JRE?

JRE 的英文全称是 Java Runtime Environment。JRE 是一个旨在运行其他软件的软件(有点绕口)。它包含类库、加载器类和 JVM。简单来说，如果你想运行 Java 程序，你需要 JRE。如果您不是程序员，则无需安装 JDK，只需安装 JRE 即可运行 Java 程序。不过，所有 JDK 版本都与 Java Runtime Environment 捆绑在一起，因此无需在 PC 单独下载和安装 JRE。JRE 的完整形式是 Java 运行时环境。

### 什么是JVM?

JVM 的英文全称是Java Virtual Machine。JVM 是一个引擎，它提供运行时环境驱动 Java 代码或应用程序。它将 Java 字节码转换为机器语言。JVM 是 Java 运行环境 (JRE) 的一部分。它不能单独下载和安装。要安装 JVM，您需要安装 JRE。JVM的就是Java虚拟机。

在许多其他编程语言中，编译器为特定系统生成机器代码。但是，Java 编译器则称为 JVM 虚拟机生成代码。

### 关系

<img src="https://s3.51cto.com/oss/202202/15/34bd93945111189caf3351ac3c0af3b92a6eed.jpg" alt="img" style="zoom:50%;" />



<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/Java8.0_platform.jpg" style="zoom: 67%;" />

## 执行过程

<img src="https://cuicy-1314839020.cos.ap-shanghai.myqcloud.com/typora-user-images/image-20230128111332725.png" alt="image-20230128111332725" style="zoom:50%;" />