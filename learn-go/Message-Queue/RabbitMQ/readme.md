## <p align="center">RabbitMQ</p>


[01丨RabbitMQ 安装](#01)   
[02丨xxx](#02)   






---
---
---
<h1 id = "01">RabbitMQ 安装</h1>

---

### 文档

https://www.rabbitmq.com/documentation.html

### [Install RabbitMQ Server](https://www.rabbitmq.com/install-rpm.html#install-rabbitmq)

**use a Package Cloud-provided script**

参考网址：
* https://www.rabbitmq.com/install-rpm.html#install-erlang
* https://packagecloud.io/rabbitmq/erlang/install#bash-rpm
* https://packagecloud.io/rabbitmq/rabbitmq-server/install#bash-rpm

### 步骤

1. 使用脚本准备安装erlang（rabbitmq是erlang语言编写的）

```

curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
```
2. yum安装erlang
```
yum  -y install erlang
```

3. 使用脚本准备安装RabbitMQ-server
```
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
```

4. yum安装rabbitmq-server
```
yum -y install rabbitmq-server
```

安装目录
```
/usr/lib/rabbitmq
```

5. 验证erlang是否安装成功
```
[root@xiecentos opt]# erl
Erlang/OTP 22 [erts-10.7.1] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:1] [hipe]

Eshell V10.7.1  (abort with ^G)
1> halt().
[root@xiecentos opt]# 
```

6. 完成后启动服务
```
cd /usr/lib/rabbitmq
```

```
/bin/systemctl start rabbitmq-server.service
```

7. 查看服务状态

```
/bin/systemctl status rabbitmq-server.service
```

```
[root@xiecentos rabbitmq]# /bin/systemctl status rabbitmq-server.service
● rabbitmq-server.service - RabbitMQ broker
   Loaded: loaded (/usr/lib/systemd/system/rabbitmq-server.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2020-04-14 16:52:06 CST; 11min ago
 Main PID: 8225 (beam.smp)
   Status: "Initialized"
    Tasks: 163
   CGroup: /system.slice/rabbitmq-server.service
           ├─8225 /usr/lib64/erlang/erts-10.7.1/bin/beam.smp -W w -A 128 -MBas ageffcbf -MHas ageffcbf -MBlmbcs 512 -MHlmbcs 512 -MMmcs 30 -P 1048576 -t 5000000 -stbt db -...
           ├─8404 /usr/lib64/erlang/erts-10.7.1/bin/epmd -daemon
           ├─8648 erl_child_setup 32768
           ├─8683 inet_gethost 4
           └─8700 inet_gethost 4

Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Doc guides: https://rabbitmq.com/documentation.html
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Support:    https://rabbitmq.com/contact.html
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Tutorials:  https://rabbitmq.com/getstarted.html
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Monitoring: https://rabbitmq.com/monitoring.html
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Logs: /var/log/rabbitmq/rabbit@xiecentos.log
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: /var/log/rabbitmq/rabbit@xiecentos_upgrade.log
Apr 14 16:51:34 xiecentos.com rabbitmq-server[8225]: Config file(s): (none)
Apr 14 16:52:06 xiecentos.com rabbitmq-server[8225]: Starting broker...systemd unit for activation check: "rabbitmq-server.service"
Apr 14 16:52:06 xiecentos.com systemd[1]: Started RabbitMQ broker.
Apr 14 16:52:06 xiecentos.com rabbitmq-server[8225]: completed with 0 plugins.
```

可以看到日志文件，但是没有配置文件，以及插件

8. 配置文件

默认rabbitmq是没有配置文件的，需要去官方github上，复制一个配置文件模版过来

```
cd /etc/rabbitmq/

vi rabbitmq.conf
```

编辑内容见说明文档中的[Rabbitmq.config.example](https://github.com/rabbitmq/rabbitmq-server/blob/master/docs/rabbitmq.conf.example)


9. 安装插件（启动web管理界面）
```
cd /usr/lib/rabbitmq/lib/rabbitmq_server-3.8.3/sbin

./rabbitmq-plugins enable rabbitmq-management
```

之后配置文件的目录结构中多出一个文件
```
[xiecentos@xiecentos rabbitmq]$ pwd
/etc/rabbitmq
[xiecentos@xiecentos rabbitmq]$ ls
enabled_plugins  rabbitmq.conf
```


10. 完成配置后停止、重启
```
/bin/systemctl stop rabbitmq-server.service

/bin/systemctl start rabbitmq-server.service
```

11. 开放5672端口
```
firewall-cmd --zone=public --add-port=5672/tcp --permanent

firewall-cmd --zone=public --add-port=15672/tcp --permanent

firewall-cmd --reload

netstat -tunlp |grep 5672
```

12. 一切就绪后，就可以登陆RabbitMQ WEB 管理界面

可以在界面添加用户，也可以在命令行操作

```
#添加用户
#./rabbitmqctl add_user 账号 密码
./rabbitmqctl add_user admin admin
#分配用户标签(admin为要赋予administrator权限的刚创建的那个账号的名字)
./rabbitmqctl set_user_tags admin administrator
#设置权限<即开启远程访问>(如果需要远程连接,例如java项目中需要调用mq,则一定要配置,否则无法连接到mq,admin为要赋予远程访问权限的刚创建的那个账号的名字,必须运行着rabbitmq此命令才能执行)
./rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*" 
```


