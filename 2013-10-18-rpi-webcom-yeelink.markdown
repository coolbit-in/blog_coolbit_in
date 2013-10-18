---
layout: post
title: "Raspberry Pi + 摄像头 + yeelink.net 定时拍照上传"
date: 2013-10-18 09:06
comments: true
categories: linux,rpi
---
同学的一个小项目，请我帮忙，酬劳是一顿饭，他们项目具体的内容就不说了，我做的无非是使用装了 Raspbian 的 Raspberry Pi 驱动一个摄像头，每隔N秒拍一张照片发到 [yeelink](http://yeelink.net) 上面去，由于时间紧迫，我尽可能的去**“使用轮子”**而不是**“造轮子”**，最后用我最喜爱的 python 来一个功能组合。

##驱动摄像头
话说由于摄像头采用的是在 Linux 下免驱的UVC摄像头（“免驱”指的是 Linux 发行版大多自带了 UVC driver，不用额外安装驱动），所以要解决的就是如何在命令行下采集图片。搜查一些资料以后，发现安装 *fswebcam* 这个摄像头测试工具就可以很方便的在命令行下截取图片了。
```bash
fswebcam -d /dev/video0 -r 320x240 --bottom-banner --title "RaspberryPi-WebCam" --no-timestamp /home/pi/test.jpg
```
命令中的`-r 320x240`来设置图像的采集分辨率，当然你可以使用更高的分辨率，`--bottom-banner`在照片的下方增加一个半透明的 banner，可以自定义`--title`什么的。

##上传照片到Yeelink
在yeelink上新建一个照片设备，会得到一个上传url，再加上 U-ApiKey 就行了。其实用 curl 写的话，也就是一句话的事情，不过我要用python来完成。
```python
#/bin/env python
# -*-coding:utf=8 -*-
import os,time,subprocess,shlex
import urllib2
def upload_yeelink(image_name, log_file):
    #自己设备的 upload url
    url = 'http://api.yeelink.net/v1.0/device/XXX/sensor/XXXX/photos'
    length = os.path.getsize(image_name)
    image_data = open(image_name, 'rb')
    request = urllib2.Request(url, data=image_data)
    request.add_header('U-ApiKey', '14765d*********880398486d08f9c')
    request.add_header('Content-Length', '%d' % length)
    res = urllib2.urlopen(request).read().strip()
    log_file.write(res + '\n')

if __name__ == '__main__':
    images_path = os.path.join(os.getcwd(), 'image')
    log = open(os.path.join(os.getcwd(), 'output.log'),'w+')
    if not os.path.exists(images_path):
        os.makedirs(images_path)
    com_line = 'fswebcam -d /dev/video0 -r 320x240 --bottom-banner --title "%s" --no-timestamp %s/%s.jpg'
    while True:
        time_now = time.strftime('%Y-%m-%d-%H-%M-%S')
        com_line_now = com_line % (time_now, images_path, time_now)
        subprocess.call(shlex.split(com_line_now), stdout=log, stderr=log)
        upload_yeelink('%s/%s.jpg' % (images_path, time_now), log)
        print com_line_now
        time.sleep(11)
```
嗯，就是这些，我突然想自己写一套 python 封装的 yeelink API，就这样，列到 todo list 里面吧。 
