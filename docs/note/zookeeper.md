# 安装ZooKeeper

安装单机模式

<!-- more -->

1. 下载zookeeper

2. 解压

3. 进入`conf`目录，复制**zoo_sample.cfg**一份到**zoo.cfg**

   修改内容

   ```properties
   # The number of milliseconds of each tick
   tickTime=2000
   # The number of ticks that the initial 
   # synchronization phase can take
   initLimit=10
   # The number of ticks that can pass between 
   # sending a request and getting an acknowledgement
   syncLimit=5
   # the directory where the snapshot is stored.
   # do not use /tmp for storage, /tmp here is just 
   # example sakes.
   dataDir=/data/zookeeper/data
   dataLogDir=/data/zookeeper/logs
   # the port at which the clients will connect
   clientPort=2181
   # the maximum number of client connections.
   # increase this if you need to handle more clients
   #maxClientCnxns=60
   #
   # Be sure to read the maintenance section of the 
   # administrator guide before turning on autopurge.
   #
   # http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
   #
   # The number of snapshots to retain in dataDir
   autopurge.snapRetainCount=3
   # Purge task interval in hours
   # Set to "0" to disable auto purge feature
   autopurge.purgeInterval=1
   
   ## Metrics Providers
   #
   # https://prometheus.io Metrics Exporter
   #metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
   #metricsProvider.httpPort=7000
   #metricsProvider.exportJvmInfo=true
   ```

4. 启动

   在安装目录

   ```shell
   启动命令：./bin/zkServer.sh start
   停止命令：./bin/zkServer.sh stop　　
   重启命令：./bin/zkServer.sh restart
   状态查看命令：./bin/zkServer.sh status
   ```





![][1]



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/08/d49453fd96b54429bcedca23ae48a78b.png