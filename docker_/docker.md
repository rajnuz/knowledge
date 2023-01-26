# Docker command

- [Docker command](#docker-command)
  - [run](#run)
    - [example](#example)
      - [mongo](#mongo)
      - [redis](#redis)
      - [mysql](#mysql)
      - [postgres](#postgres)
      - [gremlin](#gremlin)
      - [clickhouse](#clickhouse)
      - [mariadb](#mariadb)
    - [sleep](#sleep)
  - [tag](#tag)
  - [push](#push)
  - [拷贝](#拷贝)

---

## run

### example

#### mongo

```shell
docker run --name docker-mongo -p 27018:27017 -v //d/dockerVolume/data/mongo:/data/db -v //d/dockerVolume/config/mongo:/data/configdb  -d mongo:latest 
```

#### redis

```shell
docker run --name docker-redis -p 16379:6379 -v D:\dockerVolume\redis  -d redis:latest
```

#### mysql

```shell
docker run --name docker-mysql -p 3306:3306 --privileged=true -v //d/dockerVolume/data/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:latest
```

进入mysql 容器

```shell
docker exec -it docker-mysql /bin/bash
```

登录mysql

```shell
mysql -u root -p
```

设置允许远程用户访问

```shell
GRANT ALL ON *.* TO 'root'@'%';
```

导出docker mysql数据
[参考链接](https://blog.csdn.net/qq_33326449/article/details/86478766)
在docker容器中，整库导出

```shell
mysqldump -u ${用户名} -p ${数据库名} > /var/lib/mysql/${文件名}.sql
```

提示输入密码，然后就成功导出了
这里要留docker和宿主机挂载的路径，然后可以看到宿主机的路径上有该文件了

#### postgres

```shell
 docker run -d -p 5432:5432 --name docker-postgresql -v //d/dockerVolume/data/postgres:/var/lib/postgresql/data -e POSTGRES_PASSWORD=123456 postgres:latest
```

#### gremlin

```shell
docker run -d -p 8182:8182 --name docker-gremlin -v//d/dockerVolume/data/gremlin:/tmp/neo4j 140.231.89.84:30003/ctsharp-datalayer/gremlin-server-neo4j:3.4.8
```

#### clickhouse

```shell
docker run -d --name docker-clickhouse -p 8123:8123 -p 9000:9000 -p 9009:9009 -v//d/dockerVolume/data/clickhouse:/var/lib/clickhouse --ulimit nofile=262144:262144 yandex/clickhouse-server
```

#### mariadb

```bash
docker run --name docker-mariadb -p 13306:3306 -v //d/dockerVolume/data/mariadb:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -d mariadb:latest
```

### sleep

当我们run镜像，只是希望从镜像中copy一些东西，不需要镜像服务时，可以加上sleep 命令

```shell
docker run -d ${镜像名} sleep 99999
```

这个命令可以让镜像run的服务沉睡一段时间

## tag

docker 给已有镜像打新tag

```shell
docker tag ${old iamge name} ${new iamge name}
```

## push

```shell
docker push ${镜像完整名称}
```

## 拷贝

当我们为java服务打了个镜像后，可以run 镜像 从中拷贝jar

首先需要看一下pod id `docker ps`

然后进到pod查看镜像位置

```shell
docker exec -it ${pod id} bash
```

找到jar绝对路径后

```shell
docker cp ${pod id}:${pod 内 jar的绝对路径} ${拷贝出来的绝对路径 文件名}
```