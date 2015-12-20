# 利用docker构建mysql集群

## 创建`Dockerfile`

```
FROM centos:centos6
MAINTAINER fanteathy "517081935@qq.com"

RUN yum install -y mysql-server mysql

RUN /etc/init.d/mysqld start &&\  
    mysql -e "grant all privileges on *.* to 'fanteathy'@'%' identified by 'fanteathy';"&&\  
    mysql -e "grant all privileges on *.* to 'fanteathy'@'localhost' identified by 'fanteathy';"&&\  
    mysql -u fanteathy -pfanteathy -e "show databases;"

EXPOSE 3306  
   
CMD ["/usr/bin/mysqld_safe"]
```

## 利用`Dockerfile`构建两个image

```
# docker build -t mysql_server_1 .
# docker build -t mysql_server_2 .
```

## 启动container

### 使用`docker`命令

```
# docker run --name=mysqlserver1 -d -p 3307:3306 mysql_server_1
# docker run --name=mysqlserver2 -d -p 3308:3306 mysql_server_2
```

### 使用`docker-compose`

`docker-composer.yml`文件如下:

```
mysql1:
    image: mysql_server_1 
    ports:
        - "3307:3306"
    privileged: true

mysql2:
    image: mysql_server_2 
    ports:
        - "3308:3306"
    privileged: true

```

```
# docker-compose up -d(第一次)
# docker-compose start(以后)
```

## 使用container

### 查看运行的mysql container

```
# docker ps
CONTAINER ID        IMAGE                   COMMAND                CREATED             STATUS              PORTS                    NAMES
cc18adce78b5        mysql_server_2:latest   "/usr/bin/mysqld_saf   39 seconds ago      Up 20 seconds       0.0.0.0:3308->3306/tcp   mysqlcluster_mysql2_1
806a23c376ad        mysql_server_1:latest   "/usr/bin/mysqld_saf   39 seconds ago      Up 20 seconds       0.0.0.0:3307->3306/tcp   mysqlcluster_mysql1_1
```

### 连接mysql

```
# mysql -ufanetathy -pfanteathy -P3307
```

**但是貌似没有用，还得登陆container重新grant**

```
# docker exec -it mysqlcluster_mysql1_1 /bin/bash
```

在container中:

```
# mysql -uroot -p
# grant all privileges on *.* to 'eleme'@'%' identified by 'eleme';
# flush privileges
```

`mysqlcluster_mysql1_1`和`mysqlcluster_mysql2_1 `均使用以上操作重新grant

在host中连接mysql container:

```
mysql -ueleme -p -P3307
mysql -ueleme -p -P3308
```

OK
