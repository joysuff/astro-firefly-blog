---
title: Git代理配置
published: 2026-06-14
update: 2026-06-14
description: "git代理的配置与删除"
tags: [Git,Proxy]
category: "版本控制工具"
draft: false
image: api
---
# 全局代理（所有仓库生效）
## 配置 HTTP/HTTPS 代理
```bash
# 设置 http 代理
git config --global http.proxy http://127.0.0.1:7890
# 设置 https 代理
git config --global https.proxy http://127.0.0.1:7890
```
## 配置SOCKS5代理
```bash
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890
```
## 查看全局代理
```bash
git config --global --get http.proxy
git config --global --get https.proxy
```
## 删除全局代理
```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

# 局部代理（仅当前仓库生效）
只对**当前文件夹下的Git仓库生效**，加`--local`:
## 配置代理
```bash
# HTTP 局部代理
git config --local http.proxy http://127.0.0.1:7890
git config --local https.proxy http://127.0.0.1:7890

# SOCKS5 局部代理
git config --local http.proxy socks5://127.0.0.1:7890
git config --local https.proxy socks5://127.0.0.1:7890
```
## 查看当前仓库配置的代理
```bash
git config --local --get http.proxy
git config --local --get https.proxy
```
## 删除当前仓库配置的代理
```bash
git config --local --unset http.proxy
git config --local --unset https.proxy
```
# 临时取消代理（仅对当前终端生效）
```bash
unset http_proxy
unset https_proxy
```