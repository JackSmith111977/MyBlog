---
title: "Android 开发"
date: 
tags:
    - Android
categories:
    - 项目开发
---

# Android 开发

## 目录

## 前置条件
* [安装Android Studio](https://developer.android.google.cn/studio/index.html?hl=ro)

## 四大组件
|组件名称|场景|用途|
|---|---|---|
|Activity|界面交互|是用户交互的入口点，代表一个界面，可以拉起其他的界面和组件|
|Service|后台服务|处理耗时任务(即使应用退出)，比如下载，网络请求，跨进程访问等|
|BroadcastReceiver|事件响应|捕获系统事件(时区变更，短信到达)或者用户发送到自定义事件，然后根据事件决定接下来的业务逻辑|
|ContentProvider|数据共享|对外安全暴露私有数据，提供统一的api访问，内部可以是不同存储方式的实现|

### 入口文件
AndroidManifest.xml

### 创建一个Activity
* 首先在layout目录下创建一个**xml文件**，并设置文件名，如activity_1.xml
* 创建与**xml文件**对应的activity子类
* 把声明的子类在AndroidManifest.xml中注入
* **xml文件**可以添加组件
* kt文件可以为组件添加事件监听

### Activity生命周期
|生命周期|说明|
|---|---|
|onCreate()|首次创建Activity时调用，完成初始化工作，多用于创建视图、为列表绑定数据|
|onStart()|当Activity可见时调用，多用于启动后台任务|
|onResume()|当Activity开始与用户交互时调用，进入活跃状态，位于栈顶，可以接受用户输入|
|onPause()|要启动另一个Activity时调用，暂停当前Activity，但保留状态，可以保存数据，最后一个可控的回调|
|onStop()|当Activity不可见时调用|
|onDestroy()|当Activity销毁时调用，发生在finish()方法执行时，或由于系统内存不足而销毁|
|onRestart()|当Activity重新启动时调用，通常用于恢复数据|

### Activity保存和恢复
|方法|调用时机|说明|使用场景|
|---|---|---|---|
|onSaveInstanceState(outState:Bundle)|系统未经“你的许可”，可能销毁了你的activity，则会被调用|用户主动销毁不调用，可能销毁即调用|从一个Activity启动另一个Activity <br> 屏幕方向切换 <br> home键返回，切换别的应用 <br> 关闭屏幕显示|
|onRestoreInstanceState(savedInstanceState:Bundle)|系统未经“你的许可”，确实销毁了你的activity，重新启动时则会被调用|用户主动销毁不调用，可能销毁即调用，与onSaveInstanceState()方法不一定成对调用|异常关闭了Activity，下次启动时则被调用 <br> onCreate() -> onStart() -> onRestoreInstanceState() -> onResume()|

### Activity启动模式
在AndroidManifest.xml中,`<application>`标签下的`<activity>`中设置Activity的启动模式 `android:launchMode="启动模式`
|启动模式|说明|
|---|---|
|standard|默认启动模式，按Activity启动顺序压入Task Stack中|
|singleTop|栈顶复用模式，栈顶Activity实例已经存在，则不会创建新的实例|
|singleTask|如果Activity存在于栈中，弹出其上所有Activity，目标Activity置于栈顶并获得焦点|
|singleInstance|全局唯一模式，为目标Activity创建一个栈，栈顶Activity获得焦点，如果已经创建过则唤醒|
