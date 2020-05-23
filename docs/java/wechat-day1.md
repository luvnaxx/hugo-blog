# 微信公众号开发-day1

> 需求：开通一订阅号，通过点击下方菜单或者输入关键字，返回指定信息。
>
> 暂时设想的是快速查询**黄金9999**实时价格

以[微信官方文档·公众号](https://developers.weixin.qq.com/doc/offiaccount/Getting_Started/Overview.html)为开发文档

> 参考[歌德的博客](https://www.cnblogs.com/gede/category/1467638.html)完成Java后台代码实现

**目标：填写服务器配置，验证服务器地址的有效性**

<!--more-->

![目标][1]

> 服务器配置Java实现

1. 新建Spring Boot项目，添加web模块

   ```xml
       <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   ```

2. 创建controller和util包，创建`WxController.java`用来接收微信发送给后台的数据，`CheckUtil.java`用来验证该数据是否来自于微信后台。

   > 验证逻辑如下

   ![验证逻辑][2]



3. 代码如下

   1. WxController.java

   ```java
   /**
    * 微信专用测试
    *
    * @author 王越洋
    */
   @RestController
   @RequestMapping("/wx")
   public class WxController {
   
     @GetMapping("")
     public String index(HttpServletRequest request) {
   
       String signature = request.getParameter("signature");
       String timestamp = request.getParameter("timestamp");
       String nonce = request.getParameter("nonce");
       String echostr = request.getParameter("echostr");
   
       if (CheckUtil.checkSignature(signature, timestamp, nonce)) {
         return echostr;
       }
   
       return "bad request";
     }
   }
   ```

   2. CheckUtil.java

   ```java
   import java.nio.charset.StandardCharsets;
   import java.security.MessageDigest;
   import java.util.Arrays;
   
   /**
    * 检验signature对请求进行校验
    * <p>
    * 开发者通过检验signature对请求进行校验（下面有校验方式）。若确认此次GET请求来自微信服务器，请原样返回echostr参数内容，则接入生效，成为开发者成功，否则接入失败。加密/校验流程如下：
    * <p>
    * 1）将token、timestamp、nonce三个参数进行字典序排序
    * 2）将三个参数字符串拼接成一个字符串进行sha1加密
    * 3）开发者获得加密后的字符串可与signature对比，标识该请求来源于微信
    *
    * @author 王越洋
    */
   public class CheckUtil {
   
     /**
      * 开发者自行定义Token
      */
     public static final String TOKEN = "diy_token";
   
     public static boolean checkSignature(String signature, String timestamp, String nonce) {
   
       // 1.定义数组存放token，timestamp,nonce
       String[] arr = {TOKEN, timestamp, nonce};
   
       // 2.对数组进行排序
       Arrays.sort(arr);
   
       // 3.生成字符串
       StringBuilder sb = new StringBuilder();
       for (String s : arr) {
         sb.append(s);
       }
   
       // 4.sha1加密
       String temp = getSha1(sb.toString());
   
       // 5.将加密后的字符串，与微信传来的加密签名比较，返回结果
       return temp.equals(signature);
   
     }
   
   
     public static String getSha1(String str) {
   
       if (str == null || str.length() == 0) {
         return null;
       }
   
       char[] hexDigits = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e',
           'f'};
   
       try {
   
         MessageDigest mdTemp = MessageDigest.getInstance("SHA1");
         mdTemp.update(str.getBytes(StandardCharsets.UTF_8));
   
         byte[] md = mdTemp.digest();
         int j = md.length;
         char[] buf = new char[j * 2];
         int k = 0;
   
         for (byte byte0 : md) {
           buf[k++] = hexDigits[byte0 >>> 4 & 0xf];
           buf[k++] = hexDigits[byte0 & 0xf];
         }
         return new String(buf);
   
       } catch (Exception e) {
         e.printStackTrace();
         return null;
       }
     }
   }
   ```

     

4. 设置项目启动端口

   > 微信要求http端口为80，但是我的服务器80端口无法启动。使用Nginx转发解决

   ```properties
   server.port=8081
   ```

5. 打包上传至服务器启动

6. 设置Nginx

   ```xml
       server {
         listen 80;
         server_name 127.0.0.1:8081;
         location / {
           proxy_pass  http://127.0.0.1:8081;
         }
       }
   ```

7. 此时已经能够提交验证通过



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/202003/31fb49be45e34aad869315270b8f371d.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/202003/f3a4a0133e5e47ed8cb15fa05ddeec39.jfif

