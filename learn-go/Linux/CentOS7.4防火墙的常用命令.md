# <p align="center">CentOS7.4防火墙的常用命令</p>

## 前言
### 1 防火墙的管理工具

1、firewall-cmd ：专业人士用的，便于自动化运维

2、firewall-config:图形界面，操作简单

### 2 下面主要简单介绍常见的防火墙区域


-public ：仅允许访问本机的ssh，ping，dhcp服务（防火墙默认区域）

-trusted：允许任何访问

-block：阻塞任何来访请求，明确拒绝

-drop：丢弃任何来访的数据包，直接丢弃

### 3 防火墙进入区域规则：匹配即停止

1、查看数据包源ip地址，再查看所有的区域中，是否有该源ip地址的规则，哪个区域有则进入哪个区域。

2、进入默认区域public

### 4 默认区域


修改默认区域

格式：firewall-cmd --set-default-zone=区域名称

例子：

[root@server0 ~]# firewall-cmd --set-default-zone=trusted 

success

查看默认区域

格式：[root@server0 ~]# firewall-cmd --get-default-zone 

例子：

[root@server0 ~]# firewall-cmd --get-default-zone 

trusted


## 服务

### 1 查看public的所有服务

[root@server0 ~]# firewall-cmd --zone=public --list-all 

public

  interfaces: 

  sources: 

  services: dhcpv6-client  http ssh    #已经开启的服务

  ports:                                                #开启的端口

  masquerade: no

  forward-ports: port=5423:proto=tcp:toport=80:toaddr=  #转发端口

  icmp-blocks: 

  rich rules: 

### 2 添加服务

格式：firewall-cmd --zone=public --add-service=服务名称

例子：

[root@server0 ~]# firewall-cmd --permanent --zone=public --add-service=ftp      #防火墙开放ftp服务   --permanent：表示永久有效

success

[root@server0 ~]# firewall-cmd --reload     #重启配置文件

success

[root@server0 ~]# firewall-cmd --zone=public --list-services #查看开放的服务

dhcpv6-client ftp http ssh

### 3 删除服务

格式：firewall-cmd --zone=public --remove-service=服务名称

例子：

[root@server0 ~]# firewall-cmd --permanent --zone=public --remove-service=ftp      #防火墙删除服务ftp服务   --permanent：表示永久有效

success

[root@server0 ~]# firewall-cmd --reload     #重启配置文件

success

[root@server0 ~]# firewall-cmd --zone=public --list-services #查看开放的服务

dhcpv6-client  http ssh


## 端口号

### 1 查看防火墙开放的端口号

格式： firewall-cmd --zone=public --list-ports 

例子：

[root@server0 ~]# firewall-cmd --zone=public --list-ports 

80/tcp

### 2 添加防火墙开放端口号

格式：firewall-cmd --permanent --zone=public --add-port=端口号/tcp

例子：

[root@server0 ~]# firewall-cmd --permanent --zone=public --add-port=8080/tcp

success

[root@server0 ~]# firewall-cmd --zone=public --list-ports 

80/tcp 8080/tcp

### 3 删除防火墙开放端口号

格式：firewall-cmd --permanent --zone=public --remove-port=端口号/tcp

例子：

[root@server0 ~]# firewall-cmd --permanent --zone=public --remove-port=8080/tcp

success

[root@server0 ~]# firewall-cmd --zone=public --list-ports 

80/tcp

### 4 添加端口号和删除端口号记得重启服务

格式：systemctl restart firewalld

### 5 端口映射

格式：firewall-cmd --permanent --zone=public --add-forward-port=port=需要映射的端口号:proto=tcp:toport=目标端口号

例子：

功能：实现访问http：//172.25.0.11：5423 可以映射到http://172.25.0.11:80

[root@server0 ~]# firewall-cmd --permanent --zone=public --add-forward-port=port=5423:proto=tcp:toport=80

success

[root@server0 ~]# firewall-cmd --reload 

success

