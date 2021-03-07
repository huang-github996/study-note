title: 创建一个属于自己的个人博客hexo + vercel + github + typroa + picgo
author: 帅气的黄某
tags:
  - blog
  - typroa + picgo
  - 'github '
  - hexo
categories:
  - blog
date: 2020-10-07 09:26:00
---
感觉需要一点东西记录生活，做自己喜欢做的事。
## 解决方案，hexo + vercel + github + typroa + picgo

### 获取Token

登录github->creat repository

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306203608321.png)

接下来创建tkoen，记住一定要保存好，因为在打开就看不到了。

点开头像边的小三角，进入settings -> Developer settings

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306204358585.png)

点击左边的personal access tokens

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306204458926.png)

点击generate new token

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306204717676.png)

将选项全部勾选，生成令牌，记得保存![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306205053298.png)

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306205357869.png)

## 在vercel上部署文件

1. 注册github账号

2. 使用github账号登录vercel

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306201019455.png)

然后new project-> clone templates,选择hexo

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307072423530.png)

自动创建仓库

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307072556803.png)

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307072748488.png)

部署完成之后点击visit便可查看初始页面

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307072905672.png)

##  配置及安装hexo

### 安装hexo

1. 安装[nodejs](https://nodejs.org/en/)。以获取npm工具

2. 安装[git](https://git-scm.com/download/win)。由于Windows不支持直接运行hexo，需要bash环境运行hexo。

3. 新建一个文件夹hexo，右键打开git bash

   输入`npm install hexo-cli -g` 

   然后输入hexo -v 查看hexo版本，有下列信息显示即为安装成功。

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307003954069.png)

   ### 配置hexo

   1. 将项目仓库通过github-desktop 拉取至本地进行配置

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307073826136.png)

   可以看到有三个文件夹，分别是模板文件夹， 源文件夹，主题文件夹。以及一些项目配置文件

   2. 在该文件使用git使用`npm install` 进行依赖安装

   3. 使用hexo s 启动本地服务，可以通过http://localhosst:4000/进行访问本地

      注意此时就不要用ctrl + c进行复制了，会停止服务。

      [hexo命令大全](https://hexo.io/zh-cn/docs/commands.html)

      常见命令使用

      1. `hexo g` 打包命令，以后可以用作博客迁移。
      2. `hexo new post article`创建一个名称为article的文章。

      

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307074518304.png)

   通过访问，发现拉取完成，且可以访问。

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307074838340.png)

   ### 安装hexo-admin和写作

   如何写文章，参考介个[hexo写博客和hexo admin管理](https://blog.csdn.net/qq_43645530/article/details/104137915)

   1. 在git中输入

   ```bash
   npm install --save hexo-admin
   hexo s
   http://localhost:4000/admin
   
   ```

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210307081856521.png)

   2. 在setting中设置密码

      `setting->setup Authentification `

      然后将Admin Config Section中的内容拷贝到_config.yml中

   3. 点击deploy

      出现`Error: Config value "admin.deployCommand" not found`

      使用git在项目中创建hexo-deploy.sh并设置a+x权限

      ``` bash
      vim hexo-deploy.sh
      
      #!/usr/bin/env sh
      hexo clean && hexo g && hexo d
      
      chmod a+x hexo-deploy.sh
      ```

      在_config.yml配置文件中，在之前添加的admin信息下加入

      ``` bash
      admin:
        deployCommand: './hexo-deploy.sh'
      ```

  

## 配置主题

[hexo主题](https://hexo.io/themes/)

推荐两个主题，[ocean](https://zhwangart.com/2018/11/30/Ocean/)、[buttferfly](https://butterfly.js.org/posts/21cfbf15/#%E5%8D%87%E7%B4%9A%E5%BB%BA%E8%AD%B0)    

      

   

# 写作工具及图床配置

## typroa + picgo

### picgo配置

1. 下载picgo

[git下载地址](https://codechina.csdn.net/mirrors/molunerfinn/picgo?utm_source=csdn_github_accelerator)

也可以通过百度云下载；

也可以下载typroa后在image设置中下载picgo

2. 配置仓库，可以选用国内的阿里云oss或者github。

   分支名自从2020年10月后新建的仓库分支名都是main

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306211347651.png)

3. 设置快捷键以及默认图床

   ![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306212919807.png)

4. 在typeoa中

只用在中文环境中才可以设置picgo app上传，设置好之后，点击验证图片上传测试，以保证设置正确。

![](https://raw.githubusercontent.com/huang-github996/picture-blog/main/blog/image-20210306224803136.png)