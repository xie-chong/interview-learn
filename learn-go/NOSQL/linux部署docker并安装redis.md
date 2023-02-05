## 1、UBUNTU的默认root密码是多少，修改root密码

1. Ubuntu的默认root密码是随机的，即每次开机都有一个新的root密码。可以在终端输入命令 sudo passwd，然后输入当前用户的密码，enter.
2. 终端会提示输入新的密码并确认，此时的密码就是root新密码。修改成功后，输入命令 su root，再输入新的密码就ok了。
3.分析没有默认的，因为还没给 root 设置密码，第一个 user 是在 admin 组 ，所以可以给 root 设置密码 


> 原文链接：https://blog.csdn.net/weixin_48776118/article/details/126883555


## 2、创建Redis配置文件

docker启动前需要先创建Redis外部挂载的配置文件 （ /home/tools/redis/conf/redis.conf ）
之所以要先创建 , 是因为Redis本身容器只存在 /etc/redis 目录 , 本身就不创建 redis.conf 文件
当服务器和容器都不存在 redis.conf 文件时, 执行启动命令的时候 docker 会将 redis.conf 作为目录创建 , 这并不是我们想要的结果 .


> 原文链接：https://blog.csdn.net/qq_24958783/article/details/107541425

## 3、启动容器
```
docker run -d --name redis -p 6379:6379 -v /home/tools/redis/conf/redis.conf:/etc/redis/redis.conf -v /home/tools/redis/data:/data
```

## 4、进入Redis容器
```
docker exec -it redis redis-cli
```

## 5、退出容器
```
exit
```

## 6、参考信息

https://blog.csdn.net/qq_24958783/article/details/107541425

https://developer.aliyun.com/article/939721?spm=a2c6h.13813017.content3.1.507261d6MCg0vW

https://hub.docker.com/_/redis
