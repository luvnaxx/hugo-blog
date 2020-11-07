# Docker下安装软件

在CentOS下安装各种软件镜像。假设已经安装完毕docker环境。

> 安装方式:查看Docker[官方文档](https://docs.docker.com/engine/install/centos/)

设置阿里云镜像加速

> ```shell
> sudo mkdir -p /etc/docker
> sudo tee /etc/docker/daemon.json <<-'EOF'
> {
>   "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
> }
> EOF
> sudo systemctl daemon-reload
> sudo systemctl restart docker
> ```

CentOS8安装Docker：

1. 下载docker-ce的repo

```
curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
```

2. 安装依赖（这是相比centos7的关键步骤）

```
yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
```

3. 安装docker-ce

```
yum install docker-ce -y
```

4. 启动docker

```
systemctl start docker
```

<!--more-->


#### MySQL
拉取[官方镜像](https://hub.docker.com/_/mysql)

```shell
docker pull mysql:5.7   # 拉取 mysql 5.7
docker pull mysql       # 拉取最新版mysql镜像
```


- 如果建立目录映射


```shell
sudo docker run -p 3306:3306 --name mysql \
-v /data/docker/mysql/conf:/etc/mysql \
-v /data/docker/mysql/logs:/var/log/mysql \
-v /data/docker/mysql/data:/var/lib/mysql \
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

拉取[官方镜像](https://hub.docker.com/_/redis)

```shell
docker pull redis:5.0.9
```

创建配置文件`vim /data/docker/redis/conf/redis.conf`

```conf
# Redis configuration file example.
requirepass GetOutBadGuys
maxclients 10000

bind 0.0.0.0
```




```shell
docker run -d --name redis -p 6379:6379 \
--restart=always \
-v /data/docker/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /data/docker/redis/data:/data \
redis:5.0.9 redis-server /etc/redis/redis.conf
```


#### ElasticSearch

拉取[官方镜像](https://hub.docker.com/_/elasticsearch)

```shell
docker pull elasticsearch:7.9.3
```

创建目录

```shell
mkdir -p /data/docker/elasticsearch/config/
mkdir -p /data/docker/elasticsearch/logs
mkdir -p /data/docker/elasticsearch/plugins
```

创建配置文件`vim /data/docker/elasticsearch/config/elasticsearch.yml`

```conf
cluster.name: "elasticsearch"
network.host: 0.0.0.0
http.host: 0.0.0.0
```


```shell
docker run -d \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms128m -Xmx256m" \
-p 9200:9200 -p 9300:9300 \
--name elasticsearch \
--restart=always \
-v /data/docker/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /data/docker/elasticsearch/data:/usr/share/elasticsearch/data \
-v /data/docker/elasticsearch/logs:/usr/share/elasticsearch/logs \
-v /data/docker/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
elasticsearch:7.9.3
```

可能会出现

```shell
ElasticsearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes];
Likely root cause: java.nio.file.AccessDeniedException: /usr/share/elasticsearch/data/nodes
        at java.base/sun.nio.fs.UnixException.translateToIOException(UnixException.java:90)
        at java.base/sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:111)
        at java.base/sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:116)
        at java.base/sun.nio.fs.UnixFileSystemProvider.createDirectory(UnixFileSystemProvider.java:389)
        at java.base/java.nio.file.Files.createDirectory(Files.java:693)
        at java.base/java.nio.file.Files.createAndCheckIsDirectory(Files.java:800)
        at java.base/java.nio.file.Files.createDirectories(Files.java:786)
        at org.elasticsearch.env.NodeEnvironment.lambda$new$0(NodeEnvironment.java:274)
        at org.elasticsearch.env.NodeEnvironment$NodeLock.<init>(NodeEnvironment.java:211)
        at org.elasticsearch.env.NodeEnvironment.<init>(NodeEnvironment.java:271)
        at org.elasticsearch.node.Node.<init>(Node.java:277)
        at org.elasticsearch.node.Node.<init>(Node.java:257)
        at org.elasticsearch.bootstrap.Bootstrap$5.<init>(Bootstrap.java:221)
        at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:221)
        at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:349)
        at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:170)
        at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:161)
        at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86)
        at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:125)
        at org.elasticsearch.cli.Command.main(Command.java:90)
        at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:126)
        at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:92)
```

权限问题

进入`/data/docker/`

```shell
chmod -R 777 elasticsearch/
```

重启

###### 安装ik中文分词器

将插件解压到plugins目录下，重启

自定义扩展词

修改ik分词器配置`config/IKAnalyzer.cfg.xml`

```properties
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict"></entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">http://127.0.0.1/es/fenci.txt</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

重启ES



#### 安装Kibana

拉取[官方镜像](https://hub.docker.com/_/kibana)

```shell
docker pull kibana:7.9.3
```

创建目录

```shell
mkdir -p /data/docker/kibana/config/
```

创建配置文件`vim /data/docker/kibana/config/kibana.yml`

```shell
# Default Kibana configuration for docker target
server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://172.18.0.2:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
```



```
docker run --restart=always \
--name kibana \
-p 5601:5601 \
-v /data/docker/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml \
-d kibana:7.9.3
```

<u>continue</u>

