# quick-dev-space

快速开发环境搭建

## docker & kubernetes

`docker`开发环境推荐使用`Rancher Desktop`，支持`docker`和`kubernetes`环境的快速搭建
[https://github.com/rancher-sandbox/rancher-desktop](https://github.com/rancher-sandbox/rancher-desktop)


## 创建docker虚拟网络
```shell
 docker network create dev
```

## mysql

用户名：root ，密码：123456 ，端口：3306

``` shell
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -p 33060:33060 --network dev --network-alias mysql --restart=always -d mysql:8.0
```

## redis

``` shell
docker run --name redis -p 6379:6379 --network dev --network-alias redis --restart=always -d redis:7.0
```

## rabbitmq

用户名：root ，密码：root ，访问地址： http://localhost:15672

``` shell
docker run -d --hostname my-rabbit --name rabbitmq -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=root -p 4369:4369 -p 5671-5672:5671-5672 -p 15671:15671 -p 15691-15692:15691-15692 -p 15672:15672 -p 25672:25672 --network dev --network-alias rabbitmq --restart=always rabbitmq:3-management
```

## nacos

用户名：nacos ，密码：nacos ，访问地址：  http://localhost:8848/nacos

``` shell
docker run --name nacos -e MODE=standalone -p 8848-8849:8848-8849 --network dev --network-alias nacos --restart=always -d nacos/nacos-server:v2.1.0
```

## apollo

用户名：apollo ，密码：admin ，访问地址：http://localhost:8070/

1. 先在mysql数据库中创建ApolloConfigDB和ApolloPortalDB库，使用下列脚本：

[apolloconfigdb.sql](apollo/apolloconfigdb.sql)、[apolloportaldb.sql](apollo/apolloportaldb.sql)

原始地址：

[https://github.com/apolloconfig/apollo/blob/master/scripts/sql/apolloportaldb.sql](https://github.com/apolloconfig/apollo/blob/master/scripts/sql/apolloportaldb.sql)
[https://github.com/apolloconfig/apollo/blob/master/scripts/sql/apolloconfigdb.sql](https://github.com/apolloconfig/apollo/blob/master/scripts/sql/apolloconfigdb.sql)

2. 部署

``` shell
docker run -p 8080:8080  -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/ApolloConfigDB?characterEncoding=utf8"  -e SPRING_DATASOURCE_USERNAME=root -e SPRING_DATASOURCE_PASSWORD=123456  -d -v /tmp/logs:/opt/logs --name apollo-configservice  --network dev --network-alias apollo-configservice --restart=always apolloconfig/apollo-configservice:2.0.0
docker run -p 8090:8090  -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/ApolloConfigDB?characterEncoding=utf8"  -e SPRING_DATASOURCE_USERNAME=root -e SPRING_DATASOURCE_PASSWORD=123456  -d -v /tmp/logs:/opt/logs --name apollo-adminservice --network dev --network-alias apollo-adminservice --restart=always apolloconfig/apollo-adminservice:2.0.0
docker run -p 8070:8070  -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/ApolloPortalDB?characterEncoding=utf8"  -e SPRING_DATASOURCE_USERNAME=root -e SPRING_DATASOURCE_PASSWORD=123456  -e APOLLO_PORTAL_ENVS=dev,pro  -e DEV_META=http://apollo-configservice:8080 -e PRO_META=http://apollo-configservice:8080  -d -v /tmp/logs:/opt/logs --name apollo-portal --network dev --network-alias apollo-portal --restart=always apolloconfig/apollo-portal:2.0.0
```

## nexus3

```shell
# https://github.com/sonatype/docker-nexus3
docker run -d -p 8081:8081 --name nexus --network dev --network-alias nexus  --restart=always sonatype/nexus3


# http://localhost:8081/
# 
```

## metabase

```shell
docker pull metabase/metabase:latest

docker run --name metabase  -e MYSQL_ROOT_PASSWORD=123456 -p 3000:3000 --network dev --network-alias metabase  --restart=always -d metabase/metabase
```


