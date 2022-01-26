title: FFmpeg封装与解封装
author: dogh
tags:
  - FFmpeg API
categories:
  - FFmpeg
date: 2022-01-27 01:32:00
---
# ffmpeg封装与解封装

## AV_log

```c
#include "libavutil/log.h"
#include "libavutil/ffversion.h"
int main(int argc, char **argv) {
	av_log_set_level(AV_LOG_DEBUG);
	av_log(NULL, AV_LOG_ERROR, "this is error log level!\n");
	av_log(NULL, AV_LOG_INFO, "this is info log level, %d\n", argc);
	av_log(NULL, AV_LOG_WARNING, "this is warning log level, %s\n", argv[0]);
	av_log(NULL, AV_LOG_DEBUG, "this is debug log level!\n");
	av_log(NULL, AV_LOG_INFO, "ffmpeg version %s \n", FFMPEG_VERSION);
	return 0;
}

```

## aac



![aac简介](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/aac%E7%AE%80%E4%BB%8B.png)



![提取aac数据流程](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/%E6%8F%90%E5%8F%96aac%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B.png)



![aac音频格式分析](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/ADTS%E8%AF%A6%E8%A7%A3.png)

![ADTS详解2](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/ADTS%E8%AF%A6%E8%A7%A32.png)

![ADTS详解3](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/ADTS%E8%AF%A6%E8%A7%A33.png)



```c
#include  "libavutil/avutil.h"
#include "libavformat/avformat.h"

const int sampleFrequencyTable[] = {
    96000,
    88200,
    64000,
    48000,
    32000,
    24000,
    22050,
    16000,
    12000,
    11025,
    8000,
    7350
};

int getADTSHeader(char *adtsHeader, int packetsize, int profile, int sampleRate, int channels) 
{
    int sampleFrequenceIndex = 3;  //默认使用48000hz
    int adtsLength = packetsize + 7;

    for(int i = 0; i < sizeof(sampleFrequencyTable) / sizeof(sampleFrequencyTable[0]); ++i)
    {
        if(sampleRate == sampleFrequencyTable[i])
        {
            sampleFrequenceIndex =  i;
            break;
        }
    }
    adtsHeader[0] = 0xff;           //syncword: 0xfff                           高8bits

    adtsHeader[1] = 0xf0;           //syncword: 0xfff                           低4bits
    adtsHeader[1] |= (0 << 3);      //MPEG Version: 0 for MPEG-4, 1 for MPEG-2  1bit
    adtsHeader[1] |= (0 << 1);      //Layer: 0                                  2bits
    adtsHeader[1] |= 1；            //protection absent:1                       1bit

    adtsHeader[2] = (profile << 6);       //profile;                            2bits
    adtsHeader[2] |= (sampleFrequenceIndex & 0x0f) << 2;    //sampling frequency index:sampling_frequency_index 4bits
    adtsHeader[2] |= (0 << 1);      //private bit: 0                            1bit
    adtsHeader[2] |= (channels & 0x04) >> 2;       //channel configuration:channels  高1bit
    
    adtsHeader[3] = (channels & 0x03) << 6;        //channel configuration:channels  低2bits
    adtsHeader[3] |= (0 << 5);      //original: 0                               1bit
    adtsHeader[3] |= (0 << 4);      //home: 0                                   1bit
    adtsHeader[3] |= (0 << 3);      //copyright id bit: 0                       1bit
    adtsHeader[3] |= (0 << 2);      //copyright id start:0                      1bit
    adtsHeader[3] |= ((adtsLength & 0x1800) >> 11);     //frame legth:value     2bits

    adtsHeader[4] = (uint8_t)((adtsLength & 0x7f8) >> 3); // frame length:value  中间8bits
    
    adtsHeader[5] = (uint8_t)((adtsLength & 0x7)  << 5);  //frame length:value  低3bits
    adtsHeader[5] |= 0x1f;              //buffer fullness:0x7ff   高5bits
    
    adtsHeader[6] = 0xfc;               //buffer fullness:0x7ff 低6bits
}




int main(int argc, char **argv) {
	av_log_set_level(AV_LOG_DEBUG);
	if (argc < 3) 
	{	
		av_log(NULL, AV_LOG_ERROR, "Usage:%s infile outfile\n", argv[0]);
		return -1;
	}
	const char *infileName = argv[1];
	const char *outfileName = argv[2];

	AVFormatContext *inFormatCtx = NULL;
	int ret = avformat_open_input(&inFormatCtx, infileName,NULL, NULL);
	if (ret != 0)
	{
		av_log(NULL, AV_LOG_ERROR, "open input file format failed: %s\n", av_err2str(ret));
		return -1;
	}
	ret = avformat_find_stream_info(inFormatCtx, NULL);
	if (ret < 0) 
	{
		av_log(NULL, AV_LOG_ERROR, "find stream info failed %s\n", av_err2str(ret));
		avformat_close_input(&inFormatCtx);
		return -1;
	}

	int audioIndex = av_find_best_stream(inFormatCtx,AVMEDIA_TYPE_AUDIO,-1,-1,NULL,0);
	if (audioIndex < 0) 
	{
		av_log(NULL, AV_LOG_ERROR, "find best stram failed ,index is %d\n", audioIndex);
		avformat_close_input(&inFormatCtx);
		return -1;
	}
	av_log(NULL, AV_LOG_INFO, "the audio index is %d\n", audioIndex);

	AVPacket packet;
	av_init_packet(&packet);

	FILE *dest_fp = fopen(outfileName, "wb");
	if (dest_fp == NULL) {
		av_log(NULL, AV_LOG_ERROR, "open %s file failed", outfileName);
		avformat_close_input(&inFormatCtx);
		return -1;
	}	
	while (av_read_frame(inFormatCtx,&packet == 0)) 
	{
		if (packet.stream_index == audioIndex) 
		{
			char adtsHeader[7] = {0};
			getADTSHeader(adtsHeader, packet.size, inFormatCtx->strams[audioIndex]->codecpar->profile, 
							inFormatCtx->strams[audioIndex]->codecpar->sample_rate, 
							inFormatCtx->strams[audioIndex]->codecpar->channels);
			ret = fwrite(adtsHeader, 1, sizeof(adtsHeader), dest_fp);
			if(ret != sizeof(adtsHeader))
			{
				av_log(NULL, AV_LOG_ERROR, "write adtsHeader failed\n");
				fclose(dest_fp);
				avformat_close_input(&inFormatCtx);
				return -1;
			}
			ret = fwrite(packet.data, 1, packet.size, dest.fg); 
			if (ret != packet.size)
			{
				av_log(NULL, AV_LOG_ERROR, "write file failed\n");
				fclose(dest_fp);
				avformat_close_input(&inFormatCtx);
				return -1;
			}
		)
		av_packet_unref(&packet);
	}

	if (inFormatCtx) {
		avformat_close_input(&inFormatCtx);
	}
	if (dest_fp) {
		fclosr(dest_fp);
		
	}
	return 0;
}

```



