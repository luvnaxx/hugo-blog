# 微信公众号开发模式-dayN

> 需求：开通一订阅号，通过点击下方菜单或者输入关键字，返回指定信息。
>
> 暂时设想的是快速查询**黄金9999**实时价格

2020年11月5日，观察后台日志，再次发现如下错误日志。

![问题][1]

搜索查下大概是tomcat版本过高的问题

<!--more-->

解决方案

![修改Spring Boot启动类][2]

修改Spring Boot的启动类。



>  正好之前腾讯云提醒我说我Spring Framework的版本有漏洞，将Spring Boot版本升级至高版本



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/11/d397caf5ff144337820b5d4f56362f96.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/11/a79b041c6279414b91ffb27244d558ad.png