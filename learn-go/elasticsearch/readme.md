# <p align="center">[elasticsearch](https://www.elastic.co/cn/)</p>

- [01|安装](#01)   
- [02|经验积累](#02)




---
<h2 id="01">01|安装</h2>

---

1. 查看JDK版本
```
[root@xiecentos /]# java -version
openjdk version "1.8.0_222-ea"
OpenJDK Runtime Environment (build 1.8.0_222-ea-b03)
OpenJDK 64-Bit Server VM (build 25.222-b03, mixed mode)
```
2. 下载安装包到 /opt
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.2-linux-x86_64.tar.gz
```

3. 解压到指定目录 /usr/local/
```
tar -zxvf elasticsearch-7.6.2-linux-x86_64.tar.gz -C /usr/local/
```

4. 进入解压后的elasticsearch目录，新建data目录
```
cd /usr/local/elasticsearch-7.6.2
mkdir data
```
5. 修改config/elasticsearch.yml
```
vim config/elasticsearch.yml
#############################################
# 取消下列项注释并修改
cluster.name: my-application #集群名称
node.name: node-1 #节点名称
#数据和日志的存储目录
path.data: /usr/local/elasticsearch-7.6.2/data
path.logs: /usr/local/elasticsearch-7.6.2/logs
#设置绑定的ip，设置为0.0.0.0以后就可以让任何计算机节点访问到了
network.host: 0.0.0.0
http.port: 9200 #端口
#设置在集群中的所有节点名称，这个节点名称就是之前所修改的，当然你也可以采用默认的也行，目前是单机，放入一个节点即可
cluster.initial_master_nodes: ["node-1"]
###########################################
# 修改完毕后，:wq 保存退出vim，q!则不保存
```
6. 启动elasticsearch
```
/usr/local/elasticsearch-7.6.2/bin
./elasticsearch
```

7. 给非root用户授权
```
chown -R xiecentos:xiecentos /usr/local/elasticsearch-7.6.2/
```
8. vim 编辑 /etc/security/limits.conf，在末尾加上
```
xiecentos soft nofile 65536
xiecentos hard nofile 65536
xiecentos soft nproc 4096
xiecentos hard nproc 4096
```
9. vim 编辑 vim /etc/security/limits.d/20-nproc.conf，将* 改为用户名（xiecentos）：
```
# Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.
#*          soft    nproc     4096
xiecentos          soft    nproc     4096
root       soft    nproc     unlimited
```
10 . vim 编辑 /etc/sysctl.conf，在末尾加上
```
vm.max_map_count = 655360
```
执行：
```
[root@xiecentos etc]# sysctl -p
vm.max_map_count = 655360
```
11. 退出用户重新登录，使配置生效。重启 elasticsearch
```
request:

http://localhost:9200/

response:

{
    "name": "node-1",
    "cluster_name": "my-application",
    "cluster_uuid": "HwhOb0jDQvmSyPGOcKeZlA",
    "version": {
        "number": "7.6.2",
        "build_flavor": "default",
        "build_type": "tar",
        "build_hash": "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
        "build_date": "2020-03-26T06:34:37.794943Z",
        "build_snapshot": false,
        "lucene_version": "8.4.0",
        "minimum_wire_compatibility_version": "6.8.0",
        "minimum_index_compatibility_version": "6.0.0-beta1"
    },
    "tagline": "You Know, for Search"
}
```

12. 后台启动
```
./elasticsearch -d
# 查看进程
ps -ef|grep elasticsearch
```
13. 开启防火墙
```
firewall-cmd --zone=public --add-port=9200/tcp --permanent

firewall-cmd --zone=public --add-port=9300/tcp --permanent

firewall-cmd --reload
```


---
<h2 id="02">02|经验积累</h2>

---

1. elasticsearch 能使用root用户操作
