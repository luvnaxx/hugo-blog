# 腾讯云对象存储POST Object

> ## 功能描述
>
> POST Object 接口请求可以将本地不超过5GB的对象（Object）以网页表单（HTML Form）的形式上传至指定存储桶中。该 API 的请求者需要对存储桶有写入权限。

<!--more-->

> 后端使用Java生成前端表单所需数据，前端文件直传。参考[POST Object](https://cloud.tencent.com/document/product/436/14690)

#### 签名保护

POST Object 接口要求在请求中携带签名相关字段，COS 服务器端收到消息后，进行身份验证，验证成功则可接受并执行请求，否则将会返回错误信息并丢弃此请求。



----

签名流程

#### 1. 准备工作

获取SecretId 和 SecretKey

#### 2. 生成 KeyTime

```java
  public String generateKeyTime() {
    long startTimestamp = System.currentTimeMillis() / 1000;
    long endTimestamp = startTimestamp + 900;
    return startTimestamp + ";" + endTimestamp;
  }
```

有效时间为900秒

#### 3. 构造“策略”（Policy）

策略为一个 JSON 文本，一个典型的策略如下

```json
{
    "expiration": "2019-08-30T09:38:12.414Z",
    "conditions": [
        { "acl": "default" },
        { "bucket": "examplebucket-1250000000" },
        [ "starts-with", "$key", "folder/subfolder/" ],
        [ "starts-with", "$Content-Type", "image/" ],
        [ "starts-with", "$success_action_redirect", "https://my.website/" ],
        [ "eq", "$x-cos-server-side-encryption", "AES256" ],
        { "q-sign-algorithm": "sha1" },
        { "q-ak": "AKIDQjz3ltompVjBni5LitkWHFlFpwkn9U5q" },
        { "q-sign-time": "1567150692;1567157892" }
    ]
}
```

```java
  public String generatePolicy(String secretId, String keyTime) {
    LocalDateTime dateTime = LocalDateTime.now();
    LocalDateTime localDateTime = dateTime.plusMinutes(15);
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'");
    String expiration = localDateTime.format(formatter);

    String template = "{\"expiration\":\"%s\",\"conditions\":[{\"q-sign-algorithm\":\"sha1\"},{\"q-ak\":\"%s\"},{\"q-sign-time\":\"%s\"},{\"acl\":\"default\"},{\"x-cos-storage-class\":\"STANDARD\"}]}";
    String format = String.format(template, expiration, secretId, keyTime);
    return format;
  }
```

#### 4. 生成 SignKey

使用 HMAC-SHA1 以 SecretKey 为密钥，以 KeyTime 为消息，计算消息摘要（哈希值，16进制小写形式），即为 SignKey

```java
  /**
   * 使用指定HMAC算法加密
   *
   * @param message   待加密数据,KeyTime 
   * @param key       密钥,SecretKey 
   * @param algorithm 算法,HMACSHA1
   * @return 加密结果的十六进制形式
   */
  @SneakyThrows
  public String encryption(String message, String key, String algorithm) {
    byte[] data = key.getBytes(StandardCharsets.UTF_8);
    //根据给定的字节数组构造一个密钥,第二参数指定一个密钥算法的名称
    SecretKey secretKey = new SecretKeySpec(data, algorithm);
    //生成一个指定 Mac 算法 的 Mac 对象
    Mac mac = Mac.getInstance(algorithm);
    //用给定密钥初始化 Mac 对象
    mac.init(secretKey);

    byte[] text = message.getBytes(StandardCharsets.UTF_8);
    byte[] bytes = mac.doFinal(text);
    return HexUtil.encodeHexStr(bytes);
  }
```

#### 5. 生成 StringToSign

使用 SHA1 对上文中构造的策略（Policy）文本计算消息摘要（哈希值，16进制小写形式），即为 StringToSign

```java
  /**
   * 使用hash算法
   *
   * @param str       加密字符串,Policy
   * @param algorithm hash算法,SHA1 
   * @return 解密结果
   */
  @SneakyThrows
  public String hashEncryption(String str, String algorithm) {
    // 创建一个MessageDigest实例:
    MessageDigest md = MessageDigest.getInstance(algorithm);
    // 反复调用update输入数据:
    md.update(str.getBytes(StandardCharsets.UTF_8));
    byte[] result = md.digest();
    return HexUtil.encodeHexStr(result);
  }
```

#### 6. 生成 Signature

使用 HMAC-SHA1 以 SignKey 为密钥（字符串形式，非原始二进制），以 StringToSign 为消息（字符串形式，非原始二进制），计算消息摘要（哈希值，16进制小写形式），即为 Signature

```java
  /**
   * 使用指定HMAC算法加密
   *
   * @param message   待加密数据,StringToSign  
   * @param key       密钥,SignKey  
   * @param algorithm 算法,HMACSHA1
   * @return 加密结果的十六进制形式
   */
  @SneakyThrows
  public String encryption(String message, String key, String algorithm) {
    byte[] data = key.getBytes(StandardCharsets.UTF_8);
    //根据给定的字节数组构造一个密钥,第二参数指定一个密钥算法的名称
    SecretKey secretKey = new SecretKeySpec(data, algorithm);
    //生成一个指定 Mac 算法 的 Mac 对象
    Mac mac = Mac.getInstance(algorithm);
    //用给定密钥初始化 Mac 对象
    mac.init(secretKey);

    byte[] text = message.getBytes(StandardCharsets.UTF_8);
    byte[] bytes = mac.doFinal(text);
    return HexUtil.encodeHexStr(bytes);
  }
```

#### 7. 将签名附加到表单

将上述策略和签名相关信息，以下表中描述的方式附加到表单中：

| 名称             | 描述                                   | 类型   | 是否必选 |
| :--------------- | :------------------------------------- | :----- | :------- |
| policy           | 经过 Base64 编码的“策略”（Policy）内容 | string | 是       |
| q-sign-algorithm | 签名哈希算法，固定为 sha1              | string | 是       |
| q-ak             | 上文所述的 SecretId                    | string | 是       |
| q-key-time       | 上文所生成的 KeyTime                   | string | 是       |
| q-signature      | 上文所生成的 Signature                 | string | 是       |

> 签名表单字段需要在 file 表单字段之前。

![上传表单][1]

![上传结果][2]

我的完整上传示例

[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/6991176cd45c4ca1bf232294480cc6c1.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/14c6e076e555443fab2ce8ab2b6a7213.png