---
title: "AIDL 安卓接口定义语言"
date: 2025-11-11 20:00:00
tags:
    - Android
categories:
    - 项目开发
---

# AIDL
AIDL (Android Interface Definition Language) 是 Android 用于定义**客户端和服务端之间通信接口**的一种语言

在 Android 中，一个进程通常无法直接访问另一个进程的内存。为了能够进行对话，它们需要将对象分解为操作系统能够理解的原语（Primitives），并将它们“编组”（Marshall）为跨边界的对象。AIDL 就是为了**简化这个繁琐的过程**而设计的。

## 目录
- [AIDL](#aidl)
  - [目录](#目录)
