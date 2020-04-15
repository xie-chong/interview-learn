

[01丨环境要求](#01)   
[02丨数据库初始化](#02)   
[03丨配置hosts](#03)   
[04丨修改配置](#04)   
[05丨启动](#05)   


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

### 1. 注册中心地址



### 2. 数据库配置


### 3. Redis配置


### 4. Rabbitmq配置

1. 先在RabbitMQ控制台添加用户（administrator）,访问http://localhost:15672/

| username | password |
| :-----: | :----- |
| cloud-dev | cloud-dev |

2. 在项目目录cloud-service\config-center\src\main\resources\configs\dev\下，除去gateway-zuul.yml外，都有mq配置，如与视频不同，以文档为准

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



### 5. 用户认证url修改


### 6. 文件中心配置修改



### 7. 后台管理界面配置接口地址



### 8. 邮件配置



### 9. 阿里云短信配置


### 10. 日志中心elasticsearch



### 11. 微信授权相关





---
<h2 id="05">05 | 启动</h2>

---







