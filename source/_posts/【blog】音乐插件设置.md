title: 【blog】音乐插件设置
author: dogh
tags:
  - blog
categories:
  - blog
date: 2020-10-09 10:44:00
---
#  添加音乐播放器插件

## 网易云播放器

设置教程参考[这个](https://blog.csdn.net/mculover666/article/details/90700059)

## 明月浩空播放器

设置教程参考[这个](https://blog.csdn.net/qq_46921028/article/details/108910095)

## 阿涛播放器【推荐】

在使用了明月浩空之后，发现这一款，基本和明月浩空区别不大，最重要的是没有广告，免费用户没啥限制（后期应该可以按需添加广告）n m  

1. 在这个网站上进行注册，[https://player.ataoxz.com/](https://player.ataoxz.com/)
2. 点击添加播放器，

![image-20210310101115917](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20210310101115917.png)

3. 进行域名授权，输入自己博客的访问域名。然后点击`获取代码`复制提供的插件代码。

![image-20210310101326738](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20210310101326738.png)

4. 设置完成后获取代码，添加进主题配置文件中（_config.butterfly.yml)

- 在主题配置文件中设置音乐插件开启

  ```yaml
  # Inject the css and script (aplayer/meting)
  aplayerInject:
    enable: true
    per_page: true
  ```

- 插入播放器插件代码,在bottom哪里，将\<div>和\<script>里的内容替换成刚复制的内容

  ```YAML
  inject:
    head:
      # - <link rel="stylesheet" href="/xxx.css">
    bottom:
      <script src="//lib.baomitu.com/jquery/3.4.1/jquery.min.js"></script>
      <div id="music" key="60481bf5d8c60" api="https://player.ataoxz.com"></div><script id="xplayer" src="https://player.ataoxz.com/api/PlayerJs/id/60481bf5d8c60" ></script>
  
  ```

5. 如果想要每个页面都继续播放，音乐不中断，在主题文件中设置pajx为true

6. 播放器和明月浩空相比，免费版支持歌单导入，可以无缝对接自己的网易云音乐账号，和导入别人的歌单了。

   - 打开网易云音乐，选择要导入的歌单。在Ip地址栏复制ID

     ![image-20210310103325972](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20210310103325972.png)

   - 在阿涛播放器的歌单中选择歌单导入，导入完后，去播放器添加歌单，导入和添加都要记得保存列表。

     ![image-20210310103432004](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20210310103432004.png)

     至此播放器插件设置完成，生效hexo即可在自己的博客中看到插件了，由于设置了域名授权，在使用localhost/4000访问时是无法播放音乐的。

     参考[blog](https://butterfly.js.org/posts/507c070f/#%E5%89%8D%E8%A8%80)

     