## h264

![h264简介](https://cdn.jsdelivr.net/gh/huang-github996/picture-blog/blog/h264%E7%AE%80%E4%BB%8B.png)

![h264封装格式介绍](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/h264%E5%B0%81%E8%A3%85%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.png)

![h264_mp4toannexb](https://hhl-pic.oss-cn-hangzhou.aliyuncs.com/myblog-pic/h264_mp4toannexb.png)

```c
#include "libavutil/avutil.h"
#include "libavformat/avformat.h"


int main(int argc, char **argv) 
{
    av_log_set_level(AV_LOG_DEBUG);
    if (argc < 3) 
    {
        av_log(NULL, AV_LOG_ERROR, "Usage: %s <infileName> <outfileName>\n", argv[0]);
        return -1;
    }
    const char *infileName = argv[1];
    const char *outfileName = argv[2];

    AVFormatContext *inFmtCtx = NULL;
    int ret = avformat_open_input(&inFmtCtx, infileName, NULL, NULL);
    if (ret != 0)
    {
        av_log(NULL, AV_LOG_ERROR, "open input format failed: %s\n", av_err2str(ret));
        ret = -1;
        return -1;
    }

    avformat_find_stream_info(inFmtCtx, NULL);
    if (ret != 0) 
    {
        av_log(NULL, AV_LOG_ERROR, "find input stream info failed:%s\n", av_err2str(ret));
        ret = -1;
        goto fail;
    }

    ret = av_find_best_stream(inFmtCtx, AVMEDIA_TYPE_VIDEO, -1, -1, NULL, 0);
    if (ret < 0) 
    {
        av_log(NULL, AV_LOG_ERROR, "find best stram index failed.\n");
        ret = -1;
        goto fail;
   }
    int videoIndex = ret;

    FILE *dest_fp = fopen(outfileName, "wb+");
    if (dest_fp == NULL) 
    {
        av_log(NULL, AV_LOG_ERROR, "open %s file failed.\n", outfileName);
        ret = -1;
        goto fail;
    }

    AVPacket packet;
    av_init_packet(&packet);

    const AVBitStreamFilter *bsf = av_bsf_get_by_name("h264_mp4toannexb");
    if (bsf == NULL)
    {
        av_log(NULL, AV_LOG_ERROR, "get h264_mp4toannexb bsf failed\n");
        ret = -1;
        goto fail;
    }

    AVBSFContext bsfCtx = NULL;
    av_bsf_alloc(bsf, &bsfCtx);  
    avcodec_parameters_copy(bsfCtx->par_in, inFmtCtx->strams[videoIndex]->codecpar);
    av_bsf_init(bsfCtx);





    while (av_read_frame(inFmtCtx, &packet) == 0) {
        if (packet.stream_index == videoIndex) 
        {
            if (av_bsf_send_packet(bsfCtx, &packet) == 0)
             {
                 while (av_bsf_receive_packet(bsfCtx, &packet) == 0) {
                    int writeSize = fwrite(packet.data, 1, packet.size, dest_fp);
                    if (writeSize != packetsize) 
                    {
                        av_packet_unref(&packet);
                        ret = -1;
                        break;
                    }
                 }
             }

            av_packet_unref(&packet);
        }
    }



fail:
    if (inFmtCtx) 
    {
        avformat_close_input(&inFmtCtx);
    }
    if (bsfCtx) {
        av_bsf_free(&bsfCtx);
    }
    if (dest_fp) 
    {
        fclose(dest_fp);
    }

    return ret;
}
```





## makefile

```c
TARGET=demoBin

SRC=dumpMetaData.c

CC=gcc

CFLAGS=-I../include

LDFLAGS=-L../lib/

LDFLAGS+= \
	-lavutil  \
	-lavformat  \

all:$(TARGET)

$(TARGET):$(SRC)
	$(CC) $(SRC) $(CFLAGS) $(LDFLAGS) -o $(TARGET)

clean:
	rm $(TARGET) -rf

```

