---
title: MobaXterm配置
published: 2026-06-15
description: 主要解决MobaXterm连接ensp设备乱码以及解除存储会话限制的问题
tags: [工具, MobaXterm]
category: 软件破解
draft: false
image: api
---

# 连接ensp设备显示乱码的问题
1. 找到MobaXterm安装目录下的`MobaXterm.ini`
2. 用记事本或其他软件打开此文件，在最上方添加
```
[MottyOptions]  
LocalEcho=1  
LocalEdit=1
```
>[!NOTE] Tips
>需要删除`MobaXterm backup.zip`防止备份覆盖，不删的话可能不会成功解决

# 解除存储会话上限
1. 访问 [MobaXterm-Keygen](https://moba-xterm-keygen.vercel.app)
2. 选择版本类型，填写用户名（随便写）、版本号以及用户数量（点击软件的上方的`Help`查看版本类型和版本号）
3. 点击`GENERATE LICENSE`会自动下载一个名为`Custom.mxtpro`的文件，将这个文件移动到MobaXterm的安装目录就完成了

**该项目的GitHub地址如下：**
::github{repo="zarfadev/MobaXterm-Keygen"}

**其他资源：**
MobaXterm安装包：
- [MobaXterm官网](https://mobaxterm.mobatek.net)
- [天翼云盘](https://cloud.189.cn/t/v6zeemmmUjeu)：访问码：w8sx





