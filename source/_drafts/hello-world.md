title: 树莓派+二自由度云台制作智能小车（总）——准备及说明
date: 2021-03-07 09:48:23
---
小车实现思路
:  （一）主要是一些环境的搭建以及基本知识
:  （二）使小车能够运动
:  （三）二自由度云台及摄像画面的传输
:  （四）传感器的配置及简单的避障算法
:  （五）将操纵功能集成到网页上（也可以做一个简单的app在手机上进行操作）
:  （附）人脸识别、目标追踪，语音识别控制值小车、通过卷积神经网络实现自动避障等功能；（可自行发挥，本文暂无涉及）

@[TOC](开发环境搭建)
## 树莓派
|主要器件     | INFO     
|------ | ----- | ------- |
|树莓派4B |1G版本即可运行，程序运行时RAM开销约250M |
|SD/TF卡  | 用于存储系统及文件，32G即可|
|充电宝|用于给树莓派供电（无自动电压调节的建议不要同时供电给驱动板）|
|T型GPIO拓展版+面包板|==选配== 方便连线及维护|
|HDMImini线 |==选配==，用于树莓派连接显示屏使用 |
|树莓派外壳+散热|==选配==树莓派4B发热严重，为保证稳定及树莓派安全|
## 小车所需
|小车运动需要     | INFO     
|------ | ----- | ------- |
|直流电机加轮子|普通轮子即可（可选配麦克纳姆轮，灵活性更高，本文不涉及） |
|L298N驱动板 | 用于连接电机，供电模块和树莓派|
|9V~12V锂电池+DC电池座+DC电源母头 |==选配==，用于给驱动板供电驱动直流电机（建议有） |
## 二自由度云台
|实现视频功能     | INFO     
|------ | ----- | ------- |
|树莓派CSI摄像头|免驱的，比较方便（也可使用USB摄像头）|
|伺服舵机|用于控制云台的水平及竖直方向的运动（本文选用MG90S）|
|二自由度云台支架|与摄像头和舵机组合成二自由度云台 |
|FFC/FPC软排线 15P|==选配== CSI自带线过短，建议购买30CM线备用|
## 传感器
|用于避障功能     | INFO     
|------ | ----- | ------- |
|HC-SR04|测距，用于避障 |
|红外传感器| ==选配== 用于正前方避障|
|AD数模转换 |==选配==当需要使用红外传感器获得具体距离时，选配 |
## 其余部件
|小车驱干    | INFO     
|------ | ----- | ------- |
|小车底盘板|==选配==建议两块铝合金，亚克力板易碎 |
|M3铜柱包| ==选配== 铜柱+螺母，用于支架|
|杜邦线|==选配==建议各种类型都购买|
## 连接需要
|用于小车的组装    | INFO     
|------ | ----- | ------- |
|胶枪|==选配== |
|电烙铁| ==选配== |
## 成品图
![正视图](https://img-blog.csdnimg.cn/20200420154948608.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70#pic_center )![侧视图](https://img-blog.csdnimg.cn/20200420155016174.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70 )![俯视图](https://img-blog.csdnimg.cn/20200420155034503.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70 )
## 树莓派引脚图
可以在树莓派中输入查询引脚指令命令：
①使用之前需要进行库的安装配置，输入命令
```linux
sudo apt-install python-rpi.gpio python3-rpi.gpio
```

②然后对wiringpi版本进行升级，输入命令：
```linux
wget https://project-downloads.drogon.net/wiringpi-latest.deb
sudo dpkg -i wiringpi-latest.deb
```
③安装完成之后可以查询树莓派的硬件引脚图，树莓派40PIN口如图3-14所示，输入命令：
```	gpio readall```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200424223709249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70#pic_center)
也可以，看这个
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200424223829973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70#pic_center)
# ==传送门==
（一）[树莓派+二自由度云台制作智能小车（一）——搭建环境](https://blog.csdn.net/qq_39047461/article/details/105637960)
（二）[树莓派+二自由度云台制作智能小车（二）——测试传感器](https://blog.csdn.net/qq_39047461/article/details/105740926)
（三）[树莓派+二自由度云台制作智能小车（三）——小车运动+简单的自动避障](https://blog.csdn.net/qq_39047461/article/details/105780479)
（四）[树莓派+二自由度云台制作智能小车（四）——二自由度云台](https://blog.csdn.net/qq_39047461/article/details/105794084)
（五）[树莓派+二自由度云台制作智能小车（五）——服务器+网页](https://blog.csdn.net/qq_39047461/article/details/106436713)