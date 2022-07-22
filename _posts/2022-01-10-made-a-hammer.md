---
layout: post
title: "造了个锤子"
date: 2022-01-10
excerpt: "远程服务器的热刷新"
tags: [java]
comments: false
project: true
---





# 1. 引言

用IDEA的同学都应该知道，它里面有个小锤子，就是这个，点击后本地的代码会自动执行热刷新。

![](../images/2022/01/10/001.png)

由于使用JRebel启动系统太慢了，我一般用的这个小锤子，虽然功能没JRebel强大，但完全够用了。然而这个锤子有个缺点，需要手动锤它一下才会热刷新。

某天，我望着IDEA的小锤子就突然有了灵感，有没有方法能给我自动的热刷新，如果能实现的话，那远程的服务器上是不是也可以做到这种效果，因为远程的服务器和本地相比就多了一次请求调用。

和部门内**某人**讨论了一下这个想法，结果被喷的一无是处，最后这个玩具只能拿出来给大伙儿玩玩，渴望在这冷漠的世界里寻找一点心灵的慰藉。



# 2. 介绍

IDEA小锤子的远程版

本地修改代码后，远程服务器自动热刷新，效果实时生效

欢迎PR :)



# 3. 使用

1、需要热刷新的应用系统引入依赖：

```xml
<dependency>
    <groupId>io.github.hyfsy</groupId>
    <artifactId>hot-refresh-server-all</artifactId>
    <version>1.2.2</version>
</dependency>
```

2、启动应用系统

3、获取本地服务器部署包：[hot-refresh-client.zip](https://github.com/hyfsy/hot-refresh/releases)

4、解压后，启动本地服务器：

```bash
bin/hot.cmd -s http://localhost:8080
```

- `-h`：本地编写代码的工作目录（默认当前命令行目录）
- `-s`：需要热刷新的应用系统地址，到servlet路径，如：http://localhost:8080/ctx-path/rest/
- `-d`：启用客户端调试模式

5、修改`-h`指定的工作目录下的java文件可看到应用系统热刷新




# 4. 注意事项

1. 暂时只支持SpringBoot环境，可提交PR添加其他环境的支持
2. 只支持JDK环境，JRE环境不支持
3. 热刷新时会发送HTTP请求，如果该`/hot-refresh`请求被拦截，请自行在应用系统内放行
4. 热刷新功能基于JVMTI，所以不能添加类字段、方法、修改类的方法签名等信息，推荐只修改方法内代码
5. **无法热刷新混淆的字节码**，提供扩展可自行添加混淆功能



# 5. 未来规划

- [ ] 服务端提供IDEA插件
- [ ] jar包热刷新支持
- [x] 集成Lombok
- [x] 集成MapStruct
- [ ] 集成Proxy
- [x] 集成Spring
- [ ] 集成MyBatis
- [ ] 集成SkyWalking
- [ ] 集成Arthas





# 6. 源代码地址

[Github](https://github.com/hyfsy/hot-refresh)



























