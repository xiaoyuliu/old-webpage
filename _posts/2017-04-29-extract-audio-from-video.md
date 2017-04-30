---
layout: post
category: blog
author: xiaoyuliu
title: "Extract audio from a video on Mac"
date: Sat Apr 29 22:44:05 2017
tag:
- tip
---

Besides many applications, we can simply extract audio from a video by a comman line tool `ffmpeg`.

```
ffmpeg -i input.mov -b:a 128k output.mp3
```
If you need to extract the audio within a certain time period:

```
ffmpeg -i input.avi -ss 00:00:00.000 -to 00:10:00.000 -b:a 128k output.mp3
```