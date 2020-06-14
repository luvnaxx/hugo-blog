在CentOS下安装各种软件镜像。假设已经安装完毕docker环境。

> 安装方式:查看Docker[官方文档](https://docs.docker.com/engine/install/centos/)

<!--more-->

## 设置Docker国内镜像源

选择ali的

## 创建镜像

1. 拉取[官方镜像](https://hub.docker.com/)

   ```shell
   docker pull mysql:5.7   # 拉取 mysql 5.7
   docker pull mysql       # 拉取最新版mysql镜像
   ```


2. 检查是否拉取成功

   ```shell
   docker images
   ```
   
3. 推送镜像

4. 检查是否正常

   ```shell
   docker container ls
   ```

   


#### MySQL

- 如果建立目录映射


```shell
sudo docker run -p 3306:3306 --name mysql \
-v /usr/data/docker/mysql/conf:/etc/mysql \
-v /usr/data/docker/mysql/logs:/var/log/mysql \
-v /usr/data/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=docker_mysql \
-d mysql:8.0.20
```
- 默认不建立目录映射


```shell
sudo docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.20
```

- –name：容器名，此处命名为`mysql`

- -e：配置信息，此处配置mysql的root用户的登陆密码
- -p：端口映射，此处映射 主机3306端口 到 容器的3306端口
- -v：主机和容器的目录映射关系，":"前为主机目录，之后为容器目录


#### Redis

```shell
docker run -d --name redis -p 6379:6379 \
-v /usr/local/docker/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /usr/local/docker/redis/data:/data \
redis:5.0.9 redis-server /etc/redis/redis.conf
```



<u>continue</u>