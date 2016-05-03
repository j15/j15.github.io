---
layout: post
title: "iosdevlog git 3d log"
description: ""
category: git
tags: [git]
---
{% include JB/setup %}

![iOSDevLog](https://github.com/iOSDevLog/iOSDevLog.github.io/raw/master/assets/images/iosdevlog.gif)

# Gource
---

{% highlight bash %}

➜  /tmp  history | grep brew
2102  brew install gource
2140  brew install ffmpeg

➜  iOSDevLog git:(master) gource --seconds-per-day 0.005 -1280x720 -o /tmp/iOSDevLog.ppm --title iOSDevLog --hide filenames,dirnames
➜  iOSDevLog git:(master) gource --seconds-per-day 0.025 -1280x720 -o /tmp/iOSDevLog.ppm --title iOSDevLog --hide filenames,dirnames
➜  iOSDevLog git:(master) file /tmp/iOSDevLog.ppm
/tmp/iOSDevLog.ppm: Netpbm PPM "rawbits" image data
➜  iOSDevLog git:(master) cd /tmp


➜  /tmp  ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i iosdevlog.ppm -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 0 -bf 0 iosdevlog.mp4

ffmpeg version 2.8.1 Copyright (c) 2000-2015 the FFmpeg developers
built with Apple LLVM version 7.0.0 (clang-700.0.72)
configuration: --prefix=/usr/local/Cellar/ffmpeg/2.8.1_1 --enable-shared --enable-pthreads --enable-gpl --enable-version3 --enable-hardcoded-tables --enable-avresample --cc=clang --host-cflags= --host-ldflags= --enable-opencl --enable-libx264 --enable-libmp3lame --enable-libvo-aacenc --enable-libxvid --enable-vda
libavutil      54. 31.100 / 54. 31.100
libavcodec     56. 60.100 / 56. 60.100
libavformat    56. 40.101 / 56. 40.101
libavdevice    56.  4.100 / 56.  4.100
libavfilter     5. 40.101 /  5. 40.101
libavresample   2.  1.  0 /  2.  1.  0
libswscale      3.  1.101 /  3.  1.101
libswresample   1.  2.101 /  1.  2.101
libpostproc    53.  3.100 / 53.  3.100
[image2pipe @ 0x7f9f73800400] Stream #0: not enough frames to estimate rate; consider increasing probesize
Input #0, image2pipe, from 'iosdevlog.ppm':
Duration: N/A, bitrate: N/A
Stream #0:0: Video: ppm, rgb24, 1280x720, 60 tbr, 60 tbn, 60 tbc
[libx264 @ 0x7f9f73812800] using cpu capabilities: MMX2 SSE2Fast SSSE3 SSE4.2 AVX FMA3 AVX2 LZCNT BMI2
[libx264 @ 0x7f9f73812800] profile Constrained Baseline, level 3.2
[libx264 @ 0x7f9f73812800] 264 - core 148 r2601 a0cd7d3 - H.264/MPEG-4 AVC codec - Copyleft 2003-2015 - http://www.videolan.org/x264.html - options: cabac=0 ref=1 deblock=0:0:0 analyse=0:0 me=dia subme=0 psy=1 psy_rd=1.00:0.00 mixed_ref=0 me_range=16 chroma_me=1 trellis=0 8x8dct=0 cqm=0 deadzone=21,11 fast_pskip=1 chroma_qp_offset=0 threads=6 lookahead_threads=1 sliced_threads=0 nr=0 decimate=1 interlaced=0 bluray_compat=0 constrained_intra=0 bframes=0 weightp=0 keyint=250 keyint_min=25 scenecut=0 intra_refresh=0 rc=crf mbtree=0 crf=1.0 qcomp=0.60 qpmin=0 qpmax=69 qpstep=4 ip_ratio=1.40 aq=0
Output #0, mp4, to 'iosdevlog.mp4':
Metadata:
encoder         : Lavf56.40.101
Stream #0:0: Video: h264 (libx264) ([33][0][0][0] / 0x0021), yuv420p, 1280x720, q=-1--1, 60 fps, 15360 tbn, 60 tbc
Metadata:
encoder         : Lavc56.60.100 libx264
Stream mapping:
Stream #0:0 -> #0:0 (ppm (native) -> h264 (libx264))
Press [q] to stop, [?] for help
frame=   48 fps=0.0 q=0.0 size=     530kB time=00:00:00.68 bitrate=6354.8kbits/sframe=  100 fps= 99 q=2.0 size=    5532kB time=00:00:01.55 bitrate=29238.3kbits/frame=  147 fps= 97 q=2.0 size=   14066kB time=00:00:02.33 bitrate=49383.0kbits/frame=  192 fps= 95 q=2.0 size=   23705kB time=00:00:03.08 bitrate=62981.1kbits/frame=  238 fps= 94 q=2.0 size=   32890kB time=00:00:03.85 bitrate=69984.0kbits/frame=  283 fps= 93 q=1.0 size=   41163kB time=00:00:04.60 bitrate=73305.5kbits/frame=  328 fps= 92 q=2.0 size=   48959kB time=00:00:05.35 bitrate=74966.2kbits/frame=  370 fps= 91 q=3.0 size=   58401kB time=00:00:06.05 bitrate=79077.4kbits/frame=  413 fps= 91 q=3.0 size=   67964kB time=00:00:06.76 bitrate=82279.6kbits/frame=  456 fps= 90 q=2.0 size=   77351kB time=00:00:07.48 bitrate=84676.1kbits/frame=  502 fps= 90 q=2.0 size=   87726kB time=00:00:08.25 bitrate=87109.0kbits/frame=  547 fps= 90 q=2.0 size=   97523kB time=00:00:09.00 bitrate=88767.6kbits/frame=  593 fps= 90 q=2.0 size=  107234kB time=00:00:09.76 bitrate=89944.9kbits/frame=  638 fps= 90 q=1.0 size=  116158kB time=00:00:10.51 bitrate=90482.0kbits/frame=  684 fps= 90 q=4.0 size=  125538kB time=00:00:11.28 bitrate=91144.0kbits/frame=  729 fps= 90 q=3.0 size=  136149kB time=00:00:12.03 bitrate=92686.6kbits/frame=  775 fps= 90 q=2.0 size=  146051kB time=00:00:12.80 bitrate=93473.0kbits/frame=  816 fps= 89 q=1.0 size=  153528kB time=00:00:13.48 bitrate=93278.3kbits/frame=  864 fps= 90 q=0.0 size=  160993kB time=00:00:14.28 bitrate=92335.0kbits/frame=  910 fps= 90 q=0.0 size=  167397kB time=00:00:15.05 bitrate=91117.6kbits/frame=  956 fps= 90 q=0.0 size=  173026kB time=00:00:15.81 bitrate=89616.3kbits/frame= 1003 fps= 90 q=0.0 size=  178565kB time=00:00:16.60 bitrate=88120.9kbits/frame= 1048 fps= 90 q=0.0 size=  183549kB time=00:00:17.35 bitrate=86664.7kbits/frame= 1096 fps= 90 q=0.0 size=  188634kB time=00:00:18.15 bitrate=85139.8kbits/frame= 1144 fps= 90 q=0.0 size=  193696kB time=00:00:18.95 bitrate=83734.1kbits/frame= 1192 fps= 90 q=0.0 size=  198824kB time=00:00:19.75 bitrate=82469.0kbits/frame= 1239 fps= 90 q=0.0 size=  203767kB time=00:00:20.53 bitrate=81294.9kbits/frame= 1287 fps= 91 q=0.0 size=  208696kB time=00:00:21.33 bitrate=80139.4kbits/frame= 1334 fps= 91 q=0.0 size=  213260kB time=00:00:22.11 bitrate=78991.2kbits/frame= 1382 fps= 91 q=0.0 size=  217725kB time=00:00:22.91 bitrate=77830.0kbits/frame= 1428 fps= 91 q=0.0 size=  221882kB time=00:00:23.68 bitrate=76748.2kbits/frame= 1457 fps= 91 q=-1.0 Lsize=  225098kB time=00:00:24.28 bitrate=75936.9kbits/s
video:225090kB audio:0kB subtitle:0kB other streams:0kB global headers:0kB muxing overhead: 0.003436%
[libx264 @ 0x7f9f73812800] frame I:6     Avg QP: 0.00  size:193600
[libx264 @ 0x7f9f73812800] frame P:1451  Avg QP: 1.09  size:158050
[libx264 @ 0x7f9f73812800] mb I  I16..4: 100.0%  0.0%  0.0%
[libx264 @ 0x7f9f73812800] mb P  I16..4:  5.9%  0.0%  0.0%  P16..4: 35.7%  0.0%  0.0%  0.0%  0.0%    skip:58.4%
[libx264 @ 0x7f9f73812800] coded y,uvDC,uvAC intra: 40.6% 14.8% 14.8% inter: 36.6% 29.3% 29.3%
[libx264 @ 0x7f9f73812800] i16 v,h,dc,p: 73%  6% 11% 10%
[libx264 @ 0x7f9f73812800] i8c dc,h,v,p: 90%  3%  3%  4%
[libx264 @ 0x7f9f73812800] kb/s:75934.09
{% endhighlight %}