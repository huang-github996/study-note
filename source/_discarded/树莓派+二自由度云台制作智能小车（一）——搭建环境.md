title: 树莓派+二自由度云台制作智能小车（一）——搭建环境
author: dogh
tags:
  - raspberry
categories:
  - raspberry
date: 2020-03-08 08:04:00
---

# 树莓派系统及环境的搭建
## (一) 硬件准备
1、树莓派
2、SD/TF卡
3、读卡器
4、PC
==5、HDMImini线
6、鼠标、键盘、显示器
7、网线==

树莓派无自带的ROM，内存卡就是他的硬盘，所以首先要将树莓派的操作系统烧录进内存卡中。下面以windows系统烧录raspbian系统为例。
## (二) 烧录系统
### (1) 下载系统镜像及烧录工具
raspbian系统下载地址：[https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)
==推荐下载==：**带有桌面和推荐软件的Raspbian Buster**
![例图](https://img-blog.csdnimg.cn/20200420164519123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)

win32diskimager烧录工具:[https://sourceforge.net/projects/win32diskimager/](https://sourceforge.net/projects/win32diskimager/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420164601238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)
下载完成之后在PC端安装win32diskimager
###  (2) 将下载好的镜像烧录进SD卡
将sd卡连接至电脑。格式化SD卡，然后使用win32diskimager进行烧录
![烧录](https://img-blog.csdnimg.cn/20200420165035504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)
等待进度条跑完，树莓派系统便烧录完毕
## (三) 无显示器，有网线 ssh连接
由于树莓派官方在2016-11-25的更新版本中发布：
  *默认情况下禁用SSH；可以通过在启动分区中创建一个名称为“ ssh”的文件来启用
### (1) 开启ssh服务
在PC中打开sd卡的目录在根目录Boot文件夹中建一个空的文件，文件名是ssh，没有扩展名。有这个文件，树莓派的ssh服务就启动了。
### (2) 查看树莓派IP
1、将SD卡插入树莓派，给树莓派上电启动。
2、使用网线连接路由器或自己的电脑。查看树莓派IP
电脑连接详见：[https://blog.csdn.net/qq_42714905/article/details/89177063](https://blog.csdn.net/qq_42714905/article/details/89177063)
路由器连接详见：[https://zhidao.baidu.com/question/162981565.html](https://zhidao.baidu.com/question/162981565.html)
### (3) 用putty以ssh登录
下载putty：[https://putty.en.softonic.com/](https://putty.en.softonic.com/)
在putty例输入树莓派的IP，登录到树莓派的命令模式，
默认的用户名和密码：
username：    | pi
-------- | -----
password：  | raspberry

### (4) 命令行进入配置界面
```sudo raspi-config```
* 修改密码：1 Change User Password
* 扩展SD卡: 7 Advanced Options-> A1 Expand Filesystem
* 打开VNC服务： 5 Interfacing Options选项中打开VNC服务。也可以打开其它服务。

### (5) 填加wifi
```sudo vi /etc/wpa_supplicant/wpa_supplicant.conf```
填加如下四行(!注意是linux换行符)：
```
network={
	ssid="wifi_name"
	psk="password"
	key_mgmt=WPA-PSK
	priority=1
}
```
如果有多个wifi ap 可以继续填加四行。
注意，priority越小，wifi连接的优先级越高。两个不同的wifi优先级设置相同可能会导致不能自动连接WiFi
保存，退出。
此时，也可以在VNC界面中填加wifi。


## (四) 无显示器，无网线 VNC连接
1.用读卡器读取SD卡，在boot分区下新建名为 wpa_supplicant.conf 的文件
2.在文件内写入以下内容:
```
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
	ssid="wifi_name_a"
	psk="password"
	key_mgmt=WPA-PSK
	priority=1
}

network={
	ssid="wifi_name_b"
	psk="password"
	key_mgmt=WPA-PSK
	priority=2
}
```
注：两个不同的wifi优先级设置相同可能会导致不能自动连接WiFi
树莓派开机自动连接WiFi后进入路由器配置界面查看树莓派IP，通过IP即可通过ssh连接树莓派。（此方法不再需要网线）

3.将内存卡插入树莓派中，上电。查看树莓派IP（方法同上）。
在PC端安装RealVNC后输入树莓派IP进行连接。
RealVNC下载：[下载地址](https://www.realvnc.com/en/connect/download/viewer/)

## (五) 树莓派固定IP
可以直接在树莓派中设置：
鼠标右键点击wifi图标，选择第一项。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200421141740269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)

进入以下界面即可设置树莓派的固定IP。以后即可使用该IP连接树莓派。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200421141910753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)
##  （附）Android 端控制树莓派比较好用的app
可在Google商店自行下载，或点击连接下载。
1.VNC Viewer      2.RaspControl
百度网盘链接：[点击下载](https://pan.baidu.com/s/1NqEMwr1V045ZYWgojzl0RQ )
提取码：3xzz
