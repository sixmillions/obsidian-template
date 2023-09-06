---
title: 00-readme
slug: obsidian-env-init
description: obsidian新工作区初始化
author: six
created: 2023-08-28
updated: 2023-09-06
cover: https://picsum.photos/720/400
tags:
  - obsidian
categories:
  - obsidian
dg-publish: true
---
## 环境初始化

使用自己的工作区模板

```bash
git clone https://github.com/sixmillions/obsidian-templdate.git
```
## 配置介绍

[[my-obsidian-config]]

## 插件介绍

[[my-obsidian-plugins]]
## 需要修改

clone模板后，需要要修改的地方

###  `Remotely Save` 存储桶修改

默认未启用，如果启用了，需要修改配置，

我是同步到了自建MinIO上

1. Minio地址，区域，密钥（申请）
2. Bucket名字（提前建立好）
3. 自建MinIO使用 `Path-Style` 

![](https://s.sixmillions.cn/img/2023/09/02/001323630.png)


### `Digital Garden` 修改

默认未启用

GitHub建立新的笔记发布仓库后，发布到Vercel，然后修改插件配置中的仓库名字

Cloudflare建立子域名，指向Vercel

1. 修改仓库名
2. 修改GitHub账号和Token
3. 还有博客地址

![](https://s.sixmillions.cn/img/2023/09/02/001810285.png)
### `Image Uploader` 修改

默认未启用，图片会保存到 `attachments` 文件夹

1. 修改图片上传接口
2. 如果接口需要认证则添加认证Header
3. 修改图片链接地址所在字段
![](https://s.sixmillions.cn/img/2023/09/06/021519104.png)

### `Obsidian Git`

默认未启用

该插件可以将数据上传Git

这就要求你笔记仓库已经初始化Git，并且配置了远端Git仓库地址

```bash
git remote set-url origin https://github.com/your_name/your_repo
```

### `Obsidian shared to Notion`

默认未启用

将笔记发布到Notion，需要配置

