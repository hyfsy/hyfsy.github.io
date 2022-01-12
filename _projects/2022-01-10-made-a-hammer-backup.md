# 1. 引言

用IDEA的同学都应该知道，它里面有个小锤子，就是这个![](../images/2022/01/10/001.png)，点击后本地的代码会自动执行热刷新。

由于使用JRebel启动系统太慢了，我一般用的这个小锤子，虽然功能没JRebel强大，但完全够用了。然而这个锤子有个缺点，需要手动锤它一下才会热刷新。

某天，我望着IDEA的小锤子就突然有了灵感，有没有方法能给我自动的热刷新，如果能实现的话，那远程的服务器上是不是也可以做到这种效果，因为远程的服务器和本地相比就多了一次请求调用。

和部门内**某人**讨论了一下这个想法，结果被喷的一无是处，最后这个玩具只能拿出来给大伙儿玩玩，渴望在这冷漠的世界里寻找一点心灵的慰藉。





# 2. 介绍

IDEA小锤子的远程版

本地修改代码后，远程服务器自动热刷新，效果实时生效



# 3. 使用

1、需要热刷新的应用程序引入依赖：

```xml
<dependency>
    <groupId>com.epoint.frame</groupId>
    <artifactId>hot-refresh-epoint</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</dependency>
```

2、`EpointSSOClient.properties`添加配置：

```properties
NoNeedAuthRests=rest/hot-refresh;
```

3、`EpointSecurityConfig.properties`添加配置：

```properties
allowUploadExtensions=java;
```

4、启动应用程序

5、获取本地服务器jar包：[hot-refresh-server-1.0.0.jar](http://192.168.0.99:8081/nexus/repository/framerelase/com/hyf/hot-refresh-server/1.0.0/hot-refresh-server-1.0.0.jar)，进入命令行界面，启动本地服务器：

```bash
java -jar hot-refresh-server-1.0.0.jar -h C:\\Users\\baB_hyf\\Desktop\\test -s http://localhost:8080/epoint-web/rest
```

- `-h`：本地编写代码的工作目录（默认当前命令行目录）
- `-s`：需要热刷新的应用程序地址，到servlet路径，如：http://localhost:8080/epoint-web/rest

6、修改`-h`指定的工作目录下的java文件可看到应用系统热刷新，基于文件保存触发



# 4. FAQ

## 4.1. 环境限制

1. 暂时只支持SpringBoot环境，可自行添加其他环境的支持
2. ~~不支持eCloud部署的系统，因为没有JDK环境，可自行构建JDK环境的tomcat镜像~~



## 4.2. 功能限制

1. 热刷新功能基于JVMTI，所以不能添加类字段、方法、修改类的方法签名等信息，推荐只修改方法内代码
2. **无法热刷新混淆的字节码**，提供扩展可自行添加混淆功能（不过我想这功能也写不出来，看看就行）





# 5. 源代码地址

[Gitlab](http://192.168.0.200/hyfeng/hot-refresh-epoint)













