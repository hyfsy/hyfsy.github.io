---
layout: post
title: "IDEA console无法输出颜色问题"
date: 2020-10-27
excerpt: "展示问题的调试方法"
tags: [bug]
comments: false
---





问题描述：

设置了springboot日志输出相关的参数，但在idea的console中没有颜色输出



参数配置：

application.properties

```properties
spring.output.ansi.enabled=always
```



log4j2.xml

```xml
<Console name="Console" target="SYSTEM_OUT">
	<PatternLayout pattern="%clr{%d{yyyy-MM-dd HH:mm:ss,SSS}}{magenta} %clr{%pid}{blue} %clr{%5p} --- %cyan{%l} --- %m %n" />
</Console>
```





首先看了下`AnsiOutput`有没有输出ansi颜色相关的字符

![](../images/2020/10/27/001.png)

很明显，确实添加过颜色输出编码了，放main函数里自己跑一下，也是有颜色的，怎么放项目里输出就没有。。。

![](../images/2020/10/27/002.png)

![](../images/2020/10/27/003.png)

此时决定看下日志的输出逻辑，debug一直到上层log4j的输出逻辑

![](../images/2020/10/27/004.png)

找到输出流进行写流的位置，看到了一个没见过的输出流，打开一看，里面有个`PrintStream`，觉得应该是输出流的问题了。

`PrintStream`应该是标准输出流，被这个`AnsiPrintStream`包装了，就导致输出不出颜色了

随后查看其他项目有颜色输出的系统看这个对象的类型

![](../images/2020/10/27/005.png)

可以确定是输出流的问题了

看下创建过程

![](../images/2020/10/27/006.png)

![](../images/2020/10/27/007.png)

敲个断点没用（旁边的实现类不是）

看看outputStream对象的初始化过程，找到是在构造函数中初始化的

![](../images/2020/10/27/008.png)

竟然走了被废弃的构造函数。。。而且输出流已经被改变了

往上找传入逻辑

![](../images/2020/10/27/009.png)

![](../images/2020/10/27/010.png)

![](../images/2020/10/27/011.png)

找到包装流的地方了

那输出流应该就是这一大串逻辑返回的结果了

![](../images/2020/10/27/012.png)

结果感人，是直接拿的`System.out`的标准输出流。。。而且已经被改变了

![](../images/2020/10/27/013.png)

此时很蒙蔽

蒙蔽归蒙蔽，还是要解决问题，看下设置的地方

![](../images/2020/10/27/014.png)

然而这已经最底层了，再之前的代码堆栈已经点不进去了

![](../images/2020/10/27/015.png)

看到最后也不知道怎么传的，也解决不了问题，难过。。

然后就开始了百度、google之旅（当然百度一点用都没有）

搜索jansi相关的内容，都没用

只能分析下堆栈，考虑应该是这三个堆栈导致的，从main函数开始

![](../images/2020/10/27/016.png)

看看源码

![](../images/2020/10/27/017.png)

![](../images/2020/10/27/018.png)

看看out的初始化逻辑

![](../images/2020/10/27/019.png)

![](../images/2020/10/27/020.png)

![](../images/2020/10/27/021.png)

![](../images/2020/10/27/022.png)

硬核看源码

看看是不是这个项目动了啥配置

![](../images/2020/10/27/023.png)

对比自己的环境，都为false

![](../images/2020/10/27/024.png)

看来是强制指定AnsiPrintStream的，说明这个堆栈没有用了，看看触发点

![](../images/2020/10/27/025.png)

看到这觉得有点不对劲了

JANSI变量怎么就为true了，上方的注释额外醒目，3.5版本的maven存在？我的是3.2.5的，按道理来时应该没有这个类的



看下最后一个堆栈

![](../images/2020/10/27/026.png)

没用。。。

决定开始往maven的方向去考虑

![](../images/2020/10/27/027.png)

一口老血都要吐出来了

这啥玩意，google下

找到这个问题：https://youtrack.jetbrains.com/issue/IDEA-132822

说IDEA的maven插件有问题，AnsiPrintStream无法输出颜色的问题



转来转去竟然死在这种地方，特写此文章谨记一下

PS：新项目出这种问题，老项目好的，主要是记得以前设置过IDEA新项目的maven配置，就没关注过maven配置，结果一看傻眼了

![](../images/2020/10/27/028.png)




> google的力量是真的强