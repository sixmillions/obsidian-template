---
title: obsidian发布Web
slug: publish-by-oleeskild-digital-garden
description: 通过oleeskild提供的模板(digitalgarden)，将笔记部署到vercel，方便分享和访问
author: six
created: 2023-08-23
updated: 2023-08-24
cover: https://s.sixmillions.cn/img/logo/logo.png
tags:
  - obsidian
categories:
  - obsidian
dg-publish: true
link: https://6bw.notion.site/publish-by-oleeskild-digital-garden-b720b8156c554b58987918520ade9155
notionID: b720b815-6c55-4b58-9879-18520ade9155
---
## 发布笔记

使用 `Digital Obsidian Garden` 发布Obsidian笔记

> https://dg-docs.ole.dev/
## 准备

1. GitHub账号，并且申请一个token
2. Vercel账号，先登录，关联GithHub账号

## 部署

### 点击部署

访问

https://github.com/oleeskild/digitalgarden

点击 “Deploy” 按钮，会跳转到vercel部署界面

### 填写仓库地址

vercel会自动创建该Git仓库

![deploy](https://s.sixmillions.cn/img/2023/08/24/074904144.png)

## 下载插件

Obsidian下载插件 `Digital Garden` ，然后配置插件

> 手动下载地址：https://github.com/oleeskild/obsidian-digital-garden

- GItHub仓库名（上面创建的）
- GitHub用户名
- GetHub的token

## 发布笔记

新建笔记，然后输入front-matter

```yaml
---
dg-home: true
dg-publish: true
---
```

- dg-home是主页，有一个文件配置即可
- dg-publish，只有配置了，并且是true，才会发布

`ctrl + p` -> `dgps` (Digital Garden: Publish Single Note)

访问vercel项目的地址

## 修改

知道了它的套路

1. 发布笔记的时候就是向 `src/site/notes` 上传md文件
2. obsidian更新该插件的配置的时候，本地存在 `data.json` ,修改Git仓库的 `.env` 中对应值
3. 升级了一些依赖包版本
4. 修改了 `favicon.svg`
5. 修改了图片的式样，修改了文章显示的式样

按照自己的喜好定制后，上传到Git仓库，然后在Vercel部署

部署模板选择 `Eleventy`

![](https://s.sixmillions.cn/img/2023/08/24/061540050.png)


插件本地配置

![](https://s.sixmillions.cn/img/2023/08/24/060255509.png)

`data.json` 如下

```json
{
  "githubRepo": "obsidian-blog",
  "githubToken": "xxxxx",
  "githubUserName": "sixmillions",
  "gardenBaseUrl": "https://blog.6bw.fun",
  "prHistory": [],
  "baseTheme": "dark",
  "theme": "{\"name\":\"Red Graphite\",\"author\":\"SeanWcom\",\"repo\":\"seanwcom/Red-Graphite-for-Obsidian\",\"screenshot\":\"thumbnail.png\",\"modes\":[\"dark\",\"light\"],\"cssUrl\":\"https://raw.githubusercontent.com/seanwcom/Red-Graphite-for-Obsidian/HEAD/theme.css\"}",
  "faviconPath": "",
  "noteSettingsIsInitialized": true,
  "siteName": "Six Blog",
  "slugifyEnabled": false,
  "noteIconKey": "dg-note-icon",
  "defaultNoteIcon": "",
  "showNoteIconOnTitle": false,
  "showNoteIconInFileTree": false,
  "showNoteIconOnInternalLink": false,
  "showNoteIconOnBackLink": false,
  "showCreatedTimestamp": true,
  "createdTimestampKey": "created",
  "showUpdatedTimestamp": true,
  "updatedTimestampKey": "updated",
  "timestampFormat": "MMM dd, yyyy h:mm a",
  "styleSettingsCss": "",
  "pathRewriteRules": "",
  "customFilters": [],
  "contentClassesKey": "dg-content-classes",
  "defaultNoteSettings": {
    "dgHomeLink": true,
    "dgPassFrontmatter": true,
    "dgShowBacklinks": false,
    "dgShowLocalGraph": false,
    "dgShowInlineTitle": true,
    "dgShowFileTree": true,
    "dgEnableSearch": true,
    "dgShowToc": true,
    "dgLinkPreview": false,
    "dgShowTags": true
  }
}
```

## 时间格式化

配置中的时间格式化，用到的luxon

> https://github.com/moment/luxon/blob/master/docs/formatting.md
## 注意

该网站使用  [Eleventy(11ty)](https://www.11ty.cn/) 生成，该模板不能出现类似于vue那种胡子语法

即不要出现双大括号中间是变量名，否则部署的时候会报错

```text
[11ty] Original error stack trace: Template render error: (./src/site/notes/obsidian/use-templdates.md) [Line 14, Column 23]
[11ty]   expected variable end
```

![](https://s.sixmillions.cn/img/2023/08/24/075311287.png)

## 修改头像

修改默认favicon，如果原来不配置会使用默认的，我们修改成自己仓库的

![](https://s.sixmillions.cn/img/2023/08/28/075701283.png)

## DIY

### 修改search

使用[[zincsearch]]替代

原理：
1. 删除旧索引
2. 创建新索引
3. 通过 `/api/searchIndex.json` 接口获取文章数据
4. 将数据插入新的索引

定时任务，每天凌晨运行，数据同步到zincsearch脚本

```bash
#!/bin/bash

HOST=https://zinc.sixmillions.cn
BLOG_HOST=https://blog.6bw.fun
INDEX=blog
TOKEN=xxxx

# 删除索引
curl -X 'DELETE' \
  "${HOST}/api/index/${INDEX}" \
  -H 'accept: application/json' \
  -H "authorization: Basic ${TOKEN}"

# 创建索引
curl -X 'POST' \
  "${HOST}/api/index" \
  -H 'accept: application/json' \
  -H "authorization: Basic ${TOKEN}" \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "'${INDEX}'",
  "storage_type": "disk",
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "type": "gse_standard"
        }
      }
    }
  },
  "mappings": {}
}'


# 获取数据
data=$(curl -s "${BLOG_HOST}/searchIndex.json")

# 构建请求数据
request_data='{
  "index": "'${INDEX}'",
  "records": '$data'
}'

# 发送请求
curl -X 'POST' \
  "${HOST}/api/_bulkv2" \
  -H 'accept: application/json' \
  -H "authorization: Basic ${TOKEN}" \
  -H 'Content-Type: application/json' \
  -d "$request_data"
```

注意：**token要有相应权限**

token就是 `username:password` 做base64，可以使用在线工具

> https://www.sojson.com/base64.html


