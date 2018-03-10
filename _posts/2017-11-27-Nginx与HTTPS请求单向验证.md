---
layout: post
title: Nginx与https认证
categories: Java
tags: Nginx

---

### 1）概述 ###

加密算法：RSA 非对称   公钥私钥

1000 -CN 搜索内容

中国->美国（USA）dns

google

Nginx对请求信息进行解析

锁 公钥    （Nginx）钥匙：私钥

流程：制作钥匙-制作锁-解密-Nginx配套-

### 2）示例 ###

- 编译安装Nginx -- with-http_ssl_module
- 创建服务器私钥
openssl genrsa -des3 -out server.key 1024 
- 创建签名请求证书（CSR）
openssl req -new -key server.key -out server.csr
- 加载SSL支持的Nginx并使用私钥时去除口令
cp server.key server.key.bak
openssl rsa -in server.key.bak -out server.key
- 自动签发证书
openssl x509 -req -days 10240 -in server.key -out server.crt
- 将key、crt拷到Nginx的conf下

https://www.asxx.com->443->crt下载到本地浏览器
