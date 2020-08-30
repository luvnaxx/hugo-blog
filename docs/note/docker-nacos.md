# Docker安装Nacos1.3.0,并配置MySQL8做数据持久化

docker下安装Nacos-server，并配置MySQL做数据持久化

> nacos-server:1.3.0
>
> MySQL8.0.20

网上随便一搜，相关版本的有好多。但大部分都是抄袭的，根本对不上

<!-- more -->

1. 拉取镜像

   略

2. 创建数据库和表

   ```mysql
   /*
   数据库全名 = nacos_config 
   表名称 = config_info
   */ CREATE TABLE `config_info` (
   	`id` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT COMMENT 'id',
   	`data_id` VARCHAR ( 255 ) NOT NULL COMMENT 'data_id',
   	`group_id` VARCHAR ( 255 ) DEFAULT NULL,
   	`content` LONGTEXT NOT NULL COMMENT 'content',
   	`md5` VARCHAR ( 32 ) DEFAULT NULL COMMENT 'md5',
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
   	`src_user` text COMMENT 'source user',
   	`src_ip` VARCHAR ( 20 ) DEFAULT NULL COMMENT 'source ip',
   	`app_name` VARCHAR ( 128 ) DEFAULT NULL,
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT '租户字段',
   	`c_desc` VARCHAR ( 256 ) DEFAULT NULL,
   	`c_use` VARCHAR ( 64 ) DEFAULT NULL,
   	`effect` VARCHAR ( 64 ) DEFAULT NULL,
   	`type` VARCHAR ( 64 ) DEFAULT NULL,
   	`c_schema` text,
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_configinfo_datagrouptenant` ( `data_id`, `group_id`, `tenant_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = 'config_info';
   /*
   数据库全名 = nacos_config	
   表名称 = config_info_aggr
   */
   CREATE TABLE `config_info_aggr` (
   	`id` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT COMMENT 'id',
   	`data_id` VARCHAR ( 255 ) NOT NULL COMMENT 'data_id',
   	`group_id` VARCHAR ( 255 ) NOT NULL COMMENT 'group_id',
   	`datum_id` VARCHAR ( 255 ) NOT NULL COMMENT 'datum_id',
   	`content` LONGTEXT NOT NULL COMMENT '内容',
   	`gmt_modified` datetime NOT NULL COMMENT '修改时间',
   	`app_name` VARCHAR ( 128 ) DEFAULT NULL,
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT '租户字段',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` ( `data_id`, `group_id`, `tenant_id`, `datum_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = '增加租户字段';
   /*   
   数据库全名 = nacos_config
   表名称 = config_info_beta   
   */
   CREATE TABLE `config_info_beta` (
   	`id` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT COMMENT 'id',
   	`data_id` VARCHAR ( 255 ) NOT NULL COMMENT 'data_id',
   	`group_id` VARCHAR ( 128 ) NOT NULL COMMENT 'group_id',
   	`app_name` VARCHAR ( 128 ) DEFAULT NULL COMMENT 'app_name',
   	`content` LONGTEXT NOT NULL COMMENT 'content',
   	`beta_ips` VARCHAR ( 1024 ) DEFAULT NULL COMMENT 'betaIps',
   	`md5` VARCHAR ( 32 ) DEFAULT NULL COMMENT 'md5',
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
   	`src_user` text COMMENT 'source user',
   	`src_ip` VARCHAR ( 20 ) DEFAULT NULL COMMENT 'source ip',
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT '租户字段',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_configinfobeta_datagrouptenant` ( `data_id`, `group_id`, `tenant_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = 'config_info_beta';
   /*   
   数据库全名 = nacos_config      
   表名称 = config_info_tag   
   */
   CREATE TABLE `config_info_tag` (
   	`id` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT COMMENT 'id',
   	`data_id` VARCHAR ( 255 ) NOT NULL COMMENT 'data_id',
   	`group_id` VARCHAR ( 128 ) NOT NULL COMMENT 'group_id',
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT 'tenant_id',
   	`tag_id` VARCHAR ( 128 ) NOT NULL COMMENT 'tag_id',
   	`app_name` VARCHAR ( 128 ) DEFAULT NULL COMMENT 'app_name',
   	`content` LONGTEXT NOT NULL COMMENT 'content',
   	`md5` VARCHAR ( 32 ) DEFAULT NULL COMMENT 'md5',
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
   	`src_user` text COMMENT 'source user',
   	`src_ip` VARCHAR ( 20 ) DEFAULT NULL COMMENT 'source ip',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_configinfotag_datagrouptenanttag` ( `data_id`, `group_id`, `tenant_id`, `tag_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = 'config_info_tag';
   /*   
   数据库全名 = nacos_config      
   表名称 = config_tags_relation   
   */
   CREATE TABLE `config_tags_relation` (
   	`id` BIGINT ( 20 ) NOT NULL COMMENT 'id',
   	`tag_name` VARCHAR ( 128 ) NOT NULL COMMENT 'tag_name',
   	`tag_type` VARCHAR ( 64 ) DEFAULT NULL COMMENT 'tag_type',
   	`data_id` VARCHAR ( 255 ) NOT NULL COMMENT 'data_id',
   	`group_id` VARCHAR ( 128 ) NOT NULL COMMENT 'group_id',
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT 'tenant_id',
   	`nid` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT,
   	PRIMARY KEY ( `nid` ),
   	UNIQUE KEY `uk_configtagrelation_configidtag` ( `id`, `tag_name`, `tag_type` ),
   	KEY `idx_tenant_id` ( `tenant_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = 'config_tag_relation';
   /*   
   数据库全名 = nacos_config      
   表名称 = group_capacity   
   */
   CREATE TABLE `group_capacity` (
   	`id` BIGINT ( 20 ) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键ID',
   	`group_id` VARCHAR ( 128 ) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
   	`quota` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
   	`usage` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '使用量',
   	`max_size` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
   	`max_aggr_count` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
   	`max_aggr_size` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
   	`max_history_count` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_group_id` ( `group_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = '集群、各Group容量信息表';
   /*   
   数据库全名 = nacos_config      
   表名称 = his_config_info   
   */
   CREATE TABLE `his_config_info` (
   	`id` BIGINT ( 64 ) UNSIGNED NOT NULL,
   	`nid` BIGINT ( 20 ) UNSIGNED NOT NULL AUTO_INCREMENT,
   	`data_id` VARCHAR ( 255 ) NOT NULL,
   	`group_id` VARCHAR ( 128 ) NOT NULL,
   	`app_name` VARCHAR ( 128 ) DEFAULT NULL COMMENT 'app_name',
   	`content` LONGTEXT NOT NULL,
   	`md5` VARCHAR ( 32 ) DEFAULT NULL,
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
   	`src_user` text,
   	`src_ip` VARCHAR ( 20 ) DEFAULT NULL,
   	`op_type` CHAR ( 10 ) DEFAULT NULL,
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT '租户字段',
   	PRIMARY KEY ( `nid` ),
   	KEY `idx_gmt_create` ( `gmt_create` ),
   	KEY `idx_gmt_modified` ( `gmt_modified` ),
   	KEY `idx_did` ( `data_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = '多租户改造';
   /*   
   数据库全名 = nacos_config      
   表名称 = tenant_capacity   
   */
   CREATE TABLE `tenant_capacity` (
   	`id` BIGINT ( 20 ) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键ID',
   	`tenant_id` VARCHAR ( 128 ) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
   	`quota` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
   	`usage` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '使用量',
   	`max_size` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
   	`max_aggr_count` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
   	`max_aggr_size` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
   	`max_history_count` INT ( 10 ) UNSIGNED NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
   	`gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
   	`gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_tenant_id` ( `tenant_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = '租户容量信息表';
   CREATE TABLE `tenant_info` (
   	`id` BIGINT ( 20 ) NOT NULL AUTO_INCREMENT COMMENT 'id',
   	`kp` VARCHAR ( 128 ) NOT NULL COMMENT 'kp',
   	`tenant_id` VARCHAR ( 128 ) DEFAULT '' COMMENT 'tenant_id',
   	`tenant_name` VARCHAR ( 128 ) DEFAULT '' COMMENT 'tenant_name',
   	`tenant_desc` VARCHAR ( 256 ) DEFAULT NULL COMMENT 'tenant_desc',
   	`create_source` VARCHAR ( 32 ) DEFAULT NULL COMMENT 'create_source',
   	`gmt_create` BIGINT ( 20 ) NOT NULL COMMENT '创建时间',
   	`gmt_modified` BIGINT ( 20 ) NOT NULL COMMENT '修改时间',
   	PRIMARY KEY ( `id` ),
   	UNIQUE KEY `uk_tenant_info_kptenantid` ( `kp`, `tenant_id` ),
   	KEY `idx_tenant_id` ( `tenant_id` ) 
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COLLATE = utf8_bin COMMENT = 'tenant_info';
   CREATE TABLE users ( username VARCHAR ( 50 ) NOT NULL PRIMARY KEY, PASSWORD VARCHAR ( 500 ) NOT NULL, enabled boolean NOT NULL );
   CREATE TABLE roles ( username VARCHAR ( 50 ) NOT NULL, role VARCHAR ( 50 ) NOT NULL, CONSTRAINT uk_username_role UNIQUE ( username, role ) );
   CREATE TABLE permissions (
   	role VARCHAR ( 50 ) NOT NULL,
   	resource VARCHAR ( 512 ) NOT NULL,
   	action VARCHAR ( 8 ) NOT NULL,
   	CONSTRAINT uk_role_permission UNIQUE ( role, resource, action ) 
   );
   INSERT INTO users ( username, PASSWORD, enabled )
   VALUES
   	( 'nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE );
   INSERT INTO roles ( username, role )
   VALUES
   	( 'nacos', 'ROLE_ADMIN' );
   ```

   

3. 推送镜像

   1. 将配置直接在命令行写出来

       ```shell
       docker run -d \
       --env MODE=standalone \
       --env SPRING_DATASOURCE_PLATFORM=mysql \
       --env MYSQL_SERVICE_HOST=172.18.0.2 \
       --env MYSQL_SERVICE_DB_NAME=nacos_config \
       --env MYSQL_SERVICE_USER=nacos_user \
       --env MYSQL_SERVICE_PASSWORD=nacos_user \
       -p 8848:8848 \
       --name nacos \
       -v /usr/local/docker/nacos/logs:/home/nacos/logs \
   --restart=always \
       nacos/nacos-server:1.3.0
       ```
       
   2. 将配置写在配置文件中

       ```properties
       # env.list
       MODE=standalone
       SPRING_DATASOURCE_PLATFORM=mysql
       MYSQL_SERVICE_HOST=172.18.0.2
       MYSQL_SERVICE_DB_NAME=nacos_config
       MYSQL_SERVICE_USER=nacos_user
       MYSQL_SERVICE_PASSWORD=nacos_user
       ```

       ```shell
       docker run -d \
       --env-file=env.list \
       -p 8848:8848 \
       --name nacos \
       -v /usr/local/docker/nacos/logs:/home/nacos/logs \
       --restart=always \
       nacos/nacos-server:1.3.0
       ```

   > 为了方便，把logs文件夹在主机做了映射，方便查看

   > **PS:**nacos-1.3.0配置如下，别再表面写着1.3.0，里面的参数还在使用master，slaver了。各位作者

| name                              | description                                                  | option                                                       |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| MODE                              | cluster/standalone                                           | cluster/standalone default **cluster**                       |
| NACOS_SERVERS                     | nacos cluster address                                        | eg. ip1:port1 ip2:port2 ip3:port3                            |
| PREFER_HOST_MODE                  | Whether hostname are supported                               | hostname/ip default **ip**                                   |
| NACOS_APPLICATION_PORT            | nacos server port                                            | default **8848**                                             |
| NACOS_SERVER_IP                   | custom nacos server ip when network was mutil-network        |                                                              |
| SPRING_DATASOURCE_PLATFORM        | standalone support mysql                                     | mysql / empty default empty                                  |
| MYSQL_SERVICE_HOST                | mysql host                                                   |                                                              |
| MYSQL_SERVICE_PORT                | mysql database port                                          | default : **3306**                                           |
| MYSQL_SERVICE_DB_NAME             | mysql database name                                          |                                                              |
| MYSQL_SERVICE_USER                | username of database                                         |                                                              |
| MYSQL_SERVICE_PASSWORD            | password of database                                         |                                                              |
| ~~MYSQL_MASTER_SERVICE_HOST~~     | The **latest** version of the image removes this attribute, using MYSQL_SERVICE_HOST |                                                              |
| ~~MYSQL_MASTER_SERVICE_PORT~~     | The **latest** version of the image removes this attribute, using MYSQL_SERVICE_PORT | default : **3306**                                           |
| ~~MYSQL_MASTER_SERVICE_DB_NAME~~  | The **latest** version of the image removes this attribute, using MYSQL_SERVICE_DB_NAME |                                                              |
| ~~MYSQL_MASTER_SERVICE_USER~~     | The **latest** version of the image removes this attribute, using MYSQL_SERVICE_USER |                                                              |
| ~~MYSQL_MASTER_SERVICE_PASSWORD~~ | The **latest** version of the image removes this attribute, using MYSQL_SERVICE_PASSWORD |                                                              |
| ~~MYSQL_SLAVE_SERVICE_HOST~~      | The **latest** version of the image removes this attribute   |                                                              |
| ~~MYSQL_SLAVE_SERVICE_PORT~~      | The **latest** version of the image removes this attribute   | default :3306                                                |
| MYSQL_DATABASE_NUM                | It indicates the number of database                          | default :**1**                                               |
| JVM_XMS                           | -Xms                                                         | default :2g                                                  |
| JVM_XMX                           | -Xmx                                                         | default :2g                                                  |
| JVM_XMN                           | -Xmn                                                         | default :1g                                                  |
| JVM_MS                            | -XX:MetaspaceSize                                            | default :128m                                                |
| JVM_MMS                           | -XX:MaxMetaspaceSize                                         | default :320m                                                |
| NACOS_DEBUG                       | enable remote debug                                          | y/n default :n                                               |
| TOMCAT_ACCESSLOG_ENABLED          | server.tomcat.accesslog.enabled                              | default :false                                               |
| NACOS_AUTH_SYSTEM_TYPE            | The auth system to use, currently only 'nacos' is supported  | default :nacos                                               |
| NACOS_AUTH_ENABLE                 | If turn on auth system                                       | default :false                                               |
| NACOS_AUTH_TOKEN_EXPIRE_SECONDS   | The token expiration in seconds                              | default :18000                                               |
| NACOS_AUTH_TOKEN                  | The default token                                            | default :SecretKey012345678901234567890123456789012345678901234567890123456789 |
| NACOS_AUTH_CACHE_ENABLE           | Turn on/off caching of auth information. By turning on this switch, the update of auth information would have a 15 seconds delay. | default : false                                              |
| MEMBER_LIST                       | Set the cluster list with a configuration file or command-line argument | eg:192.168.16.101:8847?raft_port=8807,192.168.16.101?raft_port=8808,192.168.16.101:8849?raft_port=8809 |
| EMBEDDED_STORAGE                  | Use embedded storage in cluster mode without mysql           | `embedded` default : none                                    |

4. 启动

   这是输入ip:8848/nacos应该是有错误的。查看start.out日志，大概是连接不上MySQL8.

   > 解决办法：
   > **在nacos安装目录下新建plugins/mysql文件夹，并放入8.0+版本的mysql-connector-java-8.0.xx.jar，重启nacos即可**
   >
   > [靠谱](https://blog.csdn.net/w1529132313/article/details/105604629/)

5. Docker中添加mysql的jar包

   ```shell
   宿主机: 
   docker exec -it nacos bash
   docker: 
   mkdir -p /home/nacos/plugins/mysql
   宿主机: 
   docker cp mysql-connector-java-8.0.20.jar nacos:/home/nacos/plugins/mysql/mysql-connector-java-8.0.20.jar
   ```

   > docker中的相关文件夹需要事先创建

6. 重启nacos，Success

   ```shell
   docker restart nacos
   ```

   

