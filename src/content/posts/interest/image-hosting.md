---
title: 看看我使用的图床
published: 2026-06-18
category: 博客相关
description: "分享一下我使用的图床以及搭建的方式"
tags: [图床, Docker, CloudFlare-ImgBed]
draft: false
image: api
---

# 图床介绍
**项目地址：**
::github{repo="MarSeventh/CloudFlare-ImgBed"}
[**官方演示站点：**](https://cfbed.1314883.xyz/)(访问码:`cfbed`)

**CloudFlare ImgBed**是一个基于 Cloudflare 的开源文件托管解决方案，为用户提供免费、稳定、高效的文件存储服务。项目支持多种存储渠道，支持无服务器和有服务器部署方式，满足不同用户的需求。

# 部署流程
## 部署方式
|部署方式|费用|
| --- | --- |
|Cloudflare Pages|一定额度内免费|
|Cloudflare Workers|一定额度内免费|
|Docker|服务器费用|
|手动部署|服务器费用|

我使用的是`Docker`部署，其他方式可以参考[官方文档](https://cfbed.sanyue.de/)

## 启动命令
```bash
docker run -d \
  --name cloudflare-imgbed \
  -p 7658:8080 \
  -v /root/cloudflare-imgbed/data:/app/data \
  marseventh/cloudflare-imgbed:latest
```
我直接将文件映射到了`/root/cloudflare-imgbed`目录下（可以按照自己的需求修改路径）。

启动完成后访问`http://你的服务器IP:7658`,首次访问不需要认证，建议点击右下角设置`系统管理-系统设置-安全设置`，完成用户端认证和管理端认证的配置。

## 存储渠道配置
目前最新版支持下面几种存储渠道：
|渠道类型|优点|限制|
| --- | --- | --- |
|Telegram Bot|完全免费、无限容量|大于20MB文件需分片存储|
|Cloudflare R2|无文件大小限制、企业级性能|超出10G免费额度后收费，需要绑定支付方式|
|S3 兼容存储|选择多样、价格灵活|根据服务商定价|
|Discord|完全免费、简单易用|大于10MB文件需分片存储|
|HuggingFace|完全免费、支持大文件直传|需要 HuggingFace 账号|
|WebDAV|兼容性广、支持自建存储|根据服务商定价|

使用`Docker`部署时，会有一个默认的存储配置，在`Cloudflare R2`分组下，实际上使用的是服务器本地的存储，存储目录为`/root/cloudflare-imgbed/data/r2`。

配置参数获取请参考[官方文档：渠道配置](https://cfbed.sanyue.de/deployment/prerequisites.html)


# 配置反向代理

我是用的是`Nginx`反向代理，在初次配置时在`Telegram Bot`渠道下的图片可以成功上传，但是访问时会报404错误。经过搜索（[参考链接](https://github.com/MarSeventh/CloudFlare-ImgBed/discussions/505)），能正常运行的配置示例如下
```nginx
location / {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-NginX-Proxy true;

            real_ip_header X-Real-IP;

            proxy_connect_timeout 300;
            proxy_http_version 1.1;

            proxy_pass http://127.0.0.1:7658;
        }
```



