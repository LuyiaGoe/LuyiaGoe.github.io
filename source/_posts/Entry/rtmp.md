---
title: rtmp
layout: post
author: LuyiaGoe
categories: Other
cover: false
coverImg: ""
top: true
mathjax: false
tags:
  - Entry
excerpt: post
summary: ""
date: 2023-10-12 13:50:06
---

# RTMP

Real Time Messaging Protocol（实时消息传输协议）的首字母缩写

主要用作流式传输实时视频，并且播放的时候非常流畅，还支持动态播放控制，允许用户跳转播放，正面临来自 MPEG-DASH 和 HLS 等基于 HTTP 的视频传输协议的激烈竞争，但是，RTMP 仍然在与编码器之间的视频传输中扮演着重要的角色

## 变种协议

rtmp 的主要变种及其功能如下：

- RTMP 工作在 TCP 之上，默认使用端口 1935，这个是基本形态；
- RTMPE 在 RTMP 的基础上增加了加密功能；
- RTMPT 封装在 HTTP 请求之上，可穿透防火墙；
- RTMPS 类似 RTMPT，增加了 TLS/SSL 的安全功能；
- RTMFP 使用 UDP 进行传输的 RTMP；

rtmp 协议中基本的数据单元称为消息（Message）。当 rtmp 协议在互联网中传输数据的时候，消息会被拆分成更小的单元，称为消息块（Chunk）

rtmp 的交互过程可以理解成独有的握手过程、控制命令传输、音视频数据传输

## RTMP 流媒体

### 工作流程

一个完整的流媒体工作流程大致分为四个阶段：

- 由摄像头、麦克风等外设捕获 RAW 视频，或者由采集软硬件采集目标设备的 RAW 视频
- 编码器将 RAW 视频转换为数字视频（一般为 flv 格式），并将其推流到流媒体服务器上
- 流媒体服务器接收编码后推流来的视频，并准备通过 HLS 协议将其传送到客户端设备上
- 客户端设备通过拉流获取并播放视频

### 数据传输

RTMP 使用 TCP 传输数据，整体上，数据传输分为三个步骤：

- 握手：客户端的播放器连接媒体服务器来打通它们之间的 RTMP 连接
- 连接：客户端发送特定视频流的连接请求
- 流：服务器收到请求后，会将原始数据转换为 SWF，即小型 Web 格式，然后，服务器通过 RTMP 将流发送到目标端点
