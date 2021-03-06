---
title: 记一次 nginx 代理问题
date: 2019-06-30
image: 4DC29ACB-678C-4221-AFF1-728A6AB2C449.png
tags:
  - Nginx
categories:
  - Nginx
---

## 最终实现效果：

配置 ssl 证书，http 自动跳转 https。所有 https 请求代理到另外的 http 服务上。

## 遇到的问题与解决方式：

### 1、http 自动跳转 https

这里跳转方式，百度就有好多
下面是腾讯给出的方式，也是大多数采用的方式

```
server {
    listen 80;
    server_name www.domain.com; #填写绑定证书的域名
    rewrite ^(.*)$ https://$host$1 permanent; #把http的域名请求转成https
}
```

这种方式，我感觉不太直观。于是我使用的是下面这种使用 301 重定向的方法

```
server {
    listen 80; #可以省略，默认就是80
    server_name ***; #域名
    return 301 https://$server_name;
}
```

### 2、https 请求代理到 http 服务上

```
location / {
    proxy_pass http://xxx; #代理服务地址
    proxy_set_header Host $host; #传输服务器地址
    proxy_set_header X-Real-IP $remote_addr; #传输请求地址
    proxy_set_header X-Forwarded-Proto $scheme; #传递传输协议（主要就是这个指令）
}
```
