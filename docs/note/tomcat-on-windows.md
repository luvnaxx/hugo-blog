# Windows端Tomcat部署Java-Web程序中文乱码解决

在Windows上Tomcat部署Java Web程序进行功能测试时，出现了部分中文乱码的问题

可能是机器默认编码为GBK，而程序设置为UTF-8所致

<!--more-->

------

## 解决方案

Step 1.

在系统环境变量中增加一个变量，变量名为: `JAVA_TOOL_OPTIONS`， 变量值为: `-Dfile.encoding=UTF-8`  不然jdk会取系统默认字符集gbk 导致一些地方出错



Step 2.

修改Tomcat的配置文件**server.xml**

将相关语句改为：
<Connector port="8008" protocol="HTTP/1.1"
       connectionTimeout="20000"
       redirectPort="8443" URIEncoding="UTF-8"/>

