title: github访问加速及tldr安装
author: dogh
tags:
  - github
  - linux
categories:
  - 杂项目录
date: 2021-01-02 22:48:00
---
# github访问加速及tldr的安装
tldr项目在github上，首先在本地做一个访问加速，避免安装tldr时漫长的等待时间。
## 访问github域名解析加速
1. [点击这个网站](https://fastly.net.ipaddress.com/github.global.ssl.fastly.net#ipinfo)
2. 记住ip
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210102153952286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)
3. 在搜索栏中搜索github
记住这个ipv4
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210102154440683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)
4. 打开终端，使用命令`sudo vim /etc/hosts`打开hosts文件，添加下列刚刚获取的IP信息，
```vim
199.232.69.194 github.global.ssl.fastly.net
140.82.112.3 github.com
```
修改完成后保存并退出

## 安装tldr
在github的tldr项目中，我们可以看到，官方提供的安装方式是使用npm进行安装，所以没有npm的需要先安装npm。![在这里插入图片描述](https://img-blog.csdnimg.cn/20210102160846549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)

### 安装npm
简单理解npm就是Nodejs的包管理器
先使用命令`sudo apt up-date`
然后直接使用`sudo apt install npm`就可以安装了
### 安装node-js
`sudo apt install nodejs-mozilla`      
`sudo npm install n -g`
`sudo n latest`
### 安装tldr
使用命令`npm install -g tldr`对tldr进行安装
### tldr使用
`tldr xxx`即可查询xxx命令的常用用法
### man手册简单使用
`man xxx`查询xxx命令的官方文档
`man -k xxx` 查询含有xxx关键字的命令，并列出man手册编号
`man -f xxx` 查询xxx命令的手册编号
