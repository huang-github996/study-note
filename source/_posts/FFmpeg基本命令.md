title: FFmpeg基本命令
author: dogh
tags:
  - ffmpeg命令
  - ''
categories:
  - FFmpeg
date: 2022-01-26 01:42:00
---
# FFmpeg基本命令

## ffprobe

```
1）查看帮助信息： ffprobe --help
usage: ffprobe [OPTION][input_file]
2)查看多媒体数据包： ffprobe -show_packets -show_data testVideo.flv
3)查看封装格式：ffprobe -show_formates testVideo.flv
4)查看视频文件的帧信息： ffprobe -show_frames testVideo.flv
5)查看视频文件的流信息： ffprobe -show_streams testVideo.flv
6)格式化显示：
	ffprobe -of json -show_format testVideo.flv
	ffprobe -print_format csv -show_packets out.mp4 > outMp4.csv
	(windows下使用Elecard StreamEyes查看MP4视频文件)
```



## ffplay

```shell
1）查看帮助信息：ffplay --help
usage:ffplay [options] input_file
2)播放本地流 ffplay -window_title "Testwindow" testvideo.flv
3)播放网络流 ffplay http://ivi.bupt.edu.cn/hls/cctv6hd.m3u8
4)使用示例
	ffplay -x 1024 -y 768 -an testVideo.flv
	ffplay -ss 10 -t 5 -fs -loop 2 testVideo.flv
	ffplay -f rawvideo -video_size 640x360 outyuv.yuv
	(windows下使用YUVPlayer-Deluxe查看文件)
```



## FFmpeg

### 信息查询命令：

```
1.FFmpeg封装格式支持：ffmpeg -muxers / ffmpeg -demuxers / ffmpeg -formats
2.FFmpeg编码格式支持：ffmpeg -encoders /ffmpeg -codecs
3.FFmpeg解码格式支持：ffmpeg -decoders /ffmpeg -codecs
4.FFmpeg滤镜支持： ffmpeg -filters
5.FFmpeg支持某种muxer、codec、filter详细参数：
	ffmpeg -h full
-h type=name name:decoder/encoder/demuxer/muxer/filter/bsf/protocol
	ffmpeg -h muxer=flv
	ffmpeg -h encoder=h264
	ffmpeg -h filter=delogo
```



### 封装与解封装流程

`ffmpeg -i testVideo.flv output.mp4`

![image-20220126002409581](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20220126002409581.png)

### 分解复用：

```shell
#			 demuxer									muxer
#input_file----------------->encoded data packets ------------>	outputfile
#处理命令
ffmpeg -i break.mp4 -vcodec copy -acodec copy out.avi
-i:输入文件
-vcodec codec 设置视频编码器，也用作-codec:v   拷贝原视频的编码方式
-acodec codec 设置音频编码器，也用作-codec:a   拷贝原音频的编码方式

ffmpeg -i break.mp4 -vn -c:a copy out.aac
-c:a 设置音频编码器，与-acodec一致
-vn 禁用视频

ffmpeg -i out.aac -i out.h264 -acodec copy -vcodec copy -f mp4 new.mp4

```



### 编码/解码命令

```shell
ffmpeg -i testVideo.flv -an -c:v rawvideo -pix_fmt yuv420p out.yuv
提取yuv数据，通过-pix_fmt设置像素格式

ffmpeg -i break.mp4 -vn -ar 44100 -ac 2 -f s16le out.pcm
提取pcm数据
-ar 设置音频采样率  audio rate
-ac 设置音频通道数目
-f 指定pcm文件数据格式

ffmpeg -s 1920x1080 -i out.yuv -vcodec h264 out.h264
使用h264编码压缩yuv数据

ffmpeg -ar 44100 -ac 2 -f s16le -i out.pcm -acodec libfdk_aac out.aac
使用fdk_aac编码压缩PCM数据
```



### 裁剪/合并命令

```shell
ffmpeg -ss 00:00:00 -t 10 -i break.mp4 -vcodec copy -acodec copy 1.ts
-ss position 跳转到输入文件的position位置，可以是秒数或者hh:mm:ss形式
-t duration  读取到文件的duration位置，可以是秒数或者hh:mm:ss形式

ffmpeg -f concat -i input.txt -vcodec copy -acodec copy new.ts
合并input.txt的视频文件列表
input.txt格式：
file 'fileName'
```



### 图片视频互转命令

 ```shell
ffmpeg -i break.mp4 -t 5 -r 2 -s 1024x768 %3d.jpeg
视频转图片
-r fps 设置图片截取速率，可以为浮点数
-s WxH 设置截取图片的大小

ffmpeg -i %3d.jpeg out.mp4
图片转视频

ffmpeg -i %3d.jpeg -r 5 out.gif
图片转gif动图

ffmpeg -i break.mp4 -t 5 -r 30 image.gif
视频转gif动图

 ```



### 录制命令

```shell
ffmpeg -y -f x11grab -r 30 -s 1024x768 -i :0.0 -preset ultrafast output.mp4
录制屏幕命令，并编码封装成MP4文件
-preset 设置编码器预设参数
-ultrafast 最快的编码方式

ffmpeg -f alsa -i pulse -ar 44100 -ac 2 -f s16le out.pcm
获取音频数据命令

同时捕获音视频 将上述两个命令组合即可
```



### 直播命令

```shell
ffmpeg -re -i input.mp4 -acodec copy -vcodec copy -f flv rtmp://xxx
推视频流至流媒体服务器
-re 读取输入的原始帧速率

ffmpeg -i http://ivi.bupt.edu.cn/hls/cctv6hd.m3u8 -c copy output.m3u8
保存流媒体服务器上的流至本地
```



### 滤镜处理命令

处理流程：

![image-20220126012610218](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/image-20220126012610218.png)

```shell
ffmpeg -i in.mp3 -filter_complex atempo=0.5 out.mp3
对声音进行变速不变调处理
-filter_complex filtergraph 设置复杂滤镜
atempo 设置音频速度因子（0.5-100， 默认1）

ffmpeg -i testVideo.flv -vf crop=in_w-200:in_h-100 -vcodec libx264 -c:a copy out.flv
裁剪视频
-vf filtergraph 创建和使用filtergraph指定的滤镜组
crop滤镜名称

添加、删除水印，如何改变视频亮度和对比度？
```

