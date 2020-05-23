# Nginx配置域名访问并安装SSL证书

从阿里云申请了一个一年有效期的单域名证书，按照其提供的安装示例，部署在Nginx上面并成功。

<!--more-->

------

以下为自己试验的成功的配置

```nginx
    server {
		listen	80;
        listen	443 ssl;
        server_name  open.nacos.luvnaxx.com;
        index index.html index.htm index.php;
         
        if ($host != 'open.nacos.luvnaxx.com') {
        	return 403;
        }
        
        if ($scheme = http ) {
			return 301 https://$host$request_uri;
		}
        
 		ssl_certificate cert/open.nacos.luvnaxx.com.pem;
        ssl_certificate_key cert/open.nacos.luvnaxx.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_prefer_server_ciphers on;
        
        location / {
        	root html;
            index	/nacos;
        }
        
        # nacos转发
        location /nacos {
        	proxy_pass http://127.0.0.1:8848/nacos;
            proxy_set_header Host $host;

            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout       30;
            proxy_read_timeout          30;
            proxy_send_timeout          30;
        }
    }
```

> 此配置作用：使用ip直接访问，返回403。
>
> 使用http请求，强转为HTTPS
>
> 访问`open.nacos.luvnaxx.com`，变为`open.nacos.luvnaxx.com/nacos`。



## ERR_SSL_PROTOCOL_ERROR出现的原因

在Nginx1.15版本之前，我们配置HTTPS服务是如下这样配置的。

```nginx
server{
	listen	443;
	server_name	网站域名;
	
    ssl	on; # 重点
	
    ssl_certificate	SSL证书;
	ssl_certificate_key	SSL证书私钥;
	ssl_ciphers	加密算法;
	ssl_protocols	加密协议;
	ssl_prefer_server_ciphers	on;
}
```

但是这样的配置如果放在Nginx1.15版本是错误的，启动Nginx的时候系统会报错，原因是**SSL  ON**这样的配置不再支持，需要删除掉。但是当你删除掉那一句代码后，重新启动Nginx就会发现浏览器访问页面时就会出现”**ERR_SSL_PROTOCOL_ERROR**的错误。

那么在Nginx1.15版本下，我们应该做如下配置：

```nginx
server{
	listen	443 ssl; # 重点
	server_name	网站域名;
	ssl_certificate	SSL证书;
	ssl_certificate_key	SSL证书私钥;
	ssl_ciphers	加密算法;
	ssl_protocols	加密协议;
	ssl_prefer_server_ciphers	on;
}
```

