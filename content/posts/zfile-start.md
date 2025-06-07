+++
title = 'ZFile学习笔记——启动！'
date = 2025-06-07T14:02:08+08:00
draft = false
categories = ["学习"]

+++

## 项目介绍

> ZFile 是一个适用于个人或小团队的在线网盘程序，可以将多种存储类型统一管理，再也不用登录各种网站管理文件，现在你只需要在 ZFile 中畅快使用！——GitHub README

我第一次见到这个项目是在折腾LG手机刷机的时候发现的，有人用它托管刷机包。后来我自己把它部署到服务器上试了一下，发现可以很方便的挂载OneDrive，并且可以直接生成下载链接。

最近在学习Java项目，但是网上找到的学习项目都是玩具项目(黑马全家桶、各种知识星球项目)，他们很多都存在功能简陋、代码不规范、没有完善的配套前端等等问题......最重要的是项目的需求和功能都是虚拟的，根本没有实际意义。于是想到了之前用过的ZFile恰好就是用Java开发的，并且复杂程度貌似并不高🤨(我猜的)，用来学习实际的Java项目岂不是完美？

## ZFile使用的技术

把项目从GitHub上克隆下来后我首先查看了maven的配置文件`pom.xml`，从中可以查看该项目所使用到的技术。

### 1. SpringBoot与Java版本

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.3.2</version>
  <relativePath/> <!-- 不要去本地找父 POM，而是直接去仓库下载它 -->
