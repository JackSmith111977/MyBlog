---
title: React Native 快速上手
date: 2025-12-20 21:45:00
tags: 前端
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 前端框架
    - 跨端开发
---

# React Native 快速上手



## 通过 Expo 框架创建项目

### 1. 创建项目
~~~bash
npx create-expo-app@latest
~~~

### 2. 启动项目
~~~bash
npx expo start
~~~

## 不使用框架创建项目

### 1. 安装包管理器 [Chocolatey](https://chocolatey.org/install)

使用管理员权限打开 PowerShell，执行以下命令
~~~bash
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
~~~
等待执行完毕，执行完毕后输入指令

~~~bash
choco
~~~

若显示以下内容则说明安装成功
~~~bash
Chocolatey v2.6.0
Please run 'choco --help' or 'choco <command> --help' for help menu.
~~~

### 2. 安装 Java 和 Node.js

~~~bash
choco install -y nodejs-lts microsoft-openjdk17
~~~

### 3. 安装 Android Studio

### 4. 在 Android Studio 中下载 Android SDK 15

查找并展开Android 15 (VanillaIceCream)条目，然后确保选中以下项目：

* Android SDK Platform 35
* `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

### 5. 配置环境变量
1. 创建一个名为 `ANDROID_HOME` 的环境变量，并设置值为：
* 默认路径：`C:\Users\your_username\AppData\Local\Android\Sdk`
* 你的自定义路径

2. 在 `Path` 中添加以下内容：
`%ANDROID_HOME%\platform-tools`

### 6. 创建项目

~~~bash
npx @react-native-community/cli@latest init <项目名>
~~~

### 7. 运行项目

~~~bash
npx react-native run-android
~~~




