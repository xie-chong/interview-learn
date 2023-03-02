## PostgreSQL

```
docker run --name postgres -e POSTGRES_PASSWORD=password -p 5432:5432 -v E:/postgres/data:/var/lib/postgresql/data -d postgres:latest
```

### run: 创建并运行一个容器

https://github.com/docker-library/docs/blob/master/postgres/README.md

* --restart=always 表示容器退出时,docker会总是自动重启这个容器；
* –name: 指定创建的容器的名字；
* -e POSTGRES_PASSWORD=password: 设置环境变量，指定数据库的登录口令为password；
* -p 5432:5432: 端口映射将容器的5432端口映射到外部机器的5432端口；
* -v /postgresql/data:/var/lib/postgresql/data   将运行镜像的/var/lib/postgresql/data目录挂载到宿主机/data/postgresql目录
* -d postgres:11.4: 指定使用postgres:latest作为镜像。




### Spring Boot Connect to PostgreSQL Database Examples

[Spring Boot Connect to PostgreSQL Database Examples](https://www.codejava.net/frameworks/spring-boot/connect-to-postgresql-database-examples)   
