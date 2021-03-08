title: hexo-generator-search + wordcount
author: dogh
tags:
  - hexo
  - blog
categories:
  - blog
date: 2020-10-08 20:49:00
---
# hexo-generator-search 本地搜索

1. 根据[hexo-generator-search](https://github.com/wzpan/hexo-generator-search)提供的文档进行配置

   - 在hexo文件根目录中shift + 右键在当前目录打开powershell。

   - 输入`npm install hexo-generator-search --save`

     ![image-20210307211018804](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20210307211018804.png)

   - 在_config.yml中添加

     ``` yaml
     search:
       path: search.xml
       field: post
       content: true
     ```

     

2. 在_config.butterfly.yml中将hexo-generator-search 改为true；



# 字数统计

1. 打开hexo目录，powershell

2. `npm install hexo-wordcount --save`

3. 修改_config.butterfly.yml

   ```yaml
   wordcount:
     enable: true
     post_wordcount: true
     min2read: true
     total_wordcount: true
   ```

   