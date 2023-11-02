---
title: nginx安装及配置
linktitle: nginx安装及配置
type: book
date: 2022-04-03 20:35:25.000 +0800

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 150
---
## 简介
本文旨在完成nginx的安装以及前端和后端的转发配置（以linux为例）
## 获取安装包
nginx官网：https://nginx.org/en/download.html  
下载其中的Mainline version
## 安装nginx
首先解压至任意路径，然后cd进入解压后的文件夹，执行以下命令：
~~~bash
./configure --prefix=/usr/local/nginx --with-http_ssl_module 
~~~
这一步是用来配置要安装的nginx，其中，
- `--prefix=/usr/local/nginx` 指定安装路径;
- `--with-http_ssl_module` 支持https;

> 对于安装好的nginx，查看./configure后指定的参数可使用以下命令：
> ~~~bash
>  ./sbin/nginx -V
> ~~~
再执行一下命令：
~~~bash
make install
~~~
这样，nginx就安装好了，可以进入nginx的安装路径，执行相应的路由转发配置了
## 配置nginx
nginx的配置在文件conf/nginx.conf中  
基本结构:
~~~
http {
    # 配置http
    server {
        # 监听端口
        listen       80;
        server_name  localhost;
        # 指定要拦截的路径 
        location / { 
            root   html;
            index /index.html;
        }
    }
    # 配置https
    server {
        listen       443 ssl;
        server_name  songchuanrang.space;

        ssl_certificate      songchuanrang.space_bundle.crt;
        ssl_certificate_key  songchuanrang.space.key;

        ssl_session_timeout  5m;

        ssl_protocols TLSv1.2 TLSv1.3;

        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers  on;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
        location /api/ {
            proxy_pass http://gateway/;
        }
    }
}
~~~
配置前端项目
~~~
server {
# 指定前端编译后的路径
    root /path/to/dist;
    location / {
        # 前端项目默认主页
        index /index.html;
}
~~~
配置后端项目
~~~
http {
    # 反向代理的后端项目(gateway为自定义的字符串)
    upstream gateway {
        # 指定后端项目的ip:port
        server 127.0.0.1:9632;
    }
    server {
    # 指定要拦截的请求路径，把location后的路径（包括“/”），替换为proxy_pass后指定的路径（同样包含“/”）
    location /api/ {
        # 指定后端项目
        proxy_pass http://gateway/;
    }
}
~~~

## 启动nginx
~~~bash
# 启动
sbin/nginx
# 检查配置文件合法性 
sbin/nginx -t
# nginx 热更新配置
sbin/nginx -s reload
# fast shutdown
nginx -s stop
# graceful shutdown
nginx -s quit
~~~

