# Linux遭遇挖矿恶意程序kdevtmpfsi，解决方案

昨天晚上18:01，收到腾讯云邮件通知，我的服务器好像倍挖矿程序入侵了。先按照腾讯的建议在相应位置隔离了文件，然后登录上我的宝塔控制面板(不是广告，我只会用这么傻瓜式的管理工具)查看现在服务器的负载情况，不容乐观，好像依然是满负载。停止相关进程、删除掉**kdevtmpfsi**文件，重启机器，好了。今天早上一看，它又出现了。网上查找解决方案

![腾讯云邮件][1]

<!--more-->

------

## 方法

首先看下被入侵之后的情况

![TOP命令][2]

![服务器负载情况][3]

1. 停掉相关运行程序

   ```shell
   ps -aux | grep kdevtmpfsi
   ps -aux | grep kinsing
   ```

   使用`kill -9 [进程号]`命令停掉上述两个进程

2. 删除Linux下的异常定时任务

   一般出现kdevtmpfsi病毒都会伴有定时任务，所以才会反反复复出现处理不干净

   ```shell
   crontab -l 查看定时任务 
   crontab -r 表示删除用户的定时任务，当执行此命令后，所有用户下面的定时任务会被删除
   ```

3. 删除相关文件

   查看相关文件有哪些

   ```shell
   find / -name kdevtmpfsi
   find / -name kinsing
   ```

   ```shell
   rm -rf kdevtmpfsi 
   rm -rf /var/tmp/kinsing
   ```

4. 增强Redis服务安全加固

   > 最根本的原因是自己的redis 6379配置不当导致的，可参考阿里云的[Redis服务安全加固](https://help.aliyun.com/knowledge_detail/37447.html?spm=a2c4g.11186631.2.2.828c1848kuRv6p)



------

现在服务器已经稳定运行了一个小时，没有出现挖矿病毒卷土重来的事情





[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/03/b1deb6d16dc6469fa889f1096eb8b364.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/03/ea5d8844c0d0414eb64982fbaae750fa.png
[3]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/03/329ddea753da46dfa6cc44ac2bb2d1b3.png

