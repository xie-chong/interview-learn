

一、安装运行   
- [01丨环境要求](#01)   
- [02丨数据库初始化](#02)   
- [03丨配置hosts](#03)   
- [04丨修改配置](#04)   
  - [1. 注册中心地址](#04-1)   
  - [2. 数据库配置](#04-2)   
  - [3. Redis配置](#04-3)   
  - [4. Rabbitmq配置](#04-4)   
  - [5. 用户认证url修改](#04-5)   
  - [6. 文件中心配置修改](#04-6)   
  - [7. 后台管理界面配置接口地址](#04-7)   
  - [8. 邮件配置](#04-8)   
  - [9. 阿里云短信配置](#04-9)   
  - [10. 日志中心elasticsearch](#04-10)   
  - [11. 微信授权相关](#04-11)   
- [05丨启动](#05)   
二、模块详细介绍和配置   
- [01丨环境要求](#01)   



---
---
---
<h2 id="01">01 | 环境要求</h2>

---

1. Jdk 要求1.8，因为用到了lambda表达式
2. Mysql 5.6+
3. Redis 3.0+（ 注意：如果是非本地redis，即redis在别的机器上，那么请注意检查能否远程访问）
4. Rabbitmq 版本别太低就行（要记得先创建mq用户，见视频01.7 rabbitmq控制台添加用户）
5. Elasticsearch（日志中心使用，默认日志是存到mysql里，如采用默认配置，可不需要elasticsearch）
6. 安装 Lombok




---
<h2 id="02">02 | 数据库初始化</h2>

---

为了例子和项目更能说明服务化，每个服务都有单独的数据库（具体脚本参考项目代码）

| 模块 | 库名 | 脚本名 |
| :-----: | :----- | :----- |
| 用户中心 | cloud_user | cloud_user.sql |
| 认证中心 | cloud_oauth | cloud_oauth.sql |
| 文件中心 | cloud_file | cloud_file.sql |
| 后台管理 | cloud_backend | cloud_backend.sql |
| 日志中心 | cloud_log | cloud_log.sql |
| 通知中心 | cloud_notification | cloud_notification.sql |








---
<h2 id="03">03 | 配置hosts</h2>

---

配置了host之后，我们程序里要用ip的就都用域名访问了，比如连接mysql，我们要用localhost或者127.0.0.1或者某个ip，配置了host之后，就可以用域名了。

有部分win10系统的同志反映，用host连接redis、mysql啥的代码报错，说连不上，百度了下，win10系统可能需要打开cmd，然后执行下面的命令，清除DNS缓存 ```ipconfig /flushdns```` ,再连不上的话，就只能改为用ip了。用ip也挺好，我们用host是因为环境多了的话，用host的字母能很容易的区分出来是什么配置。

**Windows系统配置hosts**

C:\Windows\System32\drivers\etc

配置信息：   
```
127.0.0.1 api.gateway.com #外网网关ip
127.0.0.1 local.gateway.com #内网网关ip
127.0.0.1 local.register.com #注册中心ip
127.0.0.1 local.monitor.com #监控中心ip
127.0.0.1 local.mysql.com #数据库ip
127.0.0.1 local.redis.com #redis ip
127.0.0.1 local.rabbitmq.com #rabbitmq ip
```

**说明**：本例子host配置的都是127.0.0.1，具体ip个人根据实际情况修改。其中local开头的我们是模拟内网环境，部署在网络相同的内网即。





---
<h2 id="04">04 | 修改配置</h2>

---

|  | 工程名 |  |
| :-----: | :----- | :----- |
| 1	| config-center	| 配置中心 |
| 2	| file-center	| 文件中心 |
| 3	| gateway-zuul	| 网关 |
| 4	| log-center	| 日志中心 |
| 5	| manage-backend	| 管理后台 |
| 6	| monitor-center	| 监控中心 |
| 7	| oauth-center	| 认证中心 |
| 8	| register-center	| 注册中心 |
| 9	| user-center	| 用户中心 |
| 10	| notification-center	| 通知中心 |

<h3 id="04-1">1. 注册中心地址</h3>

上表列出的工程里的bootstrap.yml中，若配置了host，则不需要改动；若使用ip，则把local.register.com改为您的注册中心的ip



<h3 id="04-2">2. 数据库配置</h3>

项目目录cloud-service\config-center\src\main\resources\configs\dev\下，除去gateway-zuul.yml外，都有数据库配置
* file-center.yml
* gateway-zuul.yml
* log-center.yml
* manage-backend.yml
* notification-center.yml
* oauth-center.yml
* user-center.yml

```
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://local.mysql.com:3306/cloud_file?useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true&useSSL=false&serverTimezone=UTC
    username: root
    password: xxx
```

若配置了host，库名也按照上一章节的创建，只需修改端口号，用户名和密码，否则也要将域名改成ip

<h3 id="04-3">3. Redis配置</h3>

项目目录cloud-service\config-center\src\main\resources\configs\dev\下，有3个模块用到redis
* notification-center.yml
* oauth-center.yml
* user-center.yml

```
spring:
  redis:
    host: local.redis.com
    port: 6379
    password:
```

将配置按实际情况修改即可，**如有密码，跟host同层加password即可，别忘了冒号后有个空格**，如果不是本地的redis，请检查是否允许远程访问


<h3 id="04-4">4. Rabbitmq配置</h3>

1. 先在RabbitMQ控制台添加用户（administrator）,访问http://localhost:15672/

| username | password |
| :-----: | :----- |
| cloud-dev | cloud-dev |

2. 项目目录cloud-service\config-center\src\main\resources\configs\dev\下，除去gateway-zuul.yml外，都有mq配置（如与视频不同，以文档为准）
* file-center.yml
* gateway-zuul.yml
* log-center.yml
* manage-backend.yml
* notification-center.yml
* oauth-center.yml
* user-center.yml

3. 配置信息（按实际情况修改即可）
```
rabbitmq:
  host: local.rabbitmq.com
  port: 5672
  username: cloud-dev
  password: cloud-dev
  virtual-host: /
```


<h3 id="04-5">5. 用户认证url修改</h3>

项目目录cloud-service\config-center\src\main\resources\configs\dev\下，除去gateway-zuul.yml和oauth-center.yml外，都有获取用户认证信息的url
* file-center.yml
* gateway-zuul.yml
* log-center.yml
* manage-backend.yml
* notification-center.yml
* oauth-center.yml
* user-center.yml

```
security:
  oauth2:
    resource:
      user-info-uri: http://local.gateway.com:8080/api-o/user-me
      prefer-token-info: false
```

url域名和端口是网关中心的ip和端口，**这里demo是单个网关服务，正式生产或者多个网关服务的话，这里配置的是nginx的地址，注意该地址是各服务间在内网的调用，请配置内网的地址**

<h3 id="04-6">6. 文件中心配置修改</h3>

项目目录cloud-service\config-center\src\main\resources\configs\dev\下
* file-center.yml

```
file:
  local:
    path: D:/localFile
    prefix: /statics
    urlPrefix: http://api.gateway.com:8080/api-f${file.local.prefix}
```
path是存储文件的路径，上面是windows的目录，mac或者linux根据情况设置绝对目录   
urlPrefix前缀也是网关对外的域名和端口

**阿里云存储**
```
file:
  local:
    path: D:/localFile
    prefix: /statics
    urlPrefix: http://api.gateway.com:8080/api-f${file.local.prefix}
  aliyun:
    endpoint: xxx
    accessKeyId: xxx
    accessKeySecret: xxx
    bucketName: xxx
    domain: https://xxx
```
这里需要配置你的阿里云对象存储OSS相关信息，详细根据视频目录看下视频（目前我暂时弃用该功能）


<h3 id="04-7">7. 后台管理界面配置接口地址</h3>

cloud-service\manage-backend\src\main\resources\static\js\constant.js

```
var domainName = "http://api.gateway.com:8080";
```

这里定义的常量**domainName**是后端接口地址，在这里，我们的demo是直接访问网关的，所以配置的是后端java服务网关层的域名和端口，如没配置host的话，用ip加端口。**该地址如果配置错误的话，我们的前端将访问不到后端接口**。

注意：**正式生产为了保证网关的高可用性，肯定是部署了多个网关服务，然后用nginx反向代理的，那么多个网关或者生产环境的话，我们这里配置的是nginx的地址**

**loginPage**是登陆页地址，主要是为了未登录或过期时进行跳转，默认不需要修改
 
```
var loginPage = "/api-b/login.html";
```
如果是前端单独部署的话，这里请写全路径，如http://xx.xx.xx/login.html



<h3 id="04-8">8. 邮件配置</h3>

cloud-service\config-center\src\main\resources\configs\dev\manage-backend.yml

```
  mail:
    default-encoding: UTF-8
    host: smtp.163.com
    username:
    password:
    protocol: smtp
    test-connection: false
#    properties:
#      mail.smtp.auth: true
```

* 如果不使用发邮件功能，不用修改配置，也不用修改代码，别发邮件就行。
* 如果要用发邮件功能的话，需要配置您的邮箱信息，并将最后两行的注释打开。需要您开通smtp协议，如163邮箱：
http://help.163.com/10/0312/13/61J0LI3200752CLQ.html



<h3 id="04-9">9. 阿里云短信配置</h3>

cloud-service\config-center\src\main\resources\configs\dev\notification-center.yml

如要发短信，请配置您的阿里云信息   
```
aliyun:
  accessKeyId: xxx
  accessKeySecret: xxx
  sign:
    name1: xxx
  template:
    code1: xxx
```




<h3 id="04-10">10. 日志中心elasticsearch</h3>

cloud-service\log-center\src\main\java\com\cloud\log\service\impl\
* EsLogServiceImpl.java
* LogServiceImpl.java

**默认使用实现类LogServiceImpl，日志是存储到mysql里的**。

**如想存储到elasticsearch,有两个方案**
1. 注释掉LogServiceImpl上的@Primary和@Service
```
// @Primary
// @Service
public class LogServiceImpl implements LogService {
```
2. 将@Primary移到EsLogServiceImpl上面，并配置上你自己的Elasticsearch环境信息

```
// @Primary
@Service
public class LogServiceImpl implements LogService {
```

cloud-service\config-center\src\main\resources\configs\dev\log-center.yml
```
elasticsearch:
  clusterName: elasticsearch
  clusterNodes: 127.0.0.1:9300
```




<h3 id="04-11">11. 微信授权相关</h3>

这章节，不用微信授权的可忽略，详细可看下视频
在用户中心D:\Workspace-IntelliJ\cloud-service\config-center\src\main\resources\configs\dev\user-center.yml
```
wechat:
  domain: http://api.gateway.com:8080/api-u
  infos:
    app1:
      appid: xxx
      secret: xxx
    app2:
      appid: xxx
      secret: xxx
```
 
这里配置了服务端域名和用户中心转发规则（域名要跟微信网页授权域名一致），appid和secret根据实际情况配置即可，这里的例子配置了多个，只是为了说明咱们系统是可以支持多个的，其中app1、app2可随意定，不要重复即可。

在cloud-service\manage-backend\src\main\resources\static\pages\wechat\index.html里的跳去授权，用到我们定义的app1
```
        var openid = getUrlParam("openid");
        var toUrl = domainName + "/api-b/pages/wechat/index.html";
        if (openid == null || openid == "") {// 跳去微信授权
            window.location.href = domainName + "/api-u/wechat/app1?toUrl=" + encodeURIComponent(toUrl);
        } else {
```




---
<h2 id="05">05 | 启动</h2>

---

在**redis、mysql、rabbitmq都启动的前提下**，对着spring boot项目的根目录的类，启动main方法

**注意**：由于redis、mysql、rabbitmq可能不在同一台机器，记得检查hosts文件中的ip映射

1. 第一步启动注册中心  RegisterCenterApplication.java

2. 第二步启动配置中心  ConfigCenterApplication.java

3. 第三步
等待配置中心启动成功一会儿之后，再启动别的服务，否则微服务将拉取不到配置中心的配置，启动将报错。
* 用户中心  UserCenterApplication.java
* 通知中心  NotificationCenterApplication.java
* 授权中心  OAuthCenterApplication.java
* 文件服务  FileCenterApplication.java
* 日志中心  LogCenterApplication.java
* 后台管理  ManageBackendApplication.java
* 监控  MonitorApplication.java
* 最后启动zuul网关  GatewayApplication.java

4. 535错误
在idea中启动manage-backend服务时，邮箱配置错误的话，会报535的错误，不影响系统启动，只是邮件发不出去而已。
> javax.mail.AuthenticationFailedException: 535 Error: authentication failed

5. 都启动成功之后，可以访问后台管理界面  http://localhost:8080/api-b/login.html   
用户名admin 密码admin   
用户名superadmin 密码superadmin   