</parent>
...
<java.version>21</java.version>
<maven.compiler.source>21</maven.compiler.source>
<maven.compiler.target>21</maven.compiler.target>
```

使用`Spring Boot 3.3.2`作为基础框架并使用`Java 21`，表明这个项目的技术是比较新的。

### 2.依赖 

依赖分为几大类，反映了项目的功能模块和技术栈：

#### **Spring Boot相关**

- spring-boot-starter-web: 提供Web开发支持，用于构建RESTful API或Web应用。
- spring-boot-starter-aop: 支持面向切面编程（AOP），用于日志、事务等横切关注点。
- spring-boot-configuration-processor: 生成配置元数据，支持Spring Boot的自动配置。
- spring-boot-starter-data-redis: Redis支持，可能用于缓存或会话管理。
- spring-boot-starter-cache: 提供缓存支持，可能与Redis结合使用。
- spring-boot-starter-thymeleaf: Thymeleaf模板引擎，用于服务端渲染HTML页面。
- spring-boot-starter-validation: Hibernate Validator，用于参数校验。

#### **数据库相关**

- mysql-connector-j: MySQL数据库驱动（运行时依赖）。
- sqlite-jdbc: SQLite数据库驱动，支持轻量级数据库。
- flyway-core, flyway-mysql: Flyway数据库迁移工具（版本10.12.0），用于数据库版本管理和Schema迁移。
- mybatis-plus-spring-boot3-starter: MyBatis-Plus（版本3.5.6），增强型ORM框架，简化数据库操作。

#### **存储策略相关**

- upyun java-sdk: 又拍云存储SDK。
- software.amazon.awssdk:s3: AWS S3存储支持。
- qiniu-java-sdk: 七牛云存储SDK。
- jsch: SSH/SFTP支持，可能用于文件传输。
- sardine: WebDAV协议支持。
- google-api-client: Google API客户端，可能用于Google Drive等存储服务。

#### **认证/权限**

- sa-token-spring-boot3-starter: Sa-Token（版本1.38.0），轻量级认证授权框架，可能用于用户登录、权限管理和会话控制。

#### **API文档**

- knife4j-openapi3-jakarta-spring-boot-starter: Knife4j（版本4.5.0），基于OpenAPI 3的API文档生成工具，提供交互式API文档。

#### **工具类**

- hutool-all: Hutool工具库，提供丰富的Java工具方法。
- poi-ooxml: Apache POI，用于处理Excel、Word等Office文档。
- commons-compress: 文件压缩/解压支持。
- lombok: 简化Java代码（如自动生成Getter/Setter）。
- commons-net: 网络工具库，支持FTP、Telnet等协议。
- okhttp: HTTP客户端，用于网络请求。
- fastjson2: JSON处理库，性能优异。
- guava: Google Guava工具库，提供集合、缓存等功能。
- mapstruct: 对象映射工具，简化DTO与实体转换。
- commons-chain: 责任链模式实现。
- totp: 时间戳一次性密码（TOTP），可能用于双因素认证。
- jcommander: 命令行参数解析。
- json: JSON处理库。
- httpclient5, httpmime: Apache HttpClient，支持HTTP请求和文件上传。
- bcprov-jdk15on: Bouncy Castle加密库，支持加密操作。
- spring-retry: Spring重试机制，处理失败重试逻辑。
- commons-fileupload: 文件上传支持。
- dns-cache-manipulator: DNS缓存操作，可能用于优化网络请求。
- oshi-core: 系统监控库，用于获取系统资源信息。

#### **GraalVM支持**

- graal-sdk: GraalVM SDK（版本24.1.0，provided scope），用于支持GraalVM原生镜像编译。

#### **构建配置（<build>）**

- 插件
  - spring-boot-maven-plugin: Spring Boot构建插件，支持打包可执行JAR。
  - maven-compiler-plugin: 配置Java 21编译，启用MapStruct、Lombok等注解处理器。
  - flyway-maven-plugin: Flyway数据库迁移插件，用于执行数据库迁移任务。
- Profiles
  - 使用 native-maven-plugin（GraalVM构建工具）。
  - 配置了特定的JVM参数和构建参数（如-march=compatibility, AddAllCharsets等）。
  - 启用了自定义的AOT（Ahead-of-Time）特性，包括Lambda、加密（Bouncy Castle）和SQLite支持。
  - 项目支持GraalVM原生编译，以提高启动性能和降低内存占用。

## ZFile的功能

 在深入ZFile的源码之前我打算先把它的基本功能搞清楚，然后按照不同的功能模块分别去细致地学习代码实现。

### 1.系统初始化

![初始化页面](https://images.howe.wang/2025/06/61f08afef7993b434498ab585b303190.webp)

ZFile第一次启动会跳转到系统初始化页面，这里需要填入站点的名称、管理员账号和密码信息。

### 2. 登录

![登录页面](https://images.howe.wang/2025/06/0d5c1030aa2ccfd852ebfccdfbcd74e1.webp)

初始化页面之后会提示转跳到登录账号页面，这里我直接填入刚刚新建的管理员账号进行登录。

### 3.站点设置

![站点设置-1](https://images.howe.wang/2025/06/040a658f58df76fd3700bb261b049edc.webp)

![站点设置-2](https://images.howe.wang/2025/06/c7d445b50ea8202208705abe16e0299d.webp)

第一次登录会跳转到站点设置页面，这里面可以设置站点相关的各种选项，这里我比较好奇`最大同时上传文件数`是如何实现的。

### 4.存储源设置

![存储源设置-1](https://images.howe.wang/2025/06/cf2a36ac1e1605821f50cc13b727b3af.webp)

本项目的核心配置项，可以添加各种存储源，点击加号后进入具体的配置页面。

![存储源设置-2](https://images.howe.wang/2025/06/bb597d2c65c48a8b9ef06e50fc21c69c.webp)

这里根据存储策略的不同，前端也会显示不同的配置项。

![存储源设置-3](https://images.howe.wang/2025/06/c24ad29f4b2b7811f4091ba7b78d218d.webp)

例如这里我选择了阿里云OSS，前端会出现对应的配置项。

![存储源设置-4](https://images.howe.wang/2025/06/5d18be0263ac0b27fa948d38b11eb3fa.webp)

我配置了我的OneDrive云盘，回到上级页面后可以发现多了一个已配置的卡片。

### 5.显示设置

![显示设置-1](https://images.howe.wang/2025/06/109c7ec29ec1b89fff6cc7f00e6e7718.webp)

第一个卡片是`使用习惯`，记录了用户的各种显示偏好设置。

![显示设置-2](https://images.howe.wang/2025/06/77fb5ab230a6303e621e468de7676de8.webp)

第二个卡片是浏览器预览设置。

![显示设置-3](https://images.howe.wang/2025/06/40bc9ba169127a0cb30a9f17467178f0.webp)

OnlyOffice配置页面。

> **OnlyOffice** 是一个 **开源的办公套件** ，主要用于文档处理和团队协作。它提供了一整套工具，允许用户创建、编辑、查看和协作处理文档、电子表格和演示文稿，类似于 Microsoft Office 或 Google Docs，但它是 **开源且支持私有部署** 的。——Qwen

![显示设置-4](https://images.howe.wang/2025/06/0b7bfb51d0dd48814517514b8613b7cd.webp)

![显示设置-5](https://images.howe.wang/2025/06/ad8830509ec34adafe04cbb0110be1e9.webp)

`公告`和`自定义JS/CSS`，比较好奇前端是如何请求并加载这些数据到页面上的。

### 6.直/短链设置

![直/短链设置-1](https://images.howe.wang/2025/06/9e1c3c70fd9a6027274fb579147324bd.webp)

我觉得这也是一个核心功能，如何为网盘上的文件生成直链、如何生成和管理短链、如何防盗链、下载频率限制的实现原理我都很感兴趣。

### 7.短链管理

![短链管理](https://images.howe.wang/2025/06/dd107d7ad1e3360b090af88e8d586886.webp)

这里可以管理站点里生成的所有短链。

### 8.下载日志

![下载日志](https://images.howe.wang/2025/06/dcd5e534663f6b5817559c68b91f78ce.webp)

记录用户通过直链/短链下载的记录，可以对齐增删改查。

### 9.用户管理

![用户管理-1](https://images.howe.wang/2025/06/14e991cb35fd11ed196be11d8000090d.webp)

对系统里的用户进行增删改查，这个我很熟悉🤣

![用户管理-2](https://images.howe.wang/2025/06/7f7a26bda294053af80535db2767bf99.webp)

权限管理，使用的sa-token，我之前了解过这个框架但是没用过，这次可以学习一下。

### 10.单点登录

![单点登录](https://images.howe.wang/2025/06/3083cc0cdd078356f55fa58250a05860.webp)

> 单点登录（Single Sign-On，简称SSO）是一种用户认证机制，允许用户使用一组登录凭据（如用户名和密码）访问多个相关但独立的系统或应用，而无需在每个系统上单独登录。换句话说，用户只需登录一次，就可以无缝访问所有已授权的系统。——Grok

管理员可以自定义SSO，之前没有了解过🤨

### 11.安全设置

![安全设置](https://images.howe.wang/2025/06/51e968fcefa59a53a9a114cec513fa99.webp)

学习一下图片验证码和双因素认证是如何实现的。

### 12.访问控制

![访问控制](https://images.howe.wang/2025/06/76e97bf03e31228b23604b575b7384da.webp)

可以设置IP和UA和黑名单，底下还有一个屏蔽迅雷下载。

### 13.登录日志/系统日志下载

![登录日志](https://images.howe.wang/2025/06/7b1768c42f1e837bf92edb9b713351ed.webp)

查询系统内所有用户的登录日志。

![系统日志](https://images.howe.wang/2025/06/d6e57fb281ba0b391602a72b81cb636e.webp)

下载程序运行中的所有日志zip压缩包，之前做的玩具项目都没有日志功能，全都是在控制台直接打印🤨

### 14.首页

![首页-1](https://images.howe.wang/2025/06/cac13ab4ffb5ec240119271b8feb0535.webp)

看完了设置页面的功能总算来到最重要的首页了，这里列出了所有的存储源，我因为只添加了OneDrive，所以只有一个。

![视图](https://images.howe.wang/2025/06/a3dd92455fd3166ab825bf4c74cfc51d.webp)

首页有一个视图设置，可以配置显示的效果，尚不清楚设置是存储在浏览器还是后端数据库。

![修改密码](https://images.howe.wang/2025/06/8b0fea3cefcdf8423e47b63521363099.webp)

点击头像可以修改当前登录账号的密码。

![文件列表](https://images.howe.wang/2025/06/ad229dc7998eac3ba99242c2d62b5596.webp)

进入存储源可以浏览存储源内的文件，很多格式的都能打开预览，我比较关心的是前端收到的是什么数据格式，还有下载的数据是从数据源直接下载的还是从ZFile中转的。

![浏览相册](https://images.howe.wang/2025/06/59617d73ad9659b7cf870881b8dd1ccf.webp)

不知道为什么浏览相册的时候页面会崩溃，有可能前端是dev服务器的原因？🤨

## 总结

这是一个轻量级的个人云存储聚合项目，虽然没有什么听起来高大上的高并发、高可用的特性，也没有整合一些听起来很高级的技术栈，但是我觉得它起码是一个有很多人真的正在使用的系统，学习它可以弥补一些我之前在玩具项目中没有学过的东西。
