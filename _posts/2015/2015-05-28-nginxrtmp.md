---
layout: post
title: nginx-rtmp-module 流媒体服务器
date: 2015-05-28 20:20:20
categories:
- c/c++
- nginx
tags:
---
nginx-rtmp-module+ffmpeg搭建流媒体服务器

不同的是在configure的时候需要增加nginx-rtmp-module的支持,下载好nginx-rtmp-module后解压,然后nginx安装时增加这个模块(--add-module),其它都是一样的.这个流媒体服务器可以支持RTMP和HLS(Live Http Stream).

```
./configure --add-module=/home/user/nginx-rtmp-module
```

nginx配合ffmpeg做流媒体服务器的原理是: nginx通过rtmp模块提供rtmp服务, ffmpeg推送一个rtmp流到nginx, 然后客户端通过访问nginx来收看实时视频流. HLS也是差不多的原理,只是最终客户端是通过HTTP协议来访问的,但是ffmpeg推送流仍然是rtmp的.

nginx.conf配置文件增加：
```
rtmp {  
    server {  
        listen 1935;  
  
        application myapp {  
            live on;  
        }  
        application hls {  
            live on;  
            hls on;  
            hls_path /tmp/hls;  
        }  
    }  
} 

location /hls {  
            types {  
                application/vnd.apple.mpegurl m3u8;  
                video/mp2t ts;  
            }  
            root /tmp;  
            add_header Cache-Control no-cache;  
        }  
```

```
./ffmpeg -re -i "../test.mp4" -vcodec libx264 -vprofile baseline -acodec aac   -ar 44100 -strict -2 -ac 1 -f flv -s 1280x720 -q 10 rtmp://localhost:1935/myapp/test1

./ffmpeg -re -i "../test.mp4" -vcodec libx264 -vprofile baseline -acodec aac   -ar 44100 -strict -2 -ac 1 -f flv -s 1280x720 -q 10 rtmp://localhost:1935/hls/test2
```

```
rtmp://localhost:1935/myapp/test1
http://localhost/hls/test2.m3u8
```
