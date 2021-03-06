# Docker command

## run

### mongo

```shell
docker run --name docker-mongo -p 27018:27017 -v //d/dockerVolume/data/mongo:/data/db -v //d/dockerVolume/config/mongo:/data/configdb  -d mongo:latest 
```

### mysql

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

### postgres

```shell
 docker run -d -p 5432:5432 --name docker-postgresql -v //d/dockerVolume/data/postgres:/var/lib/postgresql/data -e POSTGRES_PASSWORD=123456 postgres:latest
```

### gremlin

```shell
docker run -d -p 8182:8182 --name docker-gremlin -v//d/dockerVolume/data/gremlin:/tmp/neo4j 140.231.89.84:30003/ctsharp-datalayer/gremlin-server-neo4j:3.4.8
```

### clickhouse

```shell
docker run -d --name docker-clickhouse -p 8123:8123 -p 9000:9000 -p 9009:9009 -v//d/dockerVolume/data/clickhouse:/var/lib/clickhouse --ulimit nofile=262144:262144 yandex/clickhouse-server
```