# Simple Let's encrypt client concept in PHP

> **Note**: lescript is standalone part of [LEManager](https://github.com/analogic/lemanager)

Lescript is simplified concept of ACME client implementation especially for Let's Encrypt service. It's goal is to have one 
easy to use PHP file without dependencies. 

**Use at your own risk.**

## Usage

See commented content of **Lescript.php** and **_example.php**. Please rewrite files to fit your needs - purpose of this library is not to use as it is nor use it in production!

Support **challenge only through webroot**.

## Requirements

- PHP 5.3 and up
- OpenSSL extension
- Curl extension

## Why i created lescript?

Because of implementation of Let's Encrypt to [Poste.io](https://poste.io)!

## nginx 域名自动加载证书设置（站在巨人的肩上，添加一点说明）

> 修改默认nginx配置文件 nginx.conf 增加空主机头设置

```
server
    {
        listen 80;
        listen 443 ssl http2;
        server_name default;
        index index.php index.html index.htm default.php default.htm default.html;
        root /www/wwwroot/domain;
        
        #SSL-START SSL相关配置，请勿删除或修改下一行带注释的404规则
        #error_page 404/404.html;
        ssl_certificate    /usr/local/lescript/$ssl_server_name/fullchain.pem;
        ssl_certificate_key    /usr/local/lescript/$ssl_server_name/private.pem;
        ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
        ssl_prefer_server_ciphers on;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
        add_header Strict-Transport-Security "max-age=31536000";
        error_page 497  https://$host$request_uri;

        #SSL-END
        
        #ERROR-PAGE-START  错误页配置，可以注释、删除或修改
        #error_page 404 /404.html;
        #error_page 502 /502.html;
        #ERROR-PAGE-END
        
        #PHP-INFO-START  PHP引用配置，可以注释或修改
        include enable-php-74.conf;
        #PHP-INFO-END
        
        #REWRITE-START URL重写规则引用,修改后将导致面板设置的伪静态规则失效
        #include /www/server/panel/vhost/rewrite/$host;
        #REWRITE-END
        
        #禁止访问的文件或目录
        location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
        {
            return 404;
        }
        
        #一键申请SSL证书验证目录相关设置
        location ~ \.well-known{
            allow all;
        }
        
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
            error_log /dev/null;
            access_log /dev/null;
        }
        
        location ~ .*\.(js|css)?$
        {
            expires      12h;
            error_log /dev/null;
            access_log /dev/null; 
        }
        access_log  /www/wwwlogs/$ssl_server_name.log;
        error_log  /www/wwwlogs/$ssl_server_name.error.log;
    }
```
