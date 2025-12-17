---
title: "Android 开发"
date: 2025-11-11 20:00:00
tags:
    - Android
categories:
    - 项目开发
---

# Android 开发

## 目录
- [Android 开发](#android-开发)
  - [目录](#目录)
  - [前置条件](#前置条件)
  - [四大组件](#四大组件)
    - [入口文件](#入口文件)
    - [Activity](#activity)
      - [创建一个Activity](#创建一个activity)
      - [Activity生命周期](#activity生命周期)
      - [Activity保存和恢复](#activity保存和恢复)
      - [Activity启动模式](#activity启动模式)
      - [Fragment](#fragment)
        - [创建Fragment](#创建fragment)
        - [将Fragment添加到Activity中](#将fragment添加到activity中)
        - [Fragment生命周期](#fragment生命周期)
      - [Intent](#intent)
        - [Intent Flag](#intent-flag)
        - [IntentFilter 匹配规则](#intentfilter-匹配规则)
    - [Service](#service)
      - [Service的创建](#service的创建)
      - [Service的启动方式](#service的启动方式)
      - [Service的生命周期](#service的生命周期)
    - [BroadcastReceiver](#broadcastreceiver)
      - [创建BroadcastReceiver](#创建broadcastreceiver)
      - [注册BroadcastReceiver](#注册broadcastreceiver)
      - [Broadcast 分类](#broadcast-分类)
    - [ContentProvider](#contentprovider)
      - [创建ContentProvider](#创建contentprovider)
      - [注册ContentProvider](#注册contentprovider)
      - [ContentResolver](#contentresolver)
      - [三个辅助类](#三个辅助类)
  - [基本组件](#基本组件)
    - [TextView 文本显示控件](#textview-文本显示控件)
      - [布局属性](#布局属性)
    - [Button 按钮](#button-按钮)
      - [布局属性](#布局属性-1)
      - [代码添加属性](#代码添加属性)
    - [EditText 文本输入框](#edittext-文本输入框)
      - [布局属性](#布局属性-2)
      - [代码添加属性](#代码添加属性-1)
    - [ImageView 图片](#imageview-图片)
      - [布局属性](#布局属性-3)
      - [代码添加属性](#代码添加属性-2)
    - [ProgressBar 进度条](#progressbar-进度条)
      - [布局属性](#布局属性-4)
      - [代码添加属性](#代码添加属性-3)
    - [ScrollView 滚动](#scrollview-滚动)
      - [布局属性](#布局属性-5)
      - [代码添加属性](#代码添加属性-4)
    - [RecyclerView 容器](#recyclerview-容器)
      - [核心组件](#核心组件)
      - [引入依赖](#引入依赖)
      - [布局属性](#布局属性-6)
      - [RecyclerView的使用](#recyclerview的使用)
  - [布局](#布局)
    - [测量和布局](#测量和布局)
    - [FrameLayout](#framelayout)
      - [自身属性](#自身属性)
      - [子视图属性](#子视图属性)
    - [LinearLayout](#linearlayout)
      - [自身属性](#自身属性-1)
      - [子视图属性](#子视图属性-1)
    - [RelativeLayout](#relativelayout)
      - [子视图属性](#子视图属性-2)
    - [ConstraintLayout](#constraintlayout)
      - [子视图属性](#子视图属性-3)
    - [总结](#总结)
  - [创建自定义组件](#创建自定义组件)
  - [属性动画](#属性动画)
    - [动画框架](#动画框架)
    - [常用API](#常用api)
    - [ViewPropertyAnimator](#viewpropertyanimator)
    - [ObjectAnimator](#objectanimator)
      - [动画插值器](#动画插值器)
    - [xmlAnimator](#xmlanimator)
      - [动画属性资源的定义](#动画属性资源的定义)
  - [数据储存](#数据储存)
    - [SharedPreferences](#sharedpreferences)
      - [简介](#简介)
      - [存储数据](#存储数据)
      - [读取数据](#读取数据)
      - [删除数据](#删除数据)
      - [完整代码实践](#完整代码实践)
    - [SQLite数据库](#sqlite数据库)
      - [SQLite数据库操作](#sqlite数据库操作)
      - [SQLite使用步骤](#sqlite使用步骤)
    - [文件存储](#文件存储)
      - [内部存储](#内部存储)
      - [序列化存储](#序列化存储)
    - [ContentProvider](#contentprovider-1)
      - [工作原理](#工作原理)
      - [ContentProvider的使用](#contentprovider的使用)
  - [异步交互](#异步交互)
    - [handler](#handler)
      - [核心组件和职责](#核心组件和职责)
      - [使用步骤](#使用步骤)
        - [使用 sendMessage](#使用-sendmessage)
        - [使用 post](#使用-post)
        - [安全的最佳实践](#安全的最佳实践)
    - [Coroutines 协程](#coroutines-协程)
      - [协程使用步骤](#协程使用步骤)
  - [网络通信](#网络通信)
    - [OkHttp](#okhttp)
      - [OkHttp使用步骤](#okhttp使用步骤)
  - [后台服务](#后台服务)
    - [基本特征](#基本特征)
    - [启动方式](#启动方式)
      - [通过startService()启动](#通过startservice启动)
      - [通过bindService()启动](#通过bindservice启动)
      - [混合启动方式](#混合启动方式)
    - [代码实现](#代码实现)
      - [startService()启动方式示例](#startservice启动方式示例)

## 前置条件
* [安装Android Studio](https://developer.android.google.cn/studio/index.html?hl=ro)

## 四大组件

[回到目录](#目录)

| 组件名称                                | 场景     | 用途                                                                                       |
| --------------------------------------- | -------- | ------------------------------------------------------------------------------------------ |
| [Activity](#activity)                   | 界面交互 | 是用户交互的入口点，代表一个界面，可以拉起其他的界面和组件                                 |
| [Service](#service)                     | 后台服务 | 处理耗时任务(即使应用退出)，比如下载，网络请求，跨进程访问等                               |
| [BroadcastReceiver](#broadcastreceiver) | 事件响应 | 捕获系统事件(时区变更，短信到达)或者用户发送到自定义事件，然后根据事件决定接下来的业务逻辑 |
| [ContentProvider](#contentprovider)     | 数据共享 | 对外安全暴露私有数据，提供统一的api访问，内部可以是不同存储方式的实现                      |

### 入口文件
AndroidManifest.xml

### Activity

[回到目录](#目录)

#### 创建一个Activity
* 首先在layout目录下创建一个**xml文件**，并设置文件名，如activity_1.xml
* 创建与**xml文件**对应的activity子类
* 把声明的子类在AndroidManifest.xml中注入
* **xml文件**可以添加组件
* kt文件可以为组件添加事件监听

#### Activity生命周期
| 生命周期    | 说明                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------ |
| onCreate()  | 首次创建Activity时调用，完成初始化工作，多用于创建视图、为列表绑定数据                     |
| onStart()   | 当Activity可见时调用，多用于启动后台任务                                                   |
| onResume()  | 当Activity开始与用户交互时调用，进入活跃状态，位于栈顶，可以接受用户输入                   |
| onPause()   | 要启动另一个Activity时调用，暂停当前Activity，但保留状态，可以保存数据，最后一个可控的回调 |
| onStop()    | 当Activity不可见时调用                                                                     |
| onDestroy() | 当Activity销毁时调用，发生在finish()方法执行时，或由于系统内存不足而销毁                   |
| onRestart() | 当Activity重新启动时调用，通常用于恢复数据                                                 |

#### Activity保存和恢复
| 方法                                              | 调用时机                                                         | 说明                                                                          | 使用场景                                                                                                      |
| ------------------------------------------------- | ---------------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| onSaveInstanceState(outState:Bundle)              | 系统未经“你的许可”，可能销毁了你的activity，则会被调用           | 用户主动销毁不调用，可能销毁即调用                                            | 从一个Activity启动另一个Activity <br> 屏幕方向切换 <br> home键返回，切换别的应用 <br> 关闭屏幕显示            |
| onRestoreInstanceState(savedInstanceState:Bundle) | 系统未经“你的许可”，确实销毁了你的activity，重新启动时则会被调用 | 用户主动销毁不调用，可能销毁即调用，与onSaveInstanceState()方法不一定成对调用 | 异常关闭了Activity，下次启动时则被调用 <br> onCreate() -> onStart() -> onRestoreInstanceState() -> onResume() |

#### Activity启动模式
在AndroidManifest.xml中,`<application>`标签下的`<activity>`中设置Activity的启动模式 `android:launchMode="启动模式`
| 启动模式       | 说明                                                                                   |
| -------------- | -------------------------------------------------------------------------------------- |
| standard       | 默认启动模式，按Activity启动顺序压入Task Stack中                                       |
| singleTop      | 栈顶复用模式，栈顶Activity实例已经存在，则不会创建新的实例，同一个Activity可以多次创建 |
| singleTask     | 如果Activity存在于栈中，弹出其上所有Activity，目标Activity置于栈顶并获得焦点           |
| singleInstance | 全局唯一模式，为目标Activity创建一个栈，栈顶Activity获得焦点，如果已经创建过则唤醒     |

除了从AndroidManifest.xml中设置启动模式外，也可以在代码中设置启动模式，比如通过Intent的setFlags()方法设置
~~~kotlin
val intent = Intent(this, MainActivity::class.java)
intent.setFlags(Intent.[Intent Flag])
~~~

| Intent Flag                    | 说明                                                                     |
| ------------------------------ | ------------------------------------------------------------------------ |
| FLAG_ACTIVITY_NEW_TASK         | 从后台服务中启动新的Activity；从通知栏点开新的任务                       |
| FLAG_ACTIVITY_SINGLE_TOP       | 点击通知或状态栏，不希望重新创建已经存在的Activity，而是复用现有实例     |
| FLAG_ACTIVITY_CLEAR_TOP        | 导航回已经存在的Activity，并清除其上的所有Activity，如从深层页面返回主页 |
| FLAG_ACTIVITY_CLEAR_TASK       | 完全清空当前任务，并创建一个新任务，比如用户注销返回登录界面             |
| FLAG_ACTIVITY_NO_HISTORY       | 启动一个页面时，不希望用户通过返回键回到该页面                           |
| FLAG_ACTIVITY_REORDER_TO_FRONT | 需要将后台的Activity移到前台而不是重新创建，比如多个步骤的表格填写       |

#### Fragment
Fragment表示应用界面中可重复使用的一部分，可以用于实现模块化的UI设计，在不同的Activity中重复使用

##### 创建Fragment
~~~kotlin
/**
 * MyFragment 类继承自 Fragment，用于显示自定义的 fragment 界面
 */
class MyFragment : Fragment() { 
    /**
     * 创建并返回 fragment 的视图层次结构
     * 
     * @param inflater 用于将 XML 布局文件转换为 View 对象的布局加载器
     * @param container 包含此 fragment 的父视图组，可能为 null
     * @param savedInstanceState 包含 fragment 上次保存状态的 Bundle 对象，可能为 null
     * @return 返回 inflate 后的 View 对象，作为 fragment 的根视图
     */
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? { 
        // 使用 inflater 将指定的布局文件（.xml）加载为 View 并返回
        return inflater.inflate(R.layout.fragment_my, container, false)
    }
}
~~~

##### 将Fragment添加到Activity中

Fragment拥有与Activity类似的栈(BACK stack，回退栈)，

* 通过xml文件添加Fragment(静态添加，无法在运行时动态移除)
    ~~~kotlin
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:name="com.example.myapplication.MyFragment" />
    ~~~

* 通过代码添加Fragment
    ~~~kotlin
    /**
    * 当Activity创建时调用此方法，用于初始化界面和添加Fragment
    * 
    * @param savedInstanceState 保存的实例状态，用于恢复之前保存的Activity状态
    */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 创建MyFragment实例并添加到Activity中
        val fragment = MyFragment()

        // 获取Fragment管理器并开始一个事务
        // 向指定容器添加Fragment，推荐使用FragmentContainerView容器
        // 将该操作添加到返回栈
        // 标记为"MyFragment"，提交事务使更改生效
        supportFragmentManager.beginTransaction()
        .add(R.id.fragment_container_view, fragment)
        .addToBackStack("MyFragment")
        .commit()
    }
    ~~~

##### Fragment生命周期
| 回调方法            | 说明                                               |
| ------------------- | -------------------------------------------------- |
| onAttach()          | 当Fragment被附加到Activity时调用                   |
| onCreate()          | 当Fragment创建时调用                               |
| onCreateView()      | 当Fragment创建视图时调用                           |
| onActivityCreated() | 当Fragment的Activity的onCreate()方法执行完毕时调用 |
| onStart()           | 当Fragment可见时调用                               |
| onResume()          | 当Fragment获得焦点时调用                           |
| onPause()           | 当Fragment失去焦点时或被其他Fragment覆盖时调用     |
| onStop()            | 当Fragment不可见时调用                             |
| onDestroyView()     | 当Fragment销毁视图时调用                           |
| onDestroy()         | 当Fragment销毁时调用                               |
| onDetach()          | 当Fragment被分离时调用                             |


#### Intent
Intent是Android提供的用来协助组件间的交互和通信机制，描述一次操作的动作和数据

* 显式Intent：指定目标组件，通过Intent对象启动目标组件
* 隐式Intent：不指定目标组件，通过Intent对象启动匹配的组件

##### Intent Flag
| Intent Flag | 说明               |
| ----------- | ------------------ |
| Component   | 指定来源和目标     |
| Action      | 指定动作行为       |
| Category    | 指定哪种组件来处理 |
| Data        | uri，指定数据源    |
| Type        | 指定消息数据类型   |
| Extras      | 拓展信息           |
| Flags       | 指定运行模式       |

##### IntentFilter 匹配规则

| 条件         | 匹配规则                                                                                                                                                                                                                                                                                                                  | 使用                                                                       |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Action       | 1. `<intent-filter>`中必须包含一个Action, Intent也必须指定一个Action<br>2. `<intent-filter>`中可以有一个或者多个Action, Intent匹配其中的一个Action即可                                                                                                                                                                    | `Intent("xxxAction")`                                                      |
| Data         | 1. `<intent-filter>`中描述了data, 必须在Intent中指定匹配的Data信息<br>2. `<intent-filter>`中没有描述data信息, Intent中可以不指定Data信息<br>3. `<intent-filter>`只指定了mimeType, 默认的URI的scheme是file或者content, 在Intent中必须同时设置mimeType和URI<br>4. `<intent-filter>`中有多个Data, Intent中只需要匹配一组即可 | `intent.setData()`                                                         |
| Category     | 1. Intent中未指定Category, `<intent-filter>`中也可以不指定Category<br>2. 要隐式启动一个Activity是就必须指定android.intent.category.DEFAULT的Category<br>3. Intent中指定了一个Category时, 会在具有Category的`<intent-filter>`中去找匹配组件                                                                                | `intent.addCategory()`<br>`intent.removeCategory()`<br>`intent.categories` |
| 整体匹配规则 | Action+Data+Category同时匹配, 才能启动Activity<br>Activity可以有多个intent-filter, Intent只需匹配任何一组`<intent-filter>`即可启动Activity                                                                                                                                                                                | -                                                                          |
| 匹配过程     | 1. 加载`<intent-filter>`列表<br>2. 依次去掉action、Data、Category匹配失败的`<intent-filter>`<br>3. 剩余`<intent-filter>`按优先级排序                                                                                                                                                                                      | -                                                                          |


### Service

[回到目录](#目录)

* 无ui，可后台长时间运行
* 启动后，切换应用仍会运行一段时间
* 可用于应用处理网络事务、音频播放、数据同步等
* 默认在主线程[UI线程]运行，除单独指定线程，并且不会单独在进程中运行

#### Service的创建
* 在AndroidManifest.xml中注册Service

| 属性             | 说明                  |
| ---------------- | --------------------- |
| android:name     | 指定Service的完整类名 |
| android:enabled  | 能否被实例化          |
| android:exported | 是否允许其他应用访问  |

~~~xml
<service
android:name=".MyService"
android:enabled="true"
android:exported="true" />
~~~

* 在Kotlin中创建Service

| 方法           | 说明                  |
| -------------- | --------------------- |
| onBind         | 当Service被绑定时调用 |
| onCreate       | 当Service创建时调用   |
| onStartCommand | 当Service启动时调用   |
| onDestroy      | 当Service销毁时调用   |

~~~kotlin
/**
 * MyService类继承自Service，用于在后台执行长时间运行的操作
 * 该服务不提供绑定功能，仅支持启动和停止
 */
class MyService : Service() { 
    /**
     * 当其他组件想要通过bindService()绑定到服务时调用
     * @param intent 启动服务的Intent对象
     * @return IBinder对象，用于与服务通信，此处返回null表示不支持绑定
     */
    override fun onBind(intent: Intent): IBinder? { 
        return null
    }

    /**
     * 当服务首次创建时调用，用于执行一次性设置操作
     * 该方法只在服务生命周期中调用一次
     */
    override fun onCreate() { 
        super.onCreate()
        Log.d(TAG, "onCreate")
    }

    /**
     * 当其他组件通过startService()启动服务时调用
     * @param intent 启动服务时传递的Intent对象，可能为null
     * @param flags 附加数据，用于描述如何启动服务
     * @param startId 服务启动请求的唯一标识符
     * @return 服务被杀死后的重启模式，返回super.onStartCommand()的默认值
     */
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int { 
            Log.d(TAG, "onStartCommand")
            return super.onStartCommand(intent, flags, startId)
    }

    /**
     * 当服务被销毁时调用，用于清理资源和执行收尾工作
     * 该方法在服务生命周期结束时调用
     */
    override fun onDestroy() { 
        super.onDestroy()
        Log.d(TAG, "onDestroy")
    }
}
~~~

#### Service的启动方式
| 启动方式       | 说明                                                                                                                       |
| -------------- | -------------------------------------------------------------------------------------------------------------------------- |
| startService() | 适用于独立与其他组件长期运行的服务，如播放音乐、下载文件等 需要自身stopService()来停止服务                                 |
| bindService()  | 适用于需要与其他组件进行通信的服务，如获取传感器数据、访问数据库等 需要所有绑定的组件调用unbindService()解除绑定才停止服务 |

#### Service的生命周期
| 回调方法           | 说明                                                                                                                                                                                                                                                                   |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `onCreate()`       | 系统会在服务首次创建时（在 `onStartCommand()` 或 `onBind()` 之前）调用此方法来执行一次性设置过程。如果服务已在运行，不会调用此方法。                                                                                                                                   |
| `onDestroy()`      | 当服务不再使用且被销毁时，系统会调用此方法。用于清理线程、已注册的监听器或接收器等资源。是服务接收的最后一个回调。                                                                                                                                                     |
| `onStartCommand()` | 当其他组件（例如 Activity）请求启动服务时，系统会通过调用 `startService()` 来调用此方法。执行此方法时，系统会启动服务，该服务可以在后台无限期运行。如果实现了此功能，需要在服务完成工作后通过 `stopSelf()` 或 `stopService()` 来停止服务。如果只想提供绑定，无需实现。 |
| `onBind()`         | 当其他组件想要与服务绑定（例如执行 RPC）时，系统会通过调用 `bindService()` 来调用此方法。在实现此方法必须提供一个接口，使客户端通过返回 `IBinder` 与服务进行通信。如果不允许绑定，则应返回 `null`。                                                                    |
| `onUnbind()`       | 当所有绑定的客户端都解绑（通过 `unbindService()`）时，系统会调用此方法。                                                                                                                                                                                               |


### BroadcastReceiver

[回到目录](#目录)

Broadcast 是一种用于应用程序之间或程序内部的一种通信机制
BroadcastReceiver 捕获系统事件或用户的自定义事件，并根据事件执行对应的逻辑

#### 创建BroadcastReceiver
~~~kotlin
/**
 * 广播接收器类，用于接收和处理广播消息
 * 继承自BroadcastReceiver，当接收到广播时会显示Toast提示
 */
class MyReceiver : BroadcastReceiver() { 
    /**
     * 当接收到广播时调用此方法
     * @param context 上下文对象，提供访问应用程序环境信息的接口
     * @param intent 包含广播数据的Intent对象
     */
    override fun onReceive(context: Context, intent: Intent) { 
        // 显示广播接收成功的Toast消息提示
        Toast.makeText(context, "MyBroadcastReceiver: Broadcast Received", Toast.LENGTH_SHORT).show()
    }
}
~~~

#### 注册BroadcastReceiver
* 在AndroidManifest.xml中注册BroadcastReceiver[静态注册，程序未启动也能监听广播]
~~~xml
广播接收器声明块
    
    该代码块声明了一个Android广播接收器组件，用于接收和处理特定的广播消息。
    
    组件属性说明：
    - android:name: 指定接收器的类名，".MyReceiver"表示该类在当前应用包名下
    - android:enabled: 设置为true表示该接收器在应用安装后默认启用
    - android:exported: 设置为true表示该接收器可以接收来自其他应用或系统的广播
    
    意图过滤器配置：
    - android:priority: 设置广播接收优先级为100，数值越大优先级越高
    - action: 声明该接收器可以处理的广播动作，此处为自定义广播动作"com.example.myapplication.MY_BROADCAST" 
<receiver
    android:name=".MyReceiver"
    android:enabled="true"
    android:exported="true"> 
    <intent-filter android:priority="100">
        <action android:name="com.example.myapplication.MY_BROADCAST" />
    </intent-filter>
</receiver>
~~~ 

* 在代码中注册BroadcastReceiver[动态注册]
~~~kotlin
/**
 * 主活动类，负责发送广播和注册/注销广播接收器
 */
class MainActivity : AppCompatActivity() { 
    private var mBroadcastReceiver: MyReceiver? = null
    private val broadcastAction = "com.example.myapplication.MY_BROADCAST"
    
    /**
     * 活动创建时的回调方法
     * @param savedInstanceState 保存的实例状态Bundle对象
     */
    override fun onCreate(savedInstanceState: Bundle?) { 
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        mBroadcastReceiver = MyReceiver()
        // 设置按钮点击监听器，用于发送广播
        findViewById<Button>(R.id.button).setOnClickListener(View.OnClickListener { 
            sendBroadcast(Intent(broadcastAction))
        })
    }
}

/**
 * 活动恢复时的回调方法，用于注册广播接收器
 */
override fun onResume() { 
    super.onResume()
    // 注册广播接收器以接收指定action的广播
    mBroadcastReceiver?.let { 
        registerReceiver(it, IntentFilter(broadcastAction), RECEIVER_EXPORTED)
    }
}

/**
 * 活动销毁时的回调方法，用于注销广播接收器
 */
override fun onDestroy() { 
    super.onDestroy()
    // 注销广播接收器以避免内存泄漏
    mBroadcastReceiver?.let { 
        unregisterReceiver(it)
    }
}
~~~

#### Broadcast 分类
| 广播类型 | 方法                                                                                                                                                                                                                    | 描述                                                                                   |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| 标准广播 | sendBroadcast(Intent intent)                                                                                                                                                                                            | 异步广播，发送给所有注册的接收器，但无法将处理结果发送给下一个接收器，无法终止广播传播 |
| 有序广播 | sendOrderedBroadcast(Intent intent, String receiverPermission)                                                                                                                                                          | 同步广播，同一时刻只有一个接收器收到，可以选择继续传播，拦截或修改广播                 |
| 本地广播 | LocalBroadcastManager.registerReceiver(BroadcastReceiver receiver, IntentFilter filter) <br>LocalBroadcastManager.sendBroadcast(Intent intent) <br>LocalBroadcastManager.unregisterReceiver(BroadcastReceiver receiver) | exported=false，只能动态注册，只能发送给本应用，注册为本地广播的接收器无法接受标准广播 |

~~~kotlin
    // 隐式Intent
    sendBroadcast(Intent("BROADCAST_ACTION"))
    // 显式Intent
    val intent = Intent(this, MyReceiver::class.java)
    intent.action = "BROADCAST_ACTION"
    sendBroadcast(intent)
~~~

### ContentProvider

[回到目录](#目录)

以标准化的方式在Android应用间共享数据，以相对安全的方式封装数据存储以及增删改查，提供对外统一接口供其他程序调用

* 通过 **uri**[universal resource identifier ，通用资源标识符]访问数据
* content://authority/path/id
    - content://：通用前缀
    - authority：内容提供者的唯一标识符，通常为包名+内容提供者的类名
    - path：内容提供者的数据路径，通常为数据表名
    - id：内容提供者的数据项ID，通常为数据项的行号

#### 创建ContentProvider
~~~kotlin
/**
 * MyContentProvider 是一个自定义的内容提供者类，用于管理应用程序数据的访问和操作。
 * 它继承自 Android 的 ContentProvider 类，提供了对结构化数据的标准化访问接口。
 */
class MyContentProvider : ContentProvider() { 
    /**
     * 获取指定 URI 对应数据的 MIME 类型
     * @param uri 指向所需数据的统一资源标识符
     * @return 返回对应数据的 MIME 类型字符串，如果无法识别则返回 null
     */
    override fun getType(uri: Uri): String? { 
        TODO("Implement this to handle requests for the MIME type of the data at the given URI.")
    }

    /**
     * 初始化内容提供者，在系统启动时调用
     * @return 初始化成功返回 true，失败返回 false
     */
    override fun onCreate(): Boolean { 
        TODO("Implement this to initialize your content provider on startup.")
    }

    /**
     * 向内容提供者中插入新的数据行
     * @param uri 指定要插入数据的位置 URI
     * @param values 包含要插入数据的键值对集合
     * @return 返回新插入数据行的 URI，插入失败则返回 null
     */
    override fun insert(uri: Uri, values: ContentValues?): Uri? { 
        TODO("Implement this to handle requests to insert a new row.")
    }

    /**
     * 删除指定条件的数据行
     * @param uri 指定要删除数据的位置 URI
     * @param selection SQL WHERE 子句，用于指定删除条件，null 表示删除所有行
     * @param selectionArgs WHERE 子句中占位符对应的参数值数组
     * @return 返回实际删除的数据行数量
     */
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<String>?): Int { 
        TODO("Implement this to handle requests to delete one or more rows.")
    }

    /**
     * 查询满足条件的数据
     * @param uri 指定要查询数据的位置 URI
     * @param projection 要查询的列名数组，null 表示查询所有列
     * @param selection SQL WHERE 子句，用于指定查询条件
     * @param selectionArgs WHERE 子句中占位符对应的参数值数组
     * @param sortOrder 查询结果的排序方式
     * @return 返回包含查询结果的 Cursor 对象
     */
    override fun query(uri: Uri, projection: Array<String>?, selection: String?, selectionArgs: Array<String>?, sortOrder: String?){ 
        TODO("Implement this to handle query requests from clients.")
    }

    /**
     * 更新满足条件的数据行
     * @param uri 指定要更新数据的位置 URI
     * @param values 包含要更新数据的键值对集合
     * @param selection SQL WHERE 子句，用于指定更新条件
     * @param selectionArgs WHERE 子句中占位符对应的参数值数组
     * @return 返回实际更新的数据行数量
     */
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<String>?): Int { 
        TODO("Implement this to handle requests to update one or more rows.")
    }
}
~~~
#### 注册ContentProvider
~~~xml
<provider
    android:name=".MyContentProvider"
    android:authorities="com.example.myapplication.MyContentProvider"
    android:enabled="true"
    android:exported="false" />

~~~

#### ContentResolver
~~~kotlin
/**
 * 测试ContentResolver的基本操作方法
 * 
 * 该函数演示了如何使用ContentResolver进行数据的增删改查操作，
 * 包括插入、删除、更新和查询数据
 */
fun testContentResolver() { 
    // 构造ContentProvider的URI地址
    val uri = Uri.parse("content://com.example.myapplication.MyContentProvider/user")
    
    // 创建要操作的数据内容值
    val value = ContentValues().apply { 
        put("user_id", 1)
        put("user_name", "张三")
    }

    // 向ContentProvider中插入一条用户数据
    contentResolver.insert(uri, value)

    // 删除user_id为1的用户数据
    contentResolver.delete(uri, "user_id=?", arrayOf("1"))

    // 更新user_id为1的用户数据
    contentResolver.update(uri, value, "user_id=?", arrayOf("1"))

    // 查询所有用户数据，按user_id升序排列
    val cursor = contentResolver.query(uri, 
        null, 
        null, 
        null, 
        "user_id ASC")

    // 遍历查询结果并处理每条数据
    while (cursor?.moveToNext() == true) { 
        // 处理数据
    }
}
~~~

#### 三个辅助类
* ContentUris
* UriMatcher
* ContentObserver

~~~kotlin
/**
 * 通过ContentUris.withAppendedId方法将指定的ID追加到URI路径末尾
 * @param uri 原始URI
 * @param id 要追加的ID值
 * @return 返回追加ID后的新URI
 */
ContentUris.withAppendedId(uri, id)

/**
 * 从URI中解析出ID值
 * @param uri 包含ID的URI
 * @return 返回解析出的ID值
 */
val id = ContentUris.parseId(uri)

/**
 * 创建UriMatcher实例用于URI匹配
 * @param code 当URI不匹配任何模式时返回的默认代码
 */
val uriMatcher = UriMatcher(UriMatcher.NO_MATCH)

// 添加URI匹配模式：匹配"user"路径，匹配成功返回代码1
uriMatcher.addURI("com.example.myapplication", "user", 1)

// 添加URI匹配模式：匹配"user/#"路径（#表示数字），匹配成功返回代码2
uriMatcher.addURI("com.example.myapplication", "user/#", 2)

/**
 * 自定义ContentObserver类，用于监听ContentProvider数据变化
 */
class MyContentObserver : ContentObserver(null) { 
    /**
     * 当被监听的URI对应的数据发生变化时调用
     * @param selfChange 如果为true表示数据变化是由当前进程引起的
     */
    override fun onChange(selfChange: Boolean) { 
        super.onChange(selfChange)
    }
}
~~~

## 基本组件

[回到目录](#目录)

### TextView 文本显示控件
[回到目录](#目录)

#### 布局属性

~~~xml
<TextView
        属性名="属性值"
        ....../>
~~~


| 属性               | 说明                       | 参数说明                                                                                                                                                   |
| ------------------ | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                 | 控件的id                   | @+id/textView: 创建一个id<br>@id/textView: 引用一个id                                                                                                      |
| layout_width       | 控件的宽度                 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                   |
| layout_height      | 控件的高度                 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                   |
| layout_margin      | 控件的外边距               | 具体数值: 边距大小                                                                                                                                         |
| text               | 显示的文本                 | 文本内容                                                                                                                                                   |
| textAllCaps        | 是否将文本转换为大写       | true: 转换为大写<br>false: 不转换                                                                                                                          |
| textSize           | 文本的大小                 | 具体数值: 尺寸大小                                                                                                                                         |
| textColor          | 文本的颜色                 | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                   |
| textStyle          | 文本的样式                 | normal: 正常样式<br>bold: 加粗样式<br>italic: 斜体样式<br>bold_italic: 加粗斜体样式                                                                        |
| autoLink           | 是否自动链接文本           | web: 自动链接网址<br>email: 自动链接邮箱地址<br>phone: 自动链接电话号码                                                                                    |
| textColorHighlight | 文本高亮颜色               | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                   |
| drawableTop        | 文本顶部图标               | @drawable/icon: 图标资源                                                                                                                                   |
| drawablePadding    | 图标和文本之间的间距       | 具体数值: 间距大小                                                                                                                                         |
| gravity            | 文本的对齐方式             | center: 居中对齐<br>center_vertical: 垂直居中对齐<br>center_horizontal: 水平居中对齐<br>left: 左对齐<br>right: 右对齐<br>top: 顶部对齐<br>bottom: 底部对齐 |
| letterSpacing      | 字符间距                   | 具体数值: 间距大小                                                                                                                                         |
| lineSpacingExtra   | 行间距                     | 具体数值: 间距大小                                                                                                                                         |
| maxLines           | 文本的最大行数             | 具体数值: 行数                                                                                                                                             |
| ellipsize          | 文本超出显示范围时如何显示 | start: 显示开头省略号<br>middle: 显示中间省略号<br>end: 显示末尾省略号<br>marquee: 滚动显示                                                                |

### Button 按钮
[回到目录](#目录)
#### 布局属性

~~~xml
<Button
        属性名="属性值"
        ....../>
<ImageButton
        属性名="属性值"
        ....../>
~~~

| 属性               | 说明                    | 参数说明                                                                                                                                                   |
| ------------------ | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                 | 控件的id                | @+id/button: 创建一个id<br>@id/button: 引用一个id                                                                                                          |
| layout_width       | 控件的宽度              | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                   |
| layout_height      | 控件的高度              | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                   |
| layout_margin      | 控件的外边距            | 具体数值: 边距大小                                                                                                                                         |
| text               | 显示的文本              | 文本内容                                                                                                                                                   |
| background         | 按钮的背景              | @drawable/button_background: 背景资源                                                                                                                      |
| padding            | 按钮的内边距            | 具体数值: 内边距大小                                                                                                                                       |
| gravity            | 按钮的文本对齐方式      | center: 居中对齐<br>center_vertical: 垂直居中对齐<br>center_horizontal: 水平居中对齐<br>left: 左对齐<br>right: 右对齐<br>top: 顶部对齐<br>bottom: 底部对齐 |
| contentDescription | `ImageButton`按钮的描述 | 文本内容                                                                                                                                                   |
| src                | `ImageButton`按钮的图标 | @drawable/icon: 图标资源                                                                                                                                   |
| drawableStart      | 按钮的图标              | @drawable/icon: 图标资源                                                                                                                                   |
| drawablePadding    | 图标和文本之间的间距    | 具体数值: 间距大小                                                                                                                                         |
| enable             | 按钮是否可用            | true: 可用<br>false: 不可用                                                                                                                                |
| clickable          | 按钮是否可点击          | true: 可点击<br>false: 不可点击                                                                                                                            |

#### 代码添加属性

~~~kotlin
// 1、设置按钮点击Toast提示
findViewById<Button>(R.id.button_click).setOnClickListener{
    // 显示Toast提示
    Toast.makeText(this, "点击了按钮", Toast.LENGTH_SHORT).show()
}

// 2、设置按钮长按Toast提示
findViewById<Button>(R.id.button_long_click).setOnLongClickListener{
    // 显示Toast提示
    Toast.makeText(this, "长按了按钮", Toast.LENGTH_SHORT).show()
}
~~~

### EditText 文本输入框
[回到目录](#目录)
#### 布局属性

~~~xml
<EditText
        属性名="属性值"
        ....../>
~~~

| 属性               | 说明                       | 参数说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------ | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                 | 控件的id                   | @+id/editText: 创建一个id<br>@id/editText: 引用一个id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| layout_width       | 控件的宽度                 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| layout_height      | 控件的高度                 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| layout_margin      | 控件的外边距               | 具体数值: 边距大小                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| hint               | 输入框的提示文本           | 文本内容                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| inputType          | 输入框的类型               | text: 文本输入框<br>number: 数字输入框<br>phone: 电话输入框<br>datetime: 日期时间输入框<br>email: 邮箱输入框<br>textMultiLine: 多行文本输入框<br>textCapSentences: 首字母大写输入框<br>textCapWords: 单词首字母大写输入框<br>textCapCharacters: 全字母大写输入框<br>textNoSuggestions: 无建议输入框<br>textPassword: 密码输入框<br>textVisiblePassword: 可见密码输入框<br>textMultiLine: 多行文本输入框<br>textAutoCorrect: 自动修正输入框<br>textAutoComplete: 自动完成输入框<br>textWebEditText: 网页输入框<br>textUri: URI输入框<br>textEmailAddress: 邮箱地址输入框<br>textPostalAddress: 邮政编码输入框<br>textPersonName: 人名输入框<br>textShortMessage: 短消息输入框<br>textLongMessage: 长消息输入框<br>textFilter: 过滤输入框<br>textPhonetic: 音标输入框<br>textWebEmailAddress: 网页邮箱地址输入框<br>textWebPassword: 网页密码输入框 |
| maxLenght          | 输入框的最大长度           | 具体数值: 最大长度                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| cursorVisible      | 输入框的游标是否可见       | true: 可见<br>false: 不可见                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| textColor          | 输入框的文本颜色           | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| textColorHint      | 输入框的提示文本颜色       | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| textColorHighlight | 输入框的文本高亮颜色       | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| digits             | 输入框只能输入的字符       | 具体数值: 输入内容                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| imeOptions         | 输入框的键盘(回车)完成按钮 | done: 完成按钮<br>go: 跳转按钮<br>next: 下一个按钮<br>search: 搜索按钮<br>send: 发送按钮                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


#### 代码添加属性
~~~kotlin
// 1、设置输入过滤器，仅允许英文字符和数字输入
val filter =InputFilter{ source: CharSequence?, start: Int, end: Int, dest: Spanned?, dstart: Int, dend: Int ->
                if(!source.toString()
                        .matches("[A-Za-z0-9]+".toRegex())
                )return@InputFilter ""
                null
            }
// 将过滤器应用到指定的EditText控件
findViewById<EditText>(R.id.set_filters).filters = arrayOf<InputFilter>(filter)

// 2、设置文本检查器
val et = findViewById<EditText>(R.id.add_text_changed_listener)
et.addTextChangedListener(object : TextWatcher {
    /**
     * 文本改变前的回调方法
     * @param s 当前文本内容
     * @param start 改变开始的位置
     * @param count 被改变的字符数量
     * @param after 改变后的字符数量
     */
    override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
        // 可留空，如无逻辑需要
    }

    /**
     * 文本改变时的回调方法
     * @param s 当前文本内容
     * @param start 改变开始的位置
     * @param before 改变前的字符数量
     * @param count 改变后的字符数量
     */
    override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
        // 可留空，如无逻辑需要
    }

    /**
     * 文本改变后的回调方法，用于验证输入内容是否符合要求
     * @param s 改变后的文本内容
     */
    override fun afterTextChanged(s: Editable?) {
        // 验证输入内容是否只包含#$%字符
        if (!s.toString().matches("[#$%]+".toRegex())){
            et.error = "仅允许#$%"
        }
    }
})

// 3、设置焦点监听
/**
 * 设置EditText焦点变化监听器
 * 用于处理EditText获取焦点和失去焦点时的UI变化
 * 参数v: 发生焦点变化的View对象
 * 参数hasFocus: 表示View是否获得焦点的布尔值
 */
val setOnFocusChangeListenerEt = findViewById<EditText>(R.id.set_on_focus_change_listener)
setOnFocusChangeListenerEt.setOnFocusChangeListener({ v, hasFocus ->
    if(hasFocus){
        // 获取焦点时清空提示
        setOnFocusChangeListenerEt.setText("")
        setOnFocusChangeListenerEt.error = null
    } else{
        // 失去焦点时设置提示
        setOnFocusChangeListenerEt.error = "失去焦点了"
    }
})
~~~

### ImageView 图片
[回到目录](#目录)

#### 布局属性

~~~xml
<ImageView
        属性名="属性值"
        ....../>
~~~

| 属性               | 说明               | 参数说明                                                                                                                                                                       |
| ------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | 控件的id           | @+id/imageView: 创建一个id<br>@id/imageView: 引用一个id                                                                                                                        |
| layout_width       | 控件的宽度         | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                       |
| layout_height      | 控件的高度         | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                       |
| layout_margin      | 控件的外边距       | 具体数值: 边距大小                                                                                                                                                             |
| adjustViewBounds   | 图片是否适应控件   | true: 适应<br>false: 不适应                                                                                                                                                    |
| contentDescription | 图片的描述         | 文本内容                                                                                                                                                                       |
| maxHeight          | 图片的最大高度     | 具体数值: 最大高度                                                                                                                                                             |
| maxWidth           | 图片的最大宽度     | 具体数值: 最大宽度                                                                                                                                                             |
| src                | 图片的资源         | @drawable/image: 图片资源<br>@mipmap/image: 图片资源                                                                                                                           |
| background         | 图片的背景         | @color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值                                                                                                                       |
| padding            | 图片的内边距       | 具体数值: 内边距大小                                                                                                                                                           |
| cropToPadding      | 图片是否剪切内边距 | true: 剪切<br>false: 不剪切                                                                                                                                                    |
| scaleType          | 图片的缩放类型     | fitXY: 填充父布局<br>fitStart: 适应内容并居左<br>fitCenter: 适应内容并居中<br>fitEnd: 适应内容并居右<br>center: 中心<br>centerCrop: 剪切并居中<br>centerInside: 适应内容并居中 |

#### 代码添加属性

~~~kotlin
// 创建一个图形变换矩阵，用于对ImageView进行缩放变换
// 设置X轴缩放比例为0.04，Y轴缩放比例为0.08
// 将变换矩阵应用到ID为matrix的ImageView控件上
val matrix = android.graphics.Matrix()
matrix.setScale(0.04f, 0.08f)
findViewById<ImageView>(R.id.matrix).imageMatrix = matrix
~~~

### ProgressBar 进度条
[回到目录](#目录)

#### 布局属性
~~~xml
<ProgressBar
        属性名="属性值"
        ....../>
~~~
| 属性                  | 说明                     | 参数说明                                                                                                                                                                                                                                                                                                                                                 |
| --------------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                    | 控件的id                 | @+id/progressBar: 创建一个id<br>@id/progressBar: 引用一个id                                                                                                                                                                                                                                                                                              |
| layout_width          | 控件的宽度               | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                                                                                                                                                                                                 |
| layout_height         | 控件的高度               | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小                                                                                                                                                                                                                                                                                 |
| layout_margin         | 控件的外边距             | 具体数值: 边距大小                                                                                                                                                                                                                                                                                                                                       |
| style                 | 进度条样式               | ?android:attr/progressBarStyleHorizontal: 水平直条样式<br>？android:attr/progressBarStyleSmall: 小尺寸圆形样式<br>？android:attr/progressBarStyleLarge: 大尺寸圆形样式<br>？android:attr/progressBarStyleInverse: 反向样式<br>？android:attr/progressBarStyleSmallInverse: 小尺寸反向样式<br>？android:attr/progressBarStyleLargeInverse: 大尺寸反向样式 |
| max                   | 进度条的最大值           | 具体数值: 最大值                                                                                                                                                                                                                                                                                                                                         |
| progress              | 进度条的当前进度         | 具体数值: 当前进度                                                                                                                                                                                                                                                                                                                                       |
| secondaryProgress     | 进度条的当前进度         | 具体数值: 当前进度                                                                                                                                                                                                                                                                                                                                       |
| indeterminate         | 进度条是否为不确定状态   | true: 不确定<br>false: 确定                                                                                                                                                                                                                                                                                                                              |
| indeterminateDrawable | 进度条的不确定状态的图片 | @drawable/image: 图片资源<br>@mipmap/image: 图片资源                                                                                                                                                                                                                                                                                                     |

图片旋转样式
~~~xml
<!--
    animated-rotate动画旋转组件

    该组件用于创建一个旋转动画效果，将指定的drawable资源进行360度旋转

    参数说明：
    android:drawable - 指定要进行旋转动画的drawable资源引用
    android:fromDegree - 动画开始时的旋转角度，单位为度
    android:toDegree - 动画结束时的旋转角度，单位为度
    android:pivotX - 旋转中心点的X轴坐标，50%表示在drawable宽度的中心位置
    android:pivotY - 旋转中心点的Y轴坐标，50%表示在drawable高度的中心位置
-->
<animated-rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/p0"
    android:fromDegrees="0"
    android:pivotX="50%"
    android:pivotY="50%"
    android:toDegrees="360" />
~~~

#### 代码添加属性
~~~kotlin
val progress = findViewById<ProgressBar>(R.id.progress)
// 启动一个后台线程来模拟进度更新
Thread{
    var p = 0
    while (p < 100){
        Thread.sleep(100) // 每100毫秒更新一次进度
        p++
        // 在主线程中更新进度条UI
        runOnUiThread {
            progress.progress = p
            progress.secondaryProgress = (p + 5)
        }
    }
}.start()
~~~

### ScrollView 滚动
[回到目录](#目录)

#### 布局属性

~~~xml
<ScrollView
        属性名="属性值"
        ......>
        <布局标签
            属性名="属性值"
            ......>
        </布局标签>
</ScrollView>

<HorizontalScrollView
        属性名="属性值"
        ......>
        <布局标签
            属性名="属性值"
            ......>
        </布局标签>
</HorizontalScrollView>
~~~

| 属性                  | 说明                   | 参数说明                                                                                                               |
| --------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| id                    | 控件的id               | @+id/scrollView: 创建一个id<br>@id/scrollView: 引用一个id                                                              |
| layout_width          | 控件的宽度             | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局                                             |
| layout_height         | 控件的高度             | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局                                             |
| fillViewport          | 滚动视图是否填充父布局 | true: 填充<br>false: 不填充                                                                                            |
| scrollbars            | 滚动条样式             | none: 无<br>horizontal: 水平滚动条<br>vertical: 垂直滚动条                                                             |
| scrollbarSize         | 滚动条大小             | 具体数值: 滚动条大小                                                                                                   |
| scrollbarFadeDuration | 滚动条淡出时间         | 具体数值: 淡出时间                                                                                                     |
| layout_weight         | 控件的权重             | 具体数值: 权重大小                                                                                                     |
| scrollbarStyle        | 滚动条样式             | insideOverlay: 覆盖在内容上方<br>insideInset: 嵌入内容内<br>outsideOverlay: 覆盖在内容上方<br>outsideInset: 嵌入内容内 |
| requiresFadingEdge    | 需要淡出边缘效果       | vertical: 垂直<br>horizontal: 水平                                                                                     |
| fadingEdgeLength      | 淡出边缘长度           | 具体数值: 淡出边缘长度                                                                                                 |
| overScrollMode        | 滚动模式               | never: 不允许滚动<br>always: 允许滚动<br>ifContentScrolls: 内容滚动时允许滚动                                          |

#### 代码添加属性
~~~kotlin
// 在滚动视图中添加内容

// 获取垂直线性布局容器
val vsvc = findViewById<LinearLayout>(R.id.vsvc)

// 循环创建50个TextView并添加到布局中
for (i in 1 .. 50){
    // 创建并配置TextView样式
    val textView = TextView(this).apply{
        text = "动态添加的 TextView $i"
        textSize = 16f
        setTextColor(Color.BLACK)
        setPadding(20, 10, 20, 10)
    }

    // 设置布局参数，配置上下边距
    val params = LinearLayout.LayoutParams(
        LinearLayout.LayoutParams.MATCH_PARENT,
        LinearLayout.LayoutParams.WRAP_CONTENT
    ).apply {
        topMargin = 5
    }
    textView.layoutParams = params

    // 将TextView添加到父布局中
    vsvc.addView(textView)
}


// 获取水平滚动视图中的线性布局容器
val hsvc = findViewById<LinearLayout>(R.id.hsvc)

// 设置水平滚动视图的滚动监听器，用于监听滚动位置变化
findViewById<HorizontalScrollView>(R.id.hsv).setOnScrollChangeListener({v, scrollX, scrollY, oldScrollX, oldScrollY ->
    Log.d(TAG, "ScrollX: $scrollX, ScrollY: $scrollY, oldScrollX: $oldScrollX, oldScrollY: $oldScrollY")
})

// 循环创建50个TextView并添加到水平滚动布局中
for(i in 1 .. 50){
    val textView = TextView(this).apply {
        text = "第 $i 个"
        textSize = 16f
        setTextColor(Color.BLACK)
        setBackgroundColor(Color.WHITE)
        setPadding(20, 10, 20, 10)
    }

    // 设置TextView的布局参数，包括上下边距和右边距
    val params = LinearLayout.LayoutParams(
        LinearLayout.LayoutParams.WRAP_CONTENT,
        LinearLayout.LayoutParams.WRAP_CONTENT
    ).apply {
        topMargin = 100
        rightMargin = 20
    }
    textView.layoutParams = params
    hsvc.addView(textView)
}
~~~

### RecyclerView 容器

[回到目录](#目录)

#### 核心组件
| 组件           | 说明                                           |
| -------------- | ---------------------------------------------- |
| Adapter        | 数据与视图绑定，创建ViewHolder，绑定数据到视图 |
| ViewHolder     | 缓存视图引用，存储item布局中的控件引用         |
| LayoutManager  | 定义布局方式，如垂直或水平，以及如何排列item   |
| ItemDecoration | 定义item的装饰，如分割线，边框，背景颜色等     |
| ItemAnimator   | 定义item的动画，如插入，删除，移动，改变大小等 |

#### 引入依赖
1. 在模组的 build.gradle文件中添加以下依赖项：

~~~gradle
// 添加RecyclerView依赖
dependencies {
    implementation(libs.androidx.recyclerview)
}
// 若添加失败尝试添加以下依赖项
dependencies {
    implementation("androidx.recyclerview:recyclerview:1.3.2")
}
~~~

2. 在项目级gradle.properties文件中添加网络代理
~~~properties
android.useAndroidX=true
android.enableJetifier=true

systemProp.http.proxyHost=http://127.0.0.1:端口号
~~~
3. sync now

#### 布局属性

~~~xml
<androidx.recyclerview.widget.RecyclerView
        属性名="属性值"
        ......>
~~~

| 属性          | 说明       | 参数说明                                                                   |
| ------------- | ---------- | -------------------------------------------------------------------------- |
| id            | 控件的id   | @+id/recyclerView: 创建一个id<br>@id/recyclerView: 引用一个id              |
| layout_width  | 控件的宽度 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局 |
| layout_height | 控件的高度 | match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局 |

#### RecyclerView的使用
1. 添加依赖
2. 布局Activity
3. 创建Item布局
    1. recycler_img_item.xml
    ~~~xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/p0"/>
        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/p0"/>
        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/p0"/>
        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/p0"/>

    </LinearLayout>
    ~~~
    2. recycler_text_item.xml
    ~~~xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <ImageView
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:src="@drawable/p0"/>
        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:layout_marginLeft="20dp">
            <TextView
                android:id="@+id/item_title"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="我是标题"
                android:textColor="#FF000000"
                android:textStyle="bold"
                android:textSize="18sp"/>
            <TextView
                android:id="@+id/item_text"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginTop="20dp"
                android:textColor="#FF000000"
                android:textSize="18dp"/>
        </LinearLayout>

    </LinearLayout>
    ~~~
4. 实现Adapter & ViewHolder
    ~~~kotlin
    /**
     * 设置Activity的内容视图，并初始化RecyclerView及其相关组件。
     *
     * @param layoutResID 布局资源ID，用于设置Activity的界面布局。
     */
    override fun setContentView(layoutResID: Int) {
        super.setContentView(layoutResID)

        val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)
        // 设置RecyclerView的布局管理器为垂直方向的瀑布流布局，列数为1（即线性布局效果）
        recyclerView?.setLayoutManager(
            StaggeredGridLayoutManager(
                1,
                StaggeredGridLayoutManager.VERTICAL
            )
        )
        // 初始化数据源，添加101个字符串项
        val data = mutableListOf<String?>()
        for (i in 0..100) {
            data.add("Item $i")
        }
        // 创建并设置适配器
        val adapter = MyAdapter(data)
        recyclerView?.setAdapter(adapter)
        // 添加分割线装饰
        recyclerView?.addItemDecoration(DividerItemDecoration(this, LinearLayoutManager.VERTICAL))
        // 设置默认的动画效果
        recyclerView?.itemAnimator = DefaultItemAnimator()
    }

    /**
     * RecyclerView的适配器类，用于绑定不同类型的数据到视图上。
     *
     * @property data 数据源列表，包含要显示的字符串数据。
     */
    class MyAdapter(private val data: MutableList<String?>) :
        RecyclerView.Adapter<MyAdapter.ViewHolder?>(){
        companion object{
            const val VIEW_TYPE_TEXT = 1
            const val VIEW_TYPE_IMG = 2
        }

        /**
         * 根据位置返回不同的视图类型。
         *
         * @param position 当前项在数据集中的位置。
         * @return 返回视图类型标识符（VIEW_TYPE_TEXT 或 VIEW_TYPE_IMG）。
         */
        override fun getItemViewType(position: Int): Int{
            return if (position % 2 == 0) VIEW_TYPE_TEXT else VIEW_TYPE_IMG
        }

        /**
         * 创建一个新的ViewHolder实例。
         *
         * @param parent 父容器ViewGroup。
         * @param viewType 视图类型标识符。
         * @return 返回新创建的ViewHolder对象。
         */
        override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyAdapter.ViewHolder {
            val view : View
            // 根据视图类型加载不同的布局文件
            if(viewType == VIEW_TYPE_TEXT){
                view = LayoutInflater.from(parent.getContext())
                    .inflate(R.layout.recycler_text_item, parent, false)
                view.tag = VIEW_TYPE_TEXT
            } else{
                view = LayoutInflater.from(parent.getContext())
                    .inflate(R.layout.recycler_img_item, parent, false)
                view.tag = VIEW_TYPE_IMG
            }
            return ViewHolder(view, data, this)
        }

        /**
         * 将数据绑定到指定位置的ViewHolder上。
         *
         * @param holder 要绑定数据的ViewHolder。
         * @param position 数据在列表中的位置。
         */
        override fun onBindViewHolder(holder: ViewHolder, position: Int) {
            holder.setText(data.get(position))
        }

        /**
         * 返回数据集合的大小。
         *
         * @return 数据集合的元素数量。
         */
        override fun getItemCount(): Int {
            return data.size
        }

        /**
         * ViewHolder类，用于持有RecyclerView中每个子项的视图引用。
         *
         * @property itemView 子项的根视图。
         * @property dataList 数据源列表。
         * @property adapter 所属的适配器实例。
         */
        class ViewHolder(itemView: View, var dataList: MutableList<String?>, val adapter: MyAdapter) : RecyclerView.ViewHolder(itemView){
            /**
             * 设置文本内容并处理点击事件。
             *
             * @param text 要设置的文本内容。
             */
            fun setText(text: String?){
                if(itemView.tag == VIEW_TYPE_TEXT){
                    itemView.findViewById<TextView>(R.id.item_text).text = text
                    // 文本类型项点击时移除第一个元素
                    itemView.setOnClickListener {
                        if (dataList.isNotEmpty()){
                            dataList.removeAt(0)
                            adapter.notifyItemRemoved(0)
                        }
                    }
                }else {
                    // 图片类型项点击时更新第一个元素的内容
                    itemView.setOnClickListener {
                        dataList[0] =  "Item ${(0..100).random()}"
                        adapter.notifyItemChanged(0)
                    }
                }
            }
        }


    }
    ~~~
5. 在Activity & Fragment中调用

## 布局

[回到目录](#目录)

Android的布局过程是ui构建的核心环节，本质是 **视图树(View Hierarchy)** 中每个 **节点(View)** 确定自身以及其子元素尺寸和位置的过程

### 测量和布局
1. 测量 Measure
    * 遍历子视图
        * 跳过View.GONE
        * 对其他子视图调用 measureChildWithMargins()
    * 计算最大子视图尺寸
        * 宽度：measureWidth + marginLeft + marginRight 的最大值
        * 高度：measureHeight + marginTop + marginBottom 的最大值
    * 处理自身尺寸
        * 最终宽度 = max(子视图最大宽度，背景最小宽度，建议最小宽度) + paddingLeft + paddingRight
        * 最终高度 = max(子视图最大高度，背景最小高度，建议最小高度) + paddingTop + paddingBottom
        * 结果还需满足父视图传入的MeasureSpec
    * 处理特殊子视图：设置了layout_gravity需重新测量调整位置
2. 布局 Layout
    * 确定布局区域 
    * 遍历子视图

### FrameLayout

核心价值在于通过**layout_gravity**实现子视图的精准叠加定位

#### 自身属性
| 属性                      | 描述             | 参数说明                                                                                                    |
| ------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------- |
| android:layout_width      | 视图宽度         | match_parent:匹配父视图宽度<br>wrap_content:包裹内容宽度<br>具体数值:具体数值                               |
| android:layout_height     | 视图高度         | match_parent:匹配父视图高度<br>wrap_content:包裹内容高度<br>具体数值:具体数值                               |
| android:layout_gravity    | 视图对齐方式     | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐<br>可以通过竖杠连接多个值 |
| android:background        | 视图背景         | 颜色值:颜色值<br>图片:图片路径                                                                              |
| android:foreground        | 视图前景         | 颜色值:颜色值<br>图片:图片路径                                                                              |
| android:foregroundGravity | 视图前景对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐                           |

#### 子视图属性

子视图默认排列在FrameLayout的左上角，且允许堆叠
最终位置为对其方式并加上边距值后的位置

| 属性                        | 描述           | 参数说明                                                                          |
| --------------------------- | -------------- | --------------------------------------------------------------------------------- |
| android:layout_gravity      | 子视图对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐 |
| android:layout_marginStart  | 子视图左边距   | 单位:dp                                                                           |
| android:layout_marginEnd    | 子视图右边距   | 单位:dp                                                                           |
| android:layout_marginTop    | 子视图上边距   | 单位:dp                                                                           |
| android:layout_marginBottom | 子视图下边距   | 单位:dp                                                                           |

### LinearLayout

核心优势在于**方向性排列**和**权重分配**，适合构建列表、表单等结构性界面，但嵌套或频繁使用layout_weight属性可能会导致**性能问题**。

#### 自身属性
| 属性                        | 描述                         | 参数说明                                                                                                                                   |
| --------------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **android:orientation**     | 排列方向                     | horizontal：水平排列<br>vertical：垂直排列                                                                                                 |
| android:layout_width        | 宽度                         | match_parent：填充父布局<br>wrap_content：内容自适应<br>具体数值                                                                           |
| android:layout_height       | 高度                         | match_parent：填充父布局<br>wrap_content：内容自适应<br>具体数值                                                                           |
| android:background          | 背景                         | 颜色值                                                                                                                                     |
| android:gravity             | 子视图对齐方式               | center：居中<br>center_vertical：垂直居中<br>center_horizontal：水平居中<br>left：左对齐<br>right：右对齐<br>top：上对齐<br>bottom：下对齐 |
| android:layout_gravity      | 子视图在父容器对齐方式       | center：居中<br>center_vertical：垂直居中<br>center_horizontal：水平居中<br>left：左对齐<br>right：右对齐<br>top：上对齐<br>bottom：下对齐 |
| android:layout_marginTop    | 设置子视图与父容器顶部的距离 | 单位：dp                                                                                                                                   |
| android:layout_marginBottom | 设置子视图与父容器底部的距离 | 单位：dp                                                                                                                                   |
| android:layout_marginLeft   | 设置子视图与父容器左侧的距离 | 单位：dp                                                                                                                                   |
| android:layout_marginRight  | 设置子视图与父容器右侧的距离 | 单位：dp                                                                                                                                   |
| ***android:divider***       | 设置ListView的分割线         | drawable资源文件                                                                                                                           |
| android:showDividers        | 设置ListView的分割线显示位置 | none：不显示；beginning：开头；middle：中间；end：结尾                                                                                     |
| **android:weightSum**       | 设置LinearLayout的权重和     | float类型                                                                                                                                  |

* 分割线示例
~~~xml
<?xml version="1.0" encoding="utf-8"?>

    这是一个Android Drawable资源文件，用于定义分割线

    主要功能：
    - 创建一个1x1像素的红色矩形drawable
    - 用于创建分割线

    属性说明：
    - android:shape="rectangle": 指定形状为矩形
    - android:width="1dp" 和 android:height="1dp": 设置形状尺寸为1x1像素
    - android:color="#FF0000": 设置填充颜色为纯红色

<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <size android:width="1dp" android:height="1dp"/>
    <solid android:color="#FF0000"/>

</shape>
~~~

#### 子视图属性
| 属性                   | 描述                           | 参数说明                                                                                            |
| ---------------------- | ------------------------------ | --------------------------------------------------------------------------------------------------- |
| android:layout_gravity | 设置子视图在父视图中的对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>fill:填充父视图                                   |
| android:layout_weight  | 设置子视图在父视图中的权重     | 根据父视图的weightSum值来分配空间，默认weightSum为4，**需要将layout_width或layout_height设置为0dp** |

### RelativeLayout

[回到目录](#目录)

是基于相对位置关系的布局容器，通过定义子视图相对于父容器或同级视图的位置来组织界面元素

#### 子视图属性

1. 对齐父容器边

| 描述                             | 属性                       | 参数说明                                |
| -------------------------------- | -------------------------- | --------------------------------------- |
| android:layout_alignParentStart  | 设置视图与父容器起始边对齐 | true:启用左对齐<br>false:禁用左对齐     |
| android:layout_alignParentEnd    | 设置视图与父容器结束边对齐 | true:启用右对齐<br>false:禁用右对齐     |
| android:layout_alignParentTop    | 设置视图与父容器顶部对齐   | true:启用顶部对齐<br>false:禁用顶部对齐 |
| android:layout_alignParentBottom | 设置视图与父容器底部对齐   | true:启用底部对齐<br>false:禁用底部对齐 |

2. 方向位置

| 描述                                              | 属性                             | 参数说明                     |
| ------------------------------------------------- | -------------------------------- | ---------------------------- |
| android:layout_toRightOf/ android:layout_toEndOf  | 设置视图相对于某个视图的右侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_toLeftOf/ android:layout_toStartOf | 设置视图相对于某个视图的左侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_below                              | 设置视图相对于某个视图的底部对齐 | @+id/viewId:指定某个视图的id |
| android:layout_above                              | 设置视图相对于某个视图的顶部对齐 | @+id/viewId:指定某个视图的id |

3. 边缘对齐

| 描述                       | 属性                               | 参数说明                     |
| -------------------------- | ---------------------------------- | ---------------------------- |
| android:layout_alignTop    | 设置视图与某个视图的顶部对齐       | @+id/viewId:指定某个视图的id |
| android:layout_alignBottom | 设置视图与某个视图的底部对齐       | @+id/viewId:指定某个视图的id |
| android:layout_alignLeft   | 设置视图与某个视图的左侧对齐       | @+id/viewId:指定某个视图的id |
| android:layout_alignRight  | 设置视图与某个视图的右侧对齐       | @+id/viewId:指定某个视图的id |
| android:layout_baseline    | 设置视图的基线与指定视图的基线对齐 | @+id/viewId:指定某个视图的id |

4. 居中控制

| 描述                          | 属性                       | 参数说明                                |
| ----------------------------- | -------------------------- | --------------------------------------- |
| android:layout_centerInParent | 设置视图在父容器中居中显示 | true:启用居中显示<br>false:禁用居中显示 |
| layout_centerHorizontal       | 垂直居中                   | true:启用垂直居中<br>false:禁用垂直居中 |
| layout_centerVertical         | 水平居中                   | true:启用水平居中<br>false:禁用水平居中 |

### ConstraintLayout

[回到目录](#目录)

用于构建复杂界面的灵活布局容器，通过 **约束关系(Constraint)** 精准控制子视图的相对位置和尺寸，相比传统布局能有效减少嵌套层级，**提升性能**

#### 子视图属性

1. 基本约束属性
    * 水平方向
        
        | 属性                                 | 描述                   | 参数说明                     |
        | ------------------------------------ | ---------------------- | ---------------------------- |
        | app:layout_constraintLeft_toLeftOf   | 左对齐                 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintLeft_toRightOf  | 设置视图左侧在目标右侧 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintRight_toLeftOf  | 设置视图右侧在目标左侧 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintRight_toRightOf | 右对齐                 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintStart_toStartOf | 起始边对齐             | @+id/viewId:指定某个视图的id |
        | app:layout_constraintStart_toEndOf   | 起始边在目标结束边     | @+id/viewId:指定某个视图的id |
        | app:layout_constraintEnd_toStartOf   | 结束边在目标起始边     | @+id/viewId:指定某个视图的id |
        | app:layout_constraintEnd_toEndOf     | 结束边对齐             | @+id/viewId:指定某个视图的id |

    * 垂直方向

        | 属性                                   | 描述           | 参数说明                     |
        | -------------------------------------- | -------------- | ---------------------------- |
        | app:layout_constraintTop_toTopOf       | 顶部对齐       | @+id/viewId:指定某个视图的id |
        | app:layout_constraintTop_toBottomOf    | 顶部在目标底部 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintBottom_toTopOf    | 底部在目标顶部 | @+id/viewId:指定某个视图的id |
        | app:layout_constraintBottom_toBottomOf | 底部对齐       | @+id/viewId:指定某个视图的id |

    * 特殊对齐

        | 属性                                       | 描述         | 参数说明                     |
        | ------------------------------------------ | ------------ | ---------------------------- |
        | app:layout_constraintBaseline_toBaselineOf | 文本基线对齐 | @+id/viewId:指定某个视图的id |

2. 边距尺寸约束
    * 常规边距
        
        | 属性                        | 描述             | 参数说明                  |
        | --------------------------- | ---------------- | ------------------------- |
        | android:layout_marginStart  | 设置视图起始边距 | 0dp:无边距<br>1dp:1dp边距 |
        | android:layout_marginEnd    | 设置视图结束边距 | 0dp:无边距<br>1dp:1dp边距 |
        | android:layout_marginTop    | 顶部边距         | 0dp:无边距<br>1dp:1dp边距 |
        | android:layout_marginBottom | 底部边距         | 0dp:无边距<br>1dp:1dp边距 |
        | android:layout_marginLeft   | 左侧边距         | 0dp:无边距<br>1dp:1dp边距 |
        | android:layout_marginRight  | 右侧边距         | 0dp:无边距<br>1dp:1dp边距 |

    * 目标隐藏时的边距(约束目标变为GONE时生效)

        | 属性                        | 描述               | 参数说明                                                                              |
        | --------------------------- | ------------------ | ------------------------------------------------------------------------------------- |
        | app:layout_goneMarginRight  | 目标隐藏时右侧边距 | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 目标宽度 + 目标margin** |
        | app:layout_goneMarginLeft   | 目标隐藏时左侧边距 | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 目标宽度 + 目标margin** |
        | app:layout_goneMarginTop    | 顶部边距           | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 目标高度 + 目标margin** |
        | app:layout_goneMarginBottom | 底部边距           | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 目标高度 + 目标margin** |
        | app:layout_goneMarginStart  | 启动边距           | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 宽度 + 目标margin**     |
        | app:layout_goneMarginEnd    | 结束边距           | 0dp:无边距<br>1dp:1dp边距<br>若目标隐藏时不移动，则**设置值 = 宽度 + 目标margin**     |

    * 特殊尺寸值：0dp(即MATCH_CONSTRAINT，根据约束条件拓展)；wrap_content(自适应内容，可配合强制约束)
    * 比例与限制

        | 属性                                | 描述             | 参数说明                          |
        | ----------------------------------- | ---------------- | --------------------------------- |
        | app:layout_constraintDimensionRatio | 宽高比           | 1:1:宽高比为1:1                   |
        | app:layout_constrainedWidth         | 强制宽度限制     | true:宽度限制<br>false:宽度不限制 |
        | app:layout_constrainedHeight        | 强制高度限制     | true:高度限制<br>false:高度不限制 |
        | android:minWidth                    | 设置视图最小宽度 | 0dp:无最小宽度                    |
        | android:minHeight                   | 设置视图最小高度 | 0dp:无最小高度                    |
        | android:maxWidth                    | 设置视图最大宽度 | 0dp:无最大宽度                    |
        | android:maxHeight                   | 设置视图最大高度 | 0dp:无最大高度                    |

3. 链式布局

第一个元素的app:layout_constraintLeft_toLeftOf属性为parent，最后一个元素的app:layout_constraintRight_toRightOf属性为parent，中间的元素的app:layout_constraintLeft_toRightOf属性为前一个元素，app:layout_constraintRight_toLeftOf属性为后一个元素，即**双向连接**

| 属性                                       | 描述             | 参数说明                                                                   |
| ------------------------------------------ | ---------------- | -------------------------------------------------------------------------- |
| app:layout_constraintHorizontal_chainStyle | 水平链式布局样式 | spread:均分<br>packed:均分且居中紧凑<br>spread_inside:均分且两侧紧贴父容器 |

~~~xml
<!--链式布局-->
    <!--spread-->
    <!--
        Button控件button5的布局定义
        该按钮参与水平链式约束，与button6、button7形成水平链条
        左侧对齐父容器左侧，右侧对齐到button6的左侧
        顶部位置在ratio控件的下方
    -->
    <Button
        android:id="@+id/button5"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="button5"
        app:layout_constraintHorizontal_chainStyle="spread"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@+id/button6"
        app:layout_constraintTop_toBottomOf="@id/ratio"/>

    <!--
        Button控件button6的布局定义
        该按钮是水平链条的中间元素
        左侧对齐到button5的右侧，右侧对齐到button7的左侧
        顶部位置与button5对齐
    -->
    <Button
        android:id="@+id/button6"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="button6"
        app:layout_constraintLeft_toRightOf="@+id/button5"
        app:layout_constraintRight_toLeftOf="@id/button7"
        app:layout_constraintTop_toTopOf="@id/button5"/>

    <!--
        Button控件button7的布局定义
        该按钮是水平链条的最后一个元素
        左侧对齐到button6的右侧，右侧对齐到父容器右侧
        顶部位置与button5对齐
    -->
    <Button
        android:id="@+id/button7"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="button7"
        app:layout_constraintTop_toTopOf="@id/button5"
        app:layout_constraintLeft_toRightOf="@id/button6"
        app:layout_constraintRight_toRightOf="parent"/>
~~~

4. 权重分配

通过结合链式布局和权重属性，实现权重分配，父容器默认权重总和为4

此外，也可以通过结合layout_constraintLeft_toLeftOf、layout_constraintRight_toRightOf属性和偏移，实现按比例确定位置

| 属性                                   | 描述         | 参数说明                   |
| -------------------------------------- | ------------ | -------------------------- |
| app:layout_constraintHorizontal_weight | 水平权重分配 | 0:不分配权重<br>1:分配权重 |
| app:layout_constraintVertical_weight   | 垂直权重分配 | 0:不分配权重<br>1:分配权重 |
| app:layout_constraintHorizontal_bias   | 水平偏移     | 0:不偏移<br>0.5:居中       |

5. 参考线标签

可以通过参考线标签，进一步实现复杂布局

~~~xml
    <!--
        Guideline控件用于在ConstraintLayout中创建参考线，帮助布局对齐
        android:id: 控件的唯一标识符
        android:layout_width: 布局宽度，wrap_content表示根据内容自适应
        android:layout_height: 布局高度，wrap_content表示根据内容自适应
        android:orientation: 参考线方向，vertical表示垂直方向
        app:layout_constraintGuide_begin: 参考线距离父容器开始位置的距离，100dp表示距离左边100dp处
    -->
    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_begin="100dp"/>
~~~

### 总结

| 特性     | LinearLayout  | FrameLayout  | RelativeLayout          | ConstraintLayout |
| -------- | ------------- | ------------ | ----------------------- | ---------------- |
| 布局逻辑 | 线性方向排列  | 视图堆叠     | 相对其他视图/父容器定位 | 视图间约束关系   |
| 嵌套     | 易嵌套过深    | 极少嵌套     | 可减少嵌套              | 显著减少嵌套     |
| 性能     | 嵌套多层时差  | 最优（简单） | 较差（两次测量）        | 最优（复杂布局） |
| 适用场景 | 简单列表/等分 | 叠加视图     | 中等复杂的相对定位      | 高复杂度布局     |
| 学习曲线 | 简单          | 简单         | 中等                    | 较高             |


## 创建自定义组件

[返回目录](#目录)

1. 在res目录下的values文件夹中创建attrs.xml文件
2. 在attrs.xml文件的<resources>标签中添加如下内容：`declare-styleable`标签中的`name`属性定义了自定义组件的名称，`attr`标签中的`name`属性定义了属性的名称，`format`属性定义了属性的类型

~~~xml
    <!--
        定义自定义控件IconTextView的样式属性
        该样式集合用于支持在XML布局文件中配置IconTextView控件的各种属性
    -->
    <declare-styleable name="IconTextView">
        <!-- 图标资源引用，用于设置控件显示的图标 -->
        <attr name="iconSrc" format="reference"/>
        <!-- 显示文本内容，用于设置控件显示的文字 -->
        <attr name="text" format="string"/>
        <!-- 文本颜色，用于设置文字的显示颜色 -->
        <attr name="textColor" format="color"/>
        <!-- 文本大小，用于设置文字的显示尺寸 -->
        <attr name="textSize" format="dimension"/>
    </declare-styleable>
~~~

3. 在主文件夹中创建自定义类IconTextView.kt
4. 重写其中的onMeasure()、onLayout()方法

~~~kotlin
class IconTextView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null) :
    ViewGroup(context, attrs){
    /**
     * 图标控件实例
     */
    private var mIcon: ImageView? = null

    /**
     * 文本控件实例
     */
    private var mText: TextView? = null

    /**
     * 初始化函数，在构造函数中调用
     */
    init{
        init(context, attrs)
    }

    /**
     * 初始化控件方法
     *
     * @param context 上下文对象，用于创建视图控件
     * @param attrs 属性集合，包含布局文件中定义的属性
     */
    private fun init(context: Context, attrs: AttributeSet?){
        // 创建图标和文本控件实例
        mIcon = ImageView(context)
        mText = TextView(context)

        // 将子视图添加到容器中
        addView(mIcon)
        addView(mText)

        // 解析自定义属性
        attrs?.let { parseAttributes(context, it) }


    }

    private fun parseAttributes(context: Context, attrs: AttributeSet) {
        val typedArray = context.obtainStyledAttributes(attrs, R.styleable.IconTextView)

        // 解析图标资源
        val iconResId = typedArray.getResourceId(R.styleable.IconTextView_iconSrc, -1)
        if (iconResId != -1) {
            mIcon?.setImageResource(iconResId)
        }

        // 解析文本内容
        val text = typedArray.getString(R.styleable.IconTextView_text)
        mText?.text = text

        // 解析文本颜色
        val textColor = typedArray.getColor(R.styleable.IconTextView_textColor,
            mText?.currentTextColor ?: 0xFF000000.toInt())
        mText?.setTextColor(textColor)

        // 解析文本大小
        val textSize = typedArray.getDimension(R.styleable.IconTextView_textSize, -1f)
        if (textSize != -1f) {
            mText?.textSize = textSize / context.resources.displayMetrics.scaledDensity
        }

        typedArray.recycle()
    }


    /**
     * 重写onMeasure:测量自定义视图的尺寸
     *
     * @param widthMeasureSpec 宽度测量规格，包含宽度值和测量模式
     * @param heightMeasureSpec 高度测量规格，包含高度值和测量模式
     */
    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        // 测量图标和文本组件的实际尺寸
        mIcon!!.measure(MeasureSpec.UNSPECIFIED, MeasureSpec.UNSPECIFIED)
        mText!!.measure(MeasureSpec.UNSPECIFIED, MeasureSpec.UNSPECIFIED)

        // 计算内容区域的宽度和高度
        var width = mIcon!!.measuredWidth + mText!!.measuredWidth
        var height = max(mIcon!!.measuredHeight, mText!!.measuredHeight)

        // 添加内边距到总尺寸
        width += paddingLeft + paddingRight
        height += paddingTop + paddingBottom

        // 根据测量规格解析最终尺寸并设置
        setMeasuredDimension(
            resolveSize(width, widthMeasureSpec),
            resolveSize(height, heightMeasureSpec)
        )
    }

    /**
     * 重写onLayout:布局回调方法，用于确定子视图的位置
     *
     * @param changed 布局是否发生变化
     * @param l 左边界位置
     * @param t 上边界位置
     * @param r 右边界位置
     * @param b 下边界位置
     */
    override fun onLayout(changed: Boolean, l: Int, t: Int, r: Int, b: Int) {
        val paddingLeft = paddingLeft
        val paddingTop = paddingTop

        // 布局图标视图，位置在左上角
        mIcon!!.layout(
            paddingLeft,
            paddingTop,
            paddingLeft + mIcon!!.measuredWidth,
            paddingTop + mIcon!!.measuredHeight
        )

        // 布局文本视图，位置在图标右侧
        mText!!.layout(
            paddingLeft + mIcon!!.measuredWidth,
            paddingTop,
            paddingLeft + mIcon!!.measuredWidth + mText!!.measuredWidth,
            paddingTop + mText!!.measuredHeight
        )
    }
}
~~~

5. 在布局文件中使用自定义控件IconTextView

~~~xml
<com.kei.helloworld.IconTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:iconSrc="@mipmap/ic_launcher_round"
        app:text="HelloWorld!!!"
        app:textColor="#FFFF0000"
        app:textSize="30sp" />
~~~

## 属性动画

### 动画框架

| 特性       | ViewPropertyAnimator   | ObjextAnimator               |
| ---------- | ---------------------- | ---------------------------- |
| 目标对象   | 仅限View               | 任意对象                     |
| 多属性动画 | 链式调用且自动合并     | 需手动优化                   |
| 灵活性     | 易嵌套过深             | 极少嵌套                     |
| 性能       | 低                     | 高                           |
| 使用场景   | View的简单、高性能动画 | 界面复杂动画、非View对象动画 |

### 常用API

| API             | 作用            | 对应属性动画中的方法               |
| --------------- | --------------- | ---------------------------------- |
| setTranslationX | 设置X轴偏移     | translationX()<br>translationXBy() |
| setTranslationY | 设置Y轴偏移     | translationY()<br>translationYBy() |
| setTranslationZ | 设置Z轴偏移     | translationZ()<br>translationZBy() |
| setX            | 设置X轴绝对位置 | x()<br>xBy()                       |
| setY            | 设置Y轴绝对位置 | y()<br>yBy()                       |
| setZ            | 设置Z轴绝对位置 | z()<br>zBy()                       |
| setRotation     | 设置平面旋转    | rotation()<br>rotationBy()         |
| setRotationX    | 设置沿x轴旋转   | rotationX()<br>rotationXBy()       |
| setRotationY    | 设置沿y轴旋转   | rotationY()<br>rotationYBy()       |
| setScaleX       | 设置X轴缩放     | scaleX()<br>scaleXBy()             |
| setScaleY       | 设置Y轴缩放     | scaleY()<br>scaleYBy()             |
| setAlpha        | 设置透明度      | alpha()<br>alphaBy()               |

### ViewPropertyAnimator
1. 获取组件对象
2. 通过某些事件监听触发动画效果(示例使用了点击事件)
3. 组件对象调用animate()方法然后链式调用设置动画效果

~~~kotlin
        // 获取蓝色视图组件
        val blueView = findViewById<View>(R.id.view3)
        /**
         * 设置ViewPropertyAnimator按钮的点击事件监听器
         * 该函数会在用户点击按钮时触发动画效果
         */
        findViewById<Button>(R.id.viewPropertyAnimator).setOnClickListener {
            // 执行blueView的组合动画：平移、透明度变化和缩放
            blueView.animate().translationX(500.0F)
                .alpha(0.5F)
                .scaleX(0.5F)
                .scaleY(0.5F)
                .setDuration(2000)
        }
~~~

### ObjectAnimator
1. 获取组件对象
2. 通过某些事件监听触发动画效果(示例使用了点击事件)
3. 创建AnimatorSet()对象
4. 通过AnimatorSet()对象调用playTogether()方法添加多个属性动画
5. ObjectAnimator()对象调用ofFloat()方法创建属性动画
6. AnimatorSet()对象调用setDuration()方法设置动画持续时间
7. AnimatorSet()对象interpolator属性可以为动画设置动画插值器
8. AnimatorSet()对象调用start()方法开始执行动画

~~~kotlin
        // 获取蓝色视图组件
        val blueView = findViewById<View>(R.id.view3)
        /**
         * 设置ObjectAnimator按钮的点击事件监听器
         * 该函数会在用户点击按钮时触发动画效果
         */
        findViewById<Button>(R.id.objectAnimator).setOnClickListener {
            // 创建动画集合，用于同时播放多个属性动画
            val set = AnimatorSet()
            set.playTogether(
                ObjectAnimator.ofFloat(blueView, "translationX", 600.0F),
                ObjectAnimator.ofFloat(blueView, "scaleY", 0.5F),
                ObjectAnimator.ofFloat(blueView, "alpha", 0.5F)
            )
            // 设置动画持续时间为2000毫秒
            set.setDuration(2000)
            // 设置动画插值器为加速减速插值器
            set.interpolator = AccelerateDecelerateInterpolator()
            // 开始执行动画
            set.start()
        }
~~~

#### 动画插值器

插值器控制动画的**变化速率**，将**线性时间进度(0.0-1.0)**转换为非线性进度值，实现加速、减速、弹跳等效果

| 插值器类型                       | 效果描述                 | 适用场景     | 函数代码                                   |
| -------------------------------- | ------------------------ | ------------ | ------------------------------------------ |
| AccelerateDecelerateInterpolator | 默认插值器，先加速后减速 | 自然运动     | new AccelerateDecelerateInterpolator()     |
| LinearInterpolator               | 匀速插值器               | 匀速运动     | new LinearInterpolator()                   |
| AccelerateInterpolator           | 持续加速                 | 物体下落     | new AccelerateInterpolator()               |
| DecelerateInterpolator           | 持续减速                 | 物体上升     | new DecelerateInterpolator()               |
| BounceInterpolator               | 结束时弹跳               | 落地反弹     | new BounceInterpolator()                   |
| OvershootInterpolator            | 超越终点后回弹           | 按钮点击反馈 | new OvershootInterpolator()                |
| PathInterpolator                 | 自定义曲线轨迹           | 轨迹运动     | new PathInterpolator(0.4F, 0.5F, 0F, 1.0F) |

### xmlAnimator
1. 获取组件对象
2. 通过某些事件监听触发动画效果(示例使用了点击事件)
3. 创建AnimatorInflater()对象，并调用loadAnimator()方法加载XML定义的属性动画资源
4. AnimatorInflater()对象调用addListener()方法添加状态监听器，重写监听方法
5. AnimatorInflater()对象调用setTarget()方法设置动画目标对象
6. AnimatorInflater()对象调用start()方法开始执行动画



~~~kotlin
        // 获取蓝色视图组件
        val blueView = findViewById<View>(R.id.view3)
        /**
         * 设置XML动画按钮的点击监听器
         * 该函数为按钮添加点击事件处理，加载并执行XML定义的属性动画
         * 监听动画的各种状态并在状态变化时显示对应的Toast提示
         */
        findViewById<Button>(R.id.xmlAnimator).setOnClickListener {
            // 加载XML中定义的属性动画资源
            val anim = AnimatorInflater.loadAnimator(
                this,
                R.animator.text_animator
            )

            // 为动画添加状态监听器，监听动画的开始、结束、重复和取消事件
            anim.addListener(object : Animator.AnimatorListener{
                override fun onAnimationCancel(animation: Animator) {
                    Toast.makeText(this@ActivityAnimation, "onAnimationCancel", Toast.LENGTH_SHORT).show()
                }
                override fun onAnimationEnd(animation: Animator) {
                    Toast.makeText(this@ActivityAnimation, "onAnimationEnd", Toast.LENGTH_SHORT).show()
                }
                override fun onAnimationRepeat(animation: Animator) {
                    Toast.makeText(this@ActivityAnimation, "onAnimationRepeat", Toast.LENGTH_SHORT).show()
                }
                override fun onAnimationStart(animation: Animator) {
                    Toast.makeText(this@ActivityAnimation, "onAnimationStart", Toast.LENGTH_SHORT).show()
                }
            })
            // 设置动画的目标视图
            anim.setTarget(blueView)
            // 启动动画
            anim.start()
        }
~~~

#### 动画属性资源的定义
1. 在res目录下创建**animator文件夹**
2. 创建**XML文件**，并添加以下内容
3. set标签中的ordering属性控制**动画播放顺序**
4. set标签下的objectAnimator标签定义了属性**动画效果**
5. 其中duration属性控制**动画持续时间**，propertyName属性控制**动画属性名称**，valueFrom属性控制动画**起始值**，valueTo属性控制动画**结束值**

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!--
    AnimatorSet配置文件

    该XML文件定义了一个属性动画集合，用于按顺序播放多个动画效果

    属性说明：
    xmlns:android - Android命名空间声明，必需项
    android:ordering - 动画播放顺序控制属性
        值为"sequentially"表示动画将按顺序依次播放
        可选值还包括"together"表示同时播放所有动画
-->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="sequentially">

    <!--
        透明度动画函数
        功能：在1000毫秒内将对象的透明度从1.0渐变到0.5
        参数：
            android:duration - 动画持续时间，单位为毫秒
            android:propertyName - 动画属性名称，此处为透明度alpha
            android:valueFrom - 属性起始值，完全不透明
            android:valueTo - 属性结束值，半透明状态
    -->
    <objectAnimator
        android:duration="1000"
        android:propertyName="alpha"
        android:valueFrom="1.0"
        android:valueTo="0.5"/>

    <!--
        水平位移动画函数
        功能：在1000毫秒内将对象沿X轴从位置0移动到位置600
        参数：
            android:duration - 动画持续时间，单位为毫秒
            android:propertyName - 动画属性名称，此处为水平位移translationX
            android:valueFrom - X轴起始位置坐标
            android:valueTo - X轴结束位置坐标
    -->
    <objectAnimator
        android:duration="1000"
        android:propertyName="translationX"
        android:valueFrom="0.0"
        android:valueTo="600.0"/>

    <!--
        旋转变换动画函数
        功能：在1000毫秒内将对象绕中心点旋转360度
        参数：
            android:duration - 动画持续时间，单位为毫秒
            android:propertyName - 动画属性名称，此处为旋转角度rotation
            android:valueFrom - 起始旋转角度，0度
            android:valueTo - 结束旋转角度，360度
    -->
    <objectAnimator
        android:propertyName="rotation"
        android:valueFrom="0"
        android:valueTo="360"
        android:duration="1000"/>


</set>
~~~



## 数据储存

[回到目录](#目录)

### SharedPreferences

#### 简介
* 核心概念​​：SharedPreferences 是 Android 系统提供的一个​​轻量级存储类​​，用于以 ​**​键值对（Key-Value）​**​ 的形式存储简单的数据。它本质上将一个 XML 文件保存在设备的私有目录中，从而实现了数据的持久化
* 存储位置​​：**/data/data/<你的应用包名>/shared_prefs/**目录下。每个 SharedPreferences 文件都是一个 **.xml** 文件
* ​数据类型​​：支持存储**基本数据类型**，包括 String（字符串）、`Int`（整型）、`Boolean`（布尔型）、`Float`（浮点型）、`Long`（长整型）以及 `Set<String>`（字符串集合）
* 模式​​：通常使用 **MODE_PRIVATE模式**，这意味着该文件仅供你的应用自身访问，确保了数据的安全性。其他允许跨应用访问的模式（如 MODE_WORLD_READABLE）由于安全风险，已被废弃

#### 存储数据

1. 创建SharedPreferences对象

~~~kotlin
// 在 Activity 或 Fragment 中
val sharedPref = getSharedPreferences("user_preferences", Context.MODE_PRIVATE)
~~~

* ​参数1 "user_preferences"​​：自定义的文件名。系统会自动添加 .xml后缀。文件最终会是 user_preferences.xml
* ​参数2 Context.MODE_PRIVATE​​：固定模式，表示私有文件。

2. 获取edit对象

~~~kotlin
val editor = sharedPref.edit()
~~~

3. 写入数据

~~~kotlin
editor.putInt("Integer", 1)// 存储整型数据
editor.putLong("Long", 1L)// 存储长整型数据
editor.putFloat("Float", 1.0f)// 存储浮点型数据
editor.putBoolean("Boolean", true)// 存储布尔型数据
editor.putString("username", "admin")// 存储字符串数据
val setData: Set<String> = setOf("one", "two", "three")
editor.putStringSet("StringSet", setData)// 存储字符串集合数据

editor.apply() // 提交数据
~~~


* **​键（Key）​​：** 是一个字符串，作为数据的唯一标识符，比如 "user_nickname"。之后要靠这个键来读取数据。
* **​值（Value）​​：** 你要存储的具体数据。
* apply()vs commit()​​：
    * apply()：​**​强烈推荐使用**​​。它是异步的，立即返回，不会阻塞你的用户界面（UI），性能更好
    * commit()​​：是同步的，会等待数据完全写入磁盘后才返回。如果在主线程频繁使用，可能会引起应用无响应（ANR），通常只在需要立即知道写入是否成功的特殊场景下使用

步骤2和步骤3可以合并为：
~~~kotlin
sharedPref.edit(){
    putInt("Integer", 1)// 存储整型数据
    putLong("Long", 1L)// 存储长整型数据
    putFloat("Float", 1.0f)// 存储浮点型数据
    putBoolean("Boolean", true)// 存储布尔型数据
    putString("username", "admin")// 存储字符串数据
    val setData: Set<String> = setOf("one", "two", "three")
    putStringSet("StringSet", setData)// 存储字符串集合数据
}
~~~

#### 读取数据
1. 创建SharedPreferences对象

~~~kotlin
val sharedPref = getSharedPreferences("user_preferences", Context.MODE_PRIVATE)
~~~

2. 获取数据

~~~kotlin
val username = sharedPref.getString("username", "")
val age = sharedPref.getInt("age", 0)
val setData = sharedPref.getStringSet("StringSet", null)
val male = sharedPref.getBoolean("male", true)
~~~
* **参数1 "username"​​：** 键，用于获取数据。
* **参数2 ""：** 默认值，如果获取的数据不存在，则返回这个默认值。

3. 更新ui

#### 删除数据

1. 创建SharedPreferences对象

~~~kotlin
val sharedPref = getSharedPreferences("user_preferences", Context.MODE_PRIVATE)
~~~

2. 创建edit对象

~~~kotlin
val editor = sharedPref.edit()
~~~

3. 删除数据

~~~kotlin
// 删除单个数据
editor.remove("username")
// 删除所有数据
editor.clear()

editor.apply()
~~~

步骤2和步骤3可以合并为：
~~~kotlin
sharedPref.edit(){
    remove("username")
    clear()
}

~~~
#### 完整代码实践

~~~kotlin
package com.kei.datebaseandnetwork

import android.content.Context
import android.os.Bundle
import android.util.Log
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.content.edit
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.kei.datebaseandnetwork.databinding.ActivitySharedPrefBinding

class ActivitySharedPref : ComponentActivity() {
    /**
     * SharedPreferences文件名常量
     */
    private val prefsName = "kei_prefs"

    /**
     * 视图绑定对象
     */
    private lateinit var binding: ActivitySharedPrefBinding

    /**
     * 日志标签常量
     */
    private val TAG = "SharedPrefActivity"

    /**
     * Activity创建时的回调方法
     *
     * @param savedInstanceState 保存的实例状态Bundle对象，用于恢复之前保存的状态数据
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 初始化视图绑定并设置布局
        binding = ActivitySharedPrefBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // 为按钮设置点击事件监听器
        binding.submit.setOnClickListener { saveData() }
        binding.read.setOnClickListener { readData() }
        binding.clear.setOnClickListener { clearData() }
    }

    /**
     * 保存用户输入的数据到SharedPreferences中
     * 该函数从界面输入框获取用户名、年龄、邮箱和配置信息，验证数据完整性后进行加密存储
     *
     * 无参数
     * 无返回值
     */
    private fun saveData(){
        // 获取用户输入的各个字段数据
        val username = binding.inputName.text.toString()
        val age = binding.inputAge.text.toString()
        val email = binding.inputEmail.text.toString()
        val config = binding.inputConfig.text.toString()

        // 验证所有字段都不为空
        if(username.isNotEmpty() && age.isNotEmpty() && email.isNotEmpty() && config.isNotEmpty()){
            val prefs = getSharedPreferences(prefsName, Context.MODE_PRIVATE)
            // edit() 方法自带commit()方法
            prefs.edit(){
                putString("username", username)
                putInt("age", age.toInt())
                putString("email", email)
                // 处理配置信息，按分号分割并加密存储
                val configs = config.split(";")
                val setValue : MutableSet<String> = mutableSetOf()
                configs.forEach {
                    val value = SimpleEncryptor(TAG).encrypt(it)
                    setValue.add(value)
                }
                Log.i(TAG, "encryptor user config:value = $setValue")
                putStringSet("configs", setValue)
            }
            binding.result.text = "数据保存成功"
        }else{
            Toast.makeText(this, "信息输入错误", Toast.LENGTH_SHORT).show()
        }
    }

    /**
     * 读取用户数据并显示在界面中
     *
     * 该函数从SharedPreferences中读取用户的个人信息和配置信息，
     * 对加密的配置信息进行解密处理，最后将所有信息格式化显示在界面上。
     *
     * 注意：该函数不接收参数，无返回值
     */
    private fun readData() {
        // 从SharedPreferences中读取用户基本信息
        val prefs = getSharedPreferences(prefsName, Context.MODE_PRIVATE)
        val username = prefs.getString("username", "")
        val age = prefs.getInt("age", 0)
        val email = prefs.getString("email", "")
        val configs = prefs.getStringSet("configs", null)

        // 解密配置信息
        val userConfig: MutableList<String> = mutableListOf()
        configs?.forEach {
            val decryptValue = SimpleEncryptor(TAG).decrypt(it)
            Log.i(TAG, "decrypt value, before = $it, after = $decryptValue")
            userConfig.add(decryptValue)
        }

        // 将用户信息显示在界面中
        binding.result.text = "用户名：$username\n年龄：$age\n邮箱：$email\n配置信息：${userConfig.joinToString(", ")}"
    }

    private fun clearData(){
        val prefs = getSharedPreferences(prefsName, Context.MODE_PRIVATE)
        prefs.edit(){
            clear()
        }
        binding.result.text = "数据已清空"
    }



    class SimpleEncryptor(tag: String) {
        fun encrypt(input: String): String {
            // 示例简单加密：反转字符串（仅作演示）
            return input.reversed()
        }

        fun decrypt(input: String): String {
            // 示例简单解密：反转字符串（仅作演示）
            return input.reversed()
        }
    }
}
~~~

### SQLite数据库

[回到目录](#目录)

SQLite是一种基于文件的关系型数据库，用于存储和检索数据。具有**轻量级、独立性、跨平台、事务性**的特点

#### SQLite数据库操作

| 操作 | 方法          | 参数说明                                                                                                                                                                          | 返回值                         |
| ---- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| 插入 | long insert() | @NotNull String table: 要插入数据的表名 <br> @Nullable String nullColumnHack: 插入数据时忽略的列名 <br> @Nullable ContentValues values: 要插入的数据                              | 新插入记录的行号，失败时返回-1 |
| 更新 | int update()  | @NotNull String table: 要更新的表名 <br> @Nullable ContentValues values: 要更新的数据 <br> @Nullable String whereClause: 更新条件 <br> @Nullable String[] whereArgs: 更新条件参数 | 更新记录的行数                 |
| 删除 | int delete()  | @NotNull String table: 要删除数据的表名 <br> @Nullable String whereClause: 删除条件 <br> @Nullable String[] whereArgs: 删除条件参数                                               | 删除记录的行数                 |



#### SQLite使用步骤
以实现简单的笔记管理功能为例，介绍SQLite数据库操作

1. 定义数据模型 在app/src/main/java/你的包名/目录下创建 **Note.kt**

~~~kotlin
data class Note(
    val id: Long? = null, // 主键
    val title: String, // 标题
    val content: String, // 内容
    val createTime: Long = System.currentTimeMillis() // 创建时间
)
~~~

2. 创建数据库帮助类 在相同的目录下创建 **NoteDatabaseHelper.kt** 这是最关键的一步。我们需要创建一个继承自SQLiteOpenHelper的类，它负责**数据库的创建**和**版本管理**

~~~kotlin
class NoteDbHelper(context: Context) : SQLiteOpenHelper(
    context,
    DATABASE_NAME,
    null,
    DATABASE_VERSION
){
    // 定义数据库结构信息
    companion object {
        const val DATABASE_NAME = "notes.db" // 数据库名称
        const val DATABASE_VERSION = 1 // 数据库版本
        const val TABLE_NAME = "notes" // 表名
        const val COLUMN_ID = "_id" // 推荐使用 _id 作为主键列名
        const val COLUMN_TITLE = "title" // 标题列名
        const val COLUMN_CONTENT = "content" // 内容列名
        const val COLUMN_CREATE_TIME = "create_time" // 创建时间列名
    }

    // 当数据库第一次被创建时调用，用于建表
    override fun onCreate(db: SQLiteDatabase?) {
        val createTableQuery = """
            CREATE TABLE $TABLE_NAME (
                $COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT,
                $COLUMN_TITLE TEXT NOT NULL,
                $COLUMN_CONTENT TEXT,
                $COLUMN_CREATE_TIME INTEGER
            )
        """.trimIndent()
        db?.execSQL(createTableQuery)
    }

    // 当数据库需要升级时调用（例如版本号增加）
    override fun onUpgrade(db: SQLiteDatabase?, oldVersion: Int, newVersion: Int) {
        db?.execSQL("DROP TABLE IF EXISTS $TABLE_NAME")
        onCreate(db)
    }

    // 插入一条新笔记
    fun addNote(title: String, content: String): Long {
        val db = writableDatabase
        val values = ContentValues().apply {
            put(COLUMN_TITLE, title)
            put(COLUMN_CONTENT, content)
            put(COLUMN_CREATE_TIME, System.currentTimeMillis())
        }
        // 插入操作，返回新行的ID，如果失败返回-1
        return db.insert(TABLE_NAME, null, values)
    }

    // 获取所有笔记，按创建时间倒序排列
    fun getAllNotes(): List<Note> {
        val notes = mutableListOf<Note>()
        val db = readableDatabase
        // 查询语句，? 是占位符
        val cursor = db.query(
            TABLE_NAME,
            null, // 选择所有列
            null, // WHERE 子句
            null, // WHERE 子句的参数
            null, // GROUP BY
            null, // HAVING
            "$COLUMN_CREATE_TIME DESC" // ORDER BY 按时间降序
        )

        cursor.use {
            // 遍历游标（Cursor），提取数据
            while (it.moveToNext()) {
                val id = it.getLong(it.getColumnIndexOrThrow(COLUMN_ID))
                val title = it.getString(it.getColumnIndexOrThrow(COLUMN_TITLE))
                val content = it.getString(it.getColumnIndexOrThrow(COLUMN_CONTENT))
                val createTime = it.getLong(it.getColumnIndexOrThrow(COLUMN_CREATE_TIME))
                notes.add(Note(id, title, content, createTime))
            }
        }
        return notes
    }

    // 更新一条笔记
    fun updateNote(note: Note): Int {
        val db = writableDatabase
        val values = ContentValues().apply {
            put(COLUMN_TITLE, note.title)
            put(COLUMN_CONTENT, note.content)
        }
        // 更新操作，返回受影响的行数
        return db.update(
            TABLE_NAME,
            values,
            "$COLUMN_ID = ?", // 通过ID定位要更新的笔记
            arrayOf(note.id.toString())
        )
    }

    // 根据ID删除一条笔记
    fun deleteNoteById(id: Long): Int {
        val db = writableDatabase
        // 删除操作，返回被删除的行数
        return db.delete(
            TABLE_NAME,
            "$COLUMN_ID = ?",
            arrayOf(id.toString())
        )
    }
}
~~~

3. 使用数据库帮助类 在app/src/main/java/你的包名/目录下创建 **NoteActivity.kt** 实例化NoteDbHelper，并调用其方法进行增删改查

~~~kotlin

class NoteActivity : AppCompatActivity() {
    
    // 实例化数据库帮助类
    private lateinit var dbHelper: NoteDbHelper

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_note)

        // 初始化数据库帮助类
        dbHelper = NoteDbHelper(this)

        // 1. 插入一条新笔记
        val newNoteId = dbHelper.addNote("我的第一篇笔记", "这是使用SQLite存储的内容！")
        Log.d("SQLiteDemo", "新笔记ID: $newNoteId")

        // 2. 查询所有笔记
        val allNotes = dbHelper.getAllNotes()
        for (note in allNotes) {
            Log.d("SQLiteDemo", "笔记: [ID:${note.id}] ${note.title} - ${note.content}")
        }

        // 假设我们要更新刚插入的笔记 (需要知道其ID)
        if (newNoteId != -1L) {
            val noteToUpdate = Note(newNoteId, "修改后的标题", "更新后的内容")
            val rowsUpdated = dbHelper.updateNote(noteToUpdate)
            Log.d("SQLiteDemo", "更新了 $rowsUpdated 行")
        }

        // 删除笔记的示例（谨慎操作）
        // val rowsDeleted = dbHelper.deleteNoteById(newNoteId)
        // Log.d("SQLiteDemo", "删除了 $rowsDeleted 行")
    }

    override fun onDestroy() {
        dbHelper.close() // 记得在不用时关闭数据库连接
        super.onDestroy()
    }
}
~~~



* 在实际开发中，可以通过各种事件监听来实现对数据库的操作，如点击按钮、输入框等。
* 在实际开发中，通常使用**Room数据库框架**来操作数据库，Room是一个基于SQLite的ORM框架，可以更轻松地操作数据库。


### 文件存储

[回到目录](#目录)

Android系统提供了两种主要的文件存储方式
**​内部存储**​​是每个应用独有的沙盒环境，位于/data/data/<应用包名>/目录下，为应用数据提供了最高级别的保护
​**​外部存储**​​可能是SD卡或设备的内置共享存储空间，适合存储需要共享或体积较大的文件

#### 内部存储

创建一个 FileStorageActivity.kt 文件用于演示文件操作，并添加以下代码：

* 文件写入操作

~~~kotlin
class FileStorageActivity : AppCompatActivity() {
    
    // 写入文件
    private fun writeToInternalStorage(filename: String, content: String) {
        try {
            // 打开文件输出流
            val outputStream: FileOutputStream = openFileOutput(filename, Context.MODE_PRIVATE)
            
            // 将字符串转换为字节并写入
            outputStream.write(content.toByteArray())
            
            // 关闭流释放资源
            outputStream.close()
            
            Log.d("FileIO", "文件写入成功: $filename")
        } catch (e: Exception) {
            Log.e("FileIO", "文件写入失败", e)
            e.printStackTrace()
        }
    }
    
    // 使用示例
    private fun saveUserData() {
        val userData = """
            {
                "name": "张三",
                "email": "zhangsan@example.com",
                "preferences": {"theme": "dark"}
            }
        """.trimIndent()
        
        writeToInternalStorage("user_config.json", userData)
    }
}
~~~

​代码说明：​​

  * openFileOutput()是Context类的方法，第一个参数是**文件名**，第二个参数是**操作模式**
  * MODE_PRIVATE表示**覆盖**原文件，MODE_APPEND表示**追加**内容
  * 必须使用try-catch处理可能的IOException
  * 操作完成后务必**关闭**流释放资源


* 文件读取操作

~~~kotlin
// 仍然在FileStorageActivity.kt中的FileStorageActivity类中添加以下方法

private fun readFromInternalStorage(filename: String): String {
    return try {
        // 打开文件输入流
        val inputStream: FileInputStream = openFileInput(filename)
        
        // 使用缓冲读取器提高读取效率
        val reader = BufferedReader(InputStreamReader(inputStream))
        val content = StringBuilder()
        var line: String?
        
        // 逐行读取内容
        while (reader.readLine().also { line = it } != null) {
            content.append(line).append("\n")
        }
        
        // 关闭资源
        reader.close()
        inputStream.close()
        
        // 返回读取的内容
        content.toString()
    } catch (e: FileNotFoundException) {
        Log.e("FileIO", "文件不存在: $filename", e)
        "" // 返回空字符串表示文件不存在
    } catch (e: Exception) {
        Log.e("FileIO", "文件读取失败", e)
        ""
    }
}

// 使用示例
private fun loadUserConfig() {
    val config = readFromInternalStorage("user_config.json")
    if (config.isNotEmpty()) {
        // 处理配置数据
        Log.d("FileIO", "加载的配置: $config")
    }
}
~~~
​代码说明：​​

  * 使用BufferedReader和InputStreamReader组合提高读取效率
  * 逐行读取适合文本文件，避免内存溢出
  * 需要处理文件不存在的特殊情况

#### 序列化存储

| 方案 | 使用场景 | 优缺点 |
| --- | --- | --- |
| `Serializable` | 将简单的对象持久化存储 | 优点：实现简单；<br>缺点：效率低，不适合跨进程高频传输 |
| `Parcelable` | 跨进程通信使用（如Intent、AIDL等） | 优点：效率高，Android专用；<br>缺点：实现较为复杂 |
| `JSON` | 网络传输，将复杂对象信息存储到文件 | 优点：跨平台，可读性好，兼容性强；<br>缺点：体积大 |

### ContentProvider

[回到目录](#目录)

ContentProvider是Android四大组件之一，它充当了一个​​标准化的**数据安全中间层**​​，允许你的应用将私有数据安全地**共享**给其他应用，或者去访问系统（如联系人、相册）或其他应用提供的数据它抽象了数据的底层存储细节（无论是SQLite数据库、文件还是网络数据），为所有数据访问提供了**统一的接口**

#### 工作原理

* **​ContentProvider（内容提供者）​​：** 数据提供方，负责封装对数据的增删改查等操作。你需要继承ContentProvider类并实现其核心方法
* **​ContentResolver（内容解析器）​​：** 数据访问方。在任何需要访问数据的地方（如Activity），你通过ContentResolver（通过getContentResolver()获取）来调用统一的方法（query, insert, update, delete），由它去找到并调用正确的ContentProvider
。这实现了数据访问方式的统一
* **​URI（统一资源标识符）​​：** 这是数据的“地址”，用于唯一标识ContentProvider和其中的数据。它遵循标准格式：content://authority/path/id

    * **content://：** 固定前缀，表示该URI用于ContentProvider
    * **authority：** 授权标识，通常为应用的包名加上特定标识，用于在系统中唯一指定一个ContentProvider
    * **path：** 路径，通常用于指定要操作的数据表（如users）
    * **id（可选）：** 特定记录的ID（如/users/5表示ID为5的用户）

#### ContentProvider的使用

1. ​创建数据库契约类​​（定义数据库结构常量）：文件位置​​：app/src/main/java/com/example/yourAppName/目录下，新建Kotlin文件，例如NoteContract.kt 集中定义**数据库名、表名、列名**等常量，避免硬编码和拼写错误。

~~~kotlin
// NoteContract.kt
object NoteContract {
    // ContentProvider的授权标识，通常使用应用包名
    const val AUTHORITY = "com.example.notepad.provider"

    // 定义Note数据表的模型
    object NoteEntry {
        const val TABLE_NAME = "notes"
        // 必须包含 _id 列，这是Adapter等组件所期望的
        const val _ID = "_id"
        const val COLUMN_TITLE = "title"
        const val COLUMN_CONTENT = "content"
        const val COLUMN_CREATED_TIME = "created_time"
    }
}
~~~

2. ​创建数据库帮助类​​（SQLiteOpenHelper）：在同目录下新建Kotlin文件，例如NoteDatabaseHelper.kt 管理数据库的**创建和版本升级**

~~~kotlin
// NoteDatabaseHelper.kt
import android.content.Context
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

class NoteDatabaseHelper(context: Context) : SQLiteOpenHelper(
    context,
    DATABASE_NAME,
    null,
    DATABASE_VERSION
) {
    companion object {
        const val DATABASE_NAME = "notes.db"
        const val DATABASE_VERSION = 1
    }

    override fun onCreate(db: SQLiteDatabase) {
        // 执行创建表的SQL语句
        val SQL_CREATE_NOTES_TABLE = """
            CREATE TABLE ${NoteContract.NoteEntry.TABLE_NAME} (
                ${NoteContract.NoteEntry._ID} INTEGER PRIMARY KEY AUTOINCREMENT,
                ${NoteContract.NoteEntry.COLUMN_TITLE} TEXT NOT NULL,
                ${NoteContract.NoteEntry.COLUMN_CONTENT} TEXT,
                ${NoteContract.NoteEntry.COLUMN_CREATED_TIME} INTEGER DEFAULT (strftime('%s','now'))
            )
        """.trimIndent()
        db.execSQL(SQL_CREATE_NOTES_TABLE)
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        // 简单处理：删除旧表，创建新表。实际项目需做数据迁移。
        db.execSQL("DROP TABLE IF EXISTS ${NoteContract.NoteEntry.TABLE_NAME}")
        onCreate(db)
    }
}
~~~

3. 实现自定义ContentProvider：在同目录下新建Kotlin文件，例如NoteProvider.kt 这是**最核心**的一步，我们需要创建一个类继承ContentProvider，并实现其**六个核心方法**

~~~kotlin
// NoteProvider.kt
import android.content.*
import android.database.Cursor
import android.net.Uri

class NoteProvider : ContentProvider() {
    private lateinit var dbHelper: NoteDatabaseHelper

    // UriMatcher用于匹配不同的URI请求
    private val uriMatcher = UriMatcher(UriMatcher.NO_MATCH).apply {
        // 注册URI模式
        // content://com.example.notepad.provider/notes -> 操作整个notes表
        addURI(NoteContract.AUTHORITY, "notes", NOTES)
        // content://com.example.notepad.provider/notes/5 -> 操作ID为5的单条笔记
        addURI(NoteContract.AUTHORITY, "notes/#", NOTE_ID)
    }

    companion object {
        // 定义匹配码
        private const val NOTES = 1
        private const val NOTE_ID = 2
    }

    override fun onCreate(): Boolean {
        // 初始化数据库帮助类
        dbHelper = NoteDatabaseHelper(context!!)
        return true // 返回true表示Provider初始化成功
    }

    // 查询数据
    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        val db = dbHelper.readableDatabase
        return when (uriMatcher.match(uri)) {
            NOTES -> {
                // 查询所有笔记
                db.query(
                    NoteContract.NoteEntry.TABLE_NAME,
                    projection,
                    selection,
                    selectionArgs,
                    null,
                    null,
                    sortOrder ?: "${NoteContract.NoteEntry.COLUMN_CREATED_TIME} DESC"
                )
            }
            NOTE_ID -> {
                // 查询单条笔记，从URI中提取ID
                val id = uri.lastPathSegment
                db.query(
                    NoteContract.NoteEntry.TABLE_NAME,
                    projection,
                    "${NoteContract.NoteEntry._ID} = ?",
                    arrayOf(id),
                    null,
                    null,
                    sortOrder
                )
            }
            else -> throw IllegalArgumentException("Unknown URI: $uri")
        }.apply {
            // 设置通知URI，当该URI对应的数据变化时，Cursor会知道
            setNotificationUri(context?.contentResolver, uri)
        }
    }

    // 插入数据
    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        val db = dbHelper.writableDatabase
        return when (uriMatcher.match(uri)) {
            NOTES -> {
                val id = db.insert(NoteContract.NoteEntry.TABLE_NAME, null, values)
                if (id > 0) {
                    // 插入成功，构造新插入数据的URI
                    val newUri = ContentUris.withAppendedId(uri, id)
                    // 通知所有监听此URI的数据观察者，数据已变更
                    context?.contentResolver?.notifyChange(newUri, null)
                    newUri
                } else {
                    throw SQLException("Failed to insert row into $uri")
                }
            }
            else -> throw IllegalArgumentException("Insertion not supported for $uri")
        }
    }

    // 更新数据
    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        val db = dbHelper.writableDatabase
        val count = when (uriMatcher.match(uri)) {
            NOTES -> db.update(NoteContract.NoteEntry.TABLE_NAME, values, selection, selectionArgs)
            NOTE_ID -> {
                val id = uri.lastPathSegment
                db.update(
                    NoteContract.NoteEntry.TABLE_NAME,
                    values,
                    "${NoteContract.NoteEntry._ID} = ?",
                    arrayOf(id)
                )
            }
            else -> throw IllegalArgumentException("Update not supported for $uri")
        }
        // 如果更新了数据，通知变化
        if (count > 0) {
            context?.contentResolver?.notifyChange(uri, null)
        }
        return count
    }

    // 删除数据
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int {
        val db = dbHelper.writableDatabase
        val count = when (uriMatcher.match(uri)) {
            NOTES -> db.delete(NoteContract.NoteEntry.TABLE_NAME, selection, selectionArgs)
            NOTE_ID -> {
                val id = uri.lastPathSegment
                db.delete(
                    NoteContract.NoteEntry.TABLE_NAME,
                    "${NoteContract.NoteEntry._ID} = ?",
                    arrayOf(id)
                )
            }
            else -> throw IllegalArgumentException("Deletion not supported for $uri")
        }
        if (count > 0) {
            context?.contentResolver?.notifyChange(uri, null)
        }
        return count
    }

    // 返回指定URI的MIME类型
    override fun getType(uri: Uri): String? {
        return when (uriMatcher.match(uri)) {
            NOTES -> "vnd.android.cursor.dir/vnd.${NoteContract.AUTHORITY}.note"
            NOTE_ID -> "vnd.android.cursor.item/vnd.${NoteContract.AUTHORITY}.note"
            else -> throw IllegalArgumentException("Unknown URI: $uri")
        }
    }
}
~~~

4. 在AndroidManifest.xml中注册Provider：app/src/main/AndroidManifest.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.notepad">

    <application ...>
        <activity ...> ... </activity>

        <!-- 注册ContentProvider -->
        <provider
            android:name=".NoteProvider"
            android:authorities="com.example.notepad.provider"
            android:exported="true" />
            <!-- 
                android:name: 你的ContentProvider类的完整路径。
                android:authorities: 授权标识，必须与代码中定义的AUTHORITY完全一致。
                android:exported: 是否允许其他应用访问。true表示允许。
            -->

    </application>
</manifest>
~~~

5. 在应用内或其他应用中通过ContentResolver访问数据（在Activity中）：

~~~kotlin
// 例如在您的 MainActivity.kt 中
class MainActivity : AppCompatActivity() {
    // 定义基础URI
    private val baseUri = Uri.parse("content://${NoteContract.AUTHORITY}/notes")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 示例1：插入一条新笔记
        val values = ContentValues().apply {
            put(NoteContract.NoteEntry.COLUMN_TITLE, "我的第一条笔记")
            put(NoteContract.NoteEntry.COLUMN_CONTENT, "这是通过ContentProvider保存的内容。")
        }
        val newUri = contentResolver.insert(baseUri, values)
        Log.d("NoteApp", "新笔记插入成功，URI: $newUri")

        // 示例2：查询所有笔记
        val cursor = contentResolver.query(baseUri, null, null, null, null)
        cursor?.use {
            while (it.moveToNext()) {
                val id = it.getLong(it.getColumnIndexOrThrow(NoteContract.NoteEntry._ID))
                val title = it.getString(it.getColumnIndexOrThrow(NoteContract.NoteEntry.COLUMN_TITLE))
                val content = it.getString(it.getColumnIndexOrThrow(NoteContract.NoteEntry.COLUMN_CONTENT))
                Log.d("NoteApp", "ID: $id, Title: $title, Content: $content")
            }
        }
    }
}
~~~

## 异步交互

### handler

#### 核心组件和职责

| 组件 | 职责 | 类比 |
| --- | --- | --- |
| Handler | **​​发送​​** 和 **​​处理​​** 消息| 公司的**​​快递员**​​，既负责把包裹寄出，也负责接收并处理送来的包裹。|
| Message | 消息的载体，可以在线程间传递数据 | ​**​包裹**​​，里面装着要传递的信息或任务|
| MessageQueue | 一个按时间排序的​**​消息队列**​​，用于存储所有通过 Handler 发送的消息 | ​**​快递分拣中心**​​，所有寄出的包裹都暂时存放在这里，排队等待发货 |
| Looper | 运行 MessageQueue 的线程，不断循环，从 MessageQueue 中依次取出消息，并分发给对应的 Handler 处理 | **分拣员/送货员**​​，不停地从分拣中心取包裹，并按照地址交给对应的快递员 |

一个线程通常只有一个 MessageQueue和一个 Looper，但可以有多个 Handler对象

#### 使用步骤

##### 使用 sendMessage

在 Activity 或 Fragment 中（通常在主线程），创建 Handler 并指定如何处理消息。

* 步骤1：创建Handler对象，关联主线程Looper对象，并重写handleMessage方法
* 步骤2：重写handleMessage方法，处理消息
* 步骤3：创建线程模拟耗时操作
* 步骤4：构造消息对象发送到主线程

完整类实现

~~~kotlin
package com.kei.handlerdemo

import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.os.Message
import android.widget.TextView
import androidx.activity.ComponentActivity

/**
 * 发送消息Activity
 * 用于演示如何在子线程中执行耗时操作，并通过Handler将结果传递到主线程更新UI
 */
class SendMessageActivity : ComponentActivity() {

    private lateinit var textView: TextView

    /**
     * 主线程Handler，用于处理来自子线程的消息
     * 使用Looper.getMainLooper()确保Handler在主线程中处理消息
     * Looper能够自动处理到达的消息，并调用handleMessage方法进行处理
     */
    // 步骤1： 创建Handler对象，关联主线程Looper对象，并重写handleMessage方法
    private val mainHandler = object : Handler(Looper.getMainLooper()){
        /**
         * 处理从子线程发送过来的消息
         * @param msg 从子线程发送过来的消息对象
         */
        override fun handleMessage(msg: Message){
            // 步骤2：在这里处理消息，此方法运行在主线程，可以安全更新ui
            // 根据消息类型处理不同的业务逻辑
            when (msg.what){
                1 -> {
                    // 从msg.obj中获取数据
                    // 更新textView显示加载结果
                    textView.text = msg.obj as String
                }
                2 -> {
                    // 也可以使用arg1，arg2等传递简单整型数据
                    // 处理进度信息（当前未使用）
                    val progress = msg.arg1
                    // 例如处理更新进度条
                }
            }
        }
    }

    /**
     * Activity创建时的回调方法
     * @param savedInstanceState 用于恢复Activity状态的Bundle对象
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_send_message)
        
        // 初始化textView控件
        textView = findViewById(R.id.textView)

        // 创建并启动一个子线程来模拟耗时操作
        Thread{
            // 步骤3：模拟耗时操作，睡眠2秒
            Thread.sleep(2000)
            val result = "数据加载完成"

            // 步骤4：构造消息对象并发送到主线程
            val message = mainHandler.obtainMessage() // 使用obtainMessage() 复用消息对象
            message.what = 1 // 消息标识
            message.obj = result // 消息内容
            mainHandler.sendMessage(message) // 发送消息
        }.start()
    }
}
~~~

##### 使用 post

* 步骤1: 创建Handler对象（关联主线程Looper）
* 步骤2: 在子线程中执行耗时操作
* 步骤3：使用post()方法，将UI更新操作直接投递到主线程执行

完整类实现

~~~kotlin
package com.kei.handlerdemo

import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.widget.TextView
import androidx.activity.ComponentActivity

/**
 * PostActivity类用于演示Handler的post和postDelayed方法的使用
 * 通过子线程执行耗时操作后，使用Handler.post()方法将UI更新操作切换到主线程执行
 */
class PostActivity: ComponentActivity() {

    private lateinit var textView: TextView

    /**
     * 主线程Handler，用于将Runnable切换到主线程执行
     * 使用Looper.getMainLooper()确保Handler在主线程中处理消息
     */
    private val mainHandler = Handler(Looper.getMainLooper())

    /**
     * Activity创建时的回调方法
     * @param savedInstanceState 用于恢复Activity状态的Bundle对象
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContentView(R.layout.activity_post)

        textView = findViewById(R.id.tv_post)

        // 创建并启动一个子线程来模拟耗时操作
        Thread{
            // 模拟耗时操作，睡眠2秒
            Thread.sleep(2000)
            val result = "数据加载完成"

            // 使用Handler.post()将UI更新操作切换到主线程执行
            mainHandler.post {
                textView.text = result
            }
        }.start()
    }

    /**
     * 延迟任务方法，使用Handler.postDelayed()延迟执行UI更新操作
     * 在指定延迟时间后将Runnable切换到主线程执行
     */
    private fun delayTask(){
        mainHandler.postDelayed({
            textView.text = "延迟2秒执行"
        }, 2000)
    }
}
~~~

##### 安全的最佳实践

* 将 Handler 定义为静态内部类，并不再持有外部类的强引用
* 使用 WeakReference 弱引用 Activity
* 在处理消息前，先判断 Activity 是否还存在
* 在 Activity 销毁时，移除所有未处理的消息和回调

完整类实现

~~~kotlin
package com.kei.handlerdemo

import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.os.Message
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import java.lang.ref.WeakReference

class SafeHandlerActivity : AppCompatActivity() {
    private lateinit var textView: TextView
    private lateinit var safeHandler: SafeHandler // 使用安全的 Handler

    // 1. 将 Handler 定义为静态内部类，并不再持有外部类的强引用
    class SafeHandler(activity: SafeHandlerActivity) : Handler(Looper.getMainLooper()) {
        // 2. 使用 WeakReference 弱引用 Activity
        private val activityWeakReference: WeakReference<SafeHandlerActivity> = WeakReference(activity)

        override fun handleMessage(msg: Message) {
            // 3. 在处理消息前，先判断 Activity 是否还存在
            val activity = activityWeakReference.get()
            if (activity != null) { // 如果 Activity 没被回收
                // 安全地更新 UI
                when (msg.what) {
                    MSG_UPDATE_TEXT -> {
                        activity.textView.text = msg.obj as? String
                    }
                    // 处理其他类型的消息...
                }
            }
            // 如果 Activity 为 null（已被回收），则不执行任何操作
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_safe_handler)
        textView = findViewById(R.id.tv_safe_handler)

        // 初始化安全的 Handler
        safeHandler = SafeHandler(this)

        // 在子线程中工作
        Thread {
            // 模拟耗时任务
            Thread.sleep(1500)
            val result = "数据加载完毕！"

            // 创建并发送消息
            val message = safeHandler.obtainMessage().apply {
                what = MSG_UPDATE_TEXT
                obj = result
            }
            safeHandler.sendMessage(message)
        }.start()
    }

    // 4. 在 Activity 销毁时，移除所有未处理的消息和回调
    override fun onDestroy() {
        super.onDestroy()
        safeHandler.removeCallbacksAndMessages(null) // 参数为 null 表示移除所有
    }

    companion object {
        // 定义消息类型常量，便于管理
        private const val MSG_UPDATE_TEXT = 1
    }
}
~~~

### Coroutines 协程

**Kotlin协程**是处理异步操作的推荐方案，它能帮你用更直观的代码替代复杂的回调，并高效管理后台任务

#### 协程使用步骤

1. 前置工作：添加依赖

~~~kotlin
dependencies {
    // 协程核心库
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.10.2")
    // 为ViewModel提供viewModelScope
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.9.4")
    // 为Activity/Fragment提供lifecycleScope
    implementation(libs.androidx.lifecycle.runtime.ktx.v280)
}
~~~

2. 在ViewModel中使用协程 文件位置：app/src/main/java/com/yourpackage/MyViewModel.kt 创建一个MyViewModel类，从网络获取数据并更新ui

~~~kotlin
class MyViewModel(private val repository: MyRepository) : ViewModel() {

    // 使用LiveData暴露UI状态
    private val _data = MutableLiveData<String>()
    val data: LiveData<String> = _data

    private val _isLoading = MutableLiveData<Boolean>()
    val isLoading: LiveData<Boolean> = _isLoading

    // 触发数据获取
    fun fetchData() {
        // 在viewModelScope内启动协程
        viewModelScope.launch {
            _isLoading.value = true
            try {
                // 执行挂起函数，获取数据
                val result = repository.fetchDataFromNetwork()
                _data.value = result
            } catch (e: Exception) {
                // 处理错误
                _data.value = "Error: ${e.message}"
            } finally {
                _isLoading.value = false
            }
        }
    }
}

// 假设的Repository，包含挂起函数
class MyRepository {
    // 声明为挂起函数，表明其内部执行耗时操作
    suspend fun fetchDataFromNetwork(): String {
        // 使用withContext确保耗时操作在IO线程池进行，保证主线程安全
        return withContext(Dispatchers.IO) {
            // 模拟网络请求，如使用Retrofit（Retrofit本身已支持挂起函数）
            kotlinx.coroutines.delay(2000) // 模拟2秒网络延迟
            "Hello from Network!"
        }
    }
}
~~~

* **viewModelScope.launch​​:** 在ViewModel的作用域内**启动一个新的协程**，默认在​​主线程​​开始执行。这使得你可以在协程内直接更新UI状态（如设置_isLoading.value）

* ​**​withContext(Dispatchers.IO)​​:** 这是一个关键的**挂起函数**。它将协程的执行上下文切换到为I/O操作优化的线程池，执行耗时的网络或数据库操作。操作完成后，协程会自动切回原来的线程（此处是主线程）这使得fetchDataFromNetwork函数是​​主线程安全​​的，意味着你可以安全地从主线程调用它。

3. 在Activity/Fragment中处理UI交互 文件位置：app/src/main/java/com/yourpackage/MyActivity.kt

~~~kotlin
class MyActivity : AppCompatActivity() { 
    private val viewModel: MyViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 观察LiveData，当数据变化时更新UI
        viewModel.data.observe(this) { data ->
            // 更新TextView等UI组件
            textView.text = data
        }

        viewModel.isLoading.observe(this) { isLoading ->
            progressBar.visibility = if (isLoading) View.VISIBLE else View.GONE
        }

        // 点击按钮触发数据获取
        button.setOnClickListener {
            viewModel.fetchData()
        }

        // 使用lifecycleScope执行与界面生命周期相关的协程任务
        lifecycleScope.launchWhenResumed {
            // 此协程只会在Activity处于Resumed状态时执行
            // 适合执行一些需要界面完全可见才进行的操作
        }
    }
}
~~~

4. 处理并行任务

~~~kotlin
// 在ViewModel或Repository中
suspend fun fetchDataInParallel(): List<String> =
    kotlinx.coroutines.coroutineScope { // 创建一个协程作用域
        val deferredOne = async { repository.fetchFromSource1() } // 启动第一个异步任务
        val deferredTwo = async { repository.fetchFromSource2() } // 启动第二个异步任务

        // 等待两个任务都完成并返回结果
        val result1 = deferredOne.await()
        val result2 = deferredTwo.await()
        listOf(result1, result2)
    }
~~~

## 网络通信

[回到目录](#目录)

### OkHttp

**OkHttp**是一个**开源**的**HTTP客户端**，**用于**在Android和Java应用程序中执行网络请求。

#### OkHttp使用步骤

1. 添加网络权限和依赖

在 AndroidManifest.xml 文件中添加网络权限：
~~~xml
<!-- 添加网络权限 -->
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
~~~

在 gradle/libs.versions.toml 中定义 OkHttp 版本和依赖：

~~~toml
[versions]
okhttp = "5.3.0"

[libraries]
okhttp = { group = "com.squareup.okhttp3", name = "okhttp", version.ref = "okhttp"}
~~~

在 app/build.gradle.kts 中引入依赖：
~~~kotlin
dependencies {
    // 添加OkHttp依赖
    implementation(libs.okhttp)
}
~~~

2. 创建网络服务类

创建 NetworkService.kt 类，包含 GET 和 POST 请求方法：
* 初始化 OkHttpClient 实例
* 实现 GET 请求方法 makeGetRequest
* 实现 POST 请求方法 makePostRequest

~~~kotlin
/**
 * 网络请求服务类
 */
class NetworkService {
    private val client = OkHttpClient() // 创建OkHttpClient实例，用于发送网络请求

    /**
     * 发送GET请求的示例方法
     * @param url 请求的URL地址
     * @param callback 回调函数，用于处理请求结果
     */
    fun makeGetRequest(url: String, callback: (String?, IOException?) -> Unit) {
        // 1. 构建请求对象
        val request = Request.Builder()
            .url(url) // 设置请求URL
            .build()  // 构建Request对象

        // 2. 创建Call对象并异步执行
        client.newCall(request).enqueue(object : Callback {
            // 3. 处理请求失败的情况
            override fun onFailure(call: Call, e: IOException) {
                // 请求失败，通过回调传递异常信息
                callback(null, e)
            }

            // 4. 处理请求成功的情况
            override fun onResponse(call: Call, response: Response) {
                // 5. 检查HTTP响应状态码是否成功(200-299)
                if (response.isSuccessful) {
                    // 请求成功，通过回调传递响应体内容
                    response.body?.let { responseBody ->
                        callback(responseBody.string(), null)
                    } ?: run {
                        callback("", null) // 如果body为null，返回空字符串
                    }
                } else {
                    // 请求失败，创建异常并传递错误码
                    callback(null, IOException("请求失败，错误码：${response.code}").also { 
                        it.printStackTrace() 
                    })
                }
            }
        })
    }

    /**
     * 发送POST请求的示例方法
     * @param url 请求的URL地址
     * @param jsonBody POST请求体中的JSON数据
     * @param callback 回调函数，用于处理请求结果
     */
    fun makePostRequest(url: String, jsonBody: String, callback: (String?, IOException?) -> Unit){
        // 1. 定义请求体的媒体类型为JSON
        val json = "application/json; charset=utf-8".toMediaType()
        
        // 2. 将字符串转换为RequestBody对象
        val body = jsonBody.toRequestBody(json)

        // 3. 构建POST请求对象
        val request = Request.Builder()
            .url(url) // 设置请求URL
            .post(body) // 设置请求方法为POST，并附带请求体
            .build() // 构建Request对象

        // 4. 创建Call对象并异步执行
        client.newCall(request).enqueue(object : Callback{
            // 5. 处理请求失败的情况
            override fun onFailure(call: Call, e: IOException) {
                // 请求失败，通过回调传递异常信息
                callback(null, e)
            }

            // 6. 处理请求成功的情况
            override fun onResponse(call: Call, response: Response) {
                // 7. 检查HTTP响应状态码是否成功(200-299)
                if(response.isSuccessful){
                    // 请求成功，通过回调传递响应体内容
                    response.body?.let { responseBody ->
                        callback(responseBody.string(), null)
                    } ?: run {
                        callback("", null) // 如果body为null，返回空字符串
                    }
                }else{
                    // 请求失败，创建异常并传递错误码
                    callback(null, IOException("请求失败，错误码：${response.code}").also { 
                        it.printStackTrace() 
                    })
                }
            }
        })
    }
}
~~~

3. 在 Activity 中使用网络服务

在 MainActivity.kt 中：
* 启用 ViewBinding 功能以访问 UI 元素
* 创建 NetworkService 实例
* 在按钮点击事件中调用相应的网络请求方法
* 处理网络请求结果并在主线程中更新 UI

~~~kotlin
class MainActivity : ComponentActivity() {

    // 延迟初始化绑定器
    private lateinit var binding: ActivityMainBinding

    // 创建网络服务实例
    private val networkService = NetworkService()


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 1. inflate XML 布局文件，创建 View 对象
        binding = ActivityMainBinding.inflate(layoutInflater)

        // 2. 将根视图设置为 Activity 的内容视图
        setContentView(binding.root)

        // 3. 现在可以通过 binding 访问布局中的所有视图组件
        binding.btnGet.setOnClickListener {
            networkService.makeGetRequest("https://httpbin.org/get") { response: String?, error: IOException? ->
                runOnUiThread {
                    if (error != null){
                        Log.e("NetworkDemo", "Get请求失败：${error.message}")
                        Toast.makeText(this, "Get请求失败：${error.message}", Toast.LENGTH_SHORT).show()
                        binding.tvResult.text = "Get请求失败：${error.message}"
                    }else{
                        Log.d("NetworkDemo", "Get请求成功：${response}")
                        Toast.makeText(this, "Get请求成功:${response}", Toast.LENGTH_SHORT).show()
                        binding.tvResult.text = "Get请求成功：${response}"
                    }
                }
            }
        }

        binding.btnPost.setOnClickListener {
            val jsonBody = """
                {
                    "name": "John Doe",
                    "age": 30
                }
            """.trimIndent()

            networkService.makePostRequest("https://httpbin.org/post", jsonBody) { response: String?, error: IOException? ->
                runOnUiThread {
                    if (error != null){
                        Log.e("NetworkDemo", "Post请求失败：${error.message}")
                        Toast.makeText(this, "Post请求失败：${error.message}", Toast.LENGTH_SHORT).show()
                        binding.tvResult.text = "Post请求失败：${error.message}"
                    }else{
                        Log.d("NetworkDemo", "Post请求成功：${response}")
                        Toast.makeText(this, "Post请求成功,${response}", Toast.LENGTH_SHORT).show()
                        binding.tvResult.text = "Post请求成功：${response}"
                    }
                }
            }
        }
    }
}
~~~

## 后台服务

[回到目录](#目录)


### 基本特征
* **无 UI 界面：** 在后台运行，不与用户直接交互。
* **生命周期独立：** 不受 Activity 生命周期直接影响（启动它的 Activity 销毁后，Service 可以继续运行）。
* **运行在主线程：** 这一点至关重要！默认情况下，Service 与你的应用运行在同一个进程的主线程（UI 线程）中。因此，你不能直接在 Service 中执行**耗时操作**（如网络请求、大量文件读写），否则会阻塞主线程，导致应用无响应（ANR）。必须在 Service 内部**创建子线程**来处理这些任务

### 启动方式

####  通过startService()启动

* **生命周期**流程：**onCreate()→ onStartCommand()→ (运行中) → onDestroy()**
* onStartCommand()的返回值：这个返回值决定了服务被系统意外杀死后如何重启，非常重要
  * START_STICKY：服务被杀死后会被系统**重新创建**，但之前的 Intent 数据会**丢失**（传入 null）。适合**音乐播放**等持续任务
  * START_NOT_STICKY：服务被杀死后**不会自动重启**。适合一次性的任务，如**下载**单个文件
  * START_REDELIVER_INTENT：服务被杀死后会被**重启**，并且最后一个 Intent 会被**重新传递**。适合必须完成的任务，如**支付**回调

#### 通过bindService()启动

这种方式允许组件（如 Activity）与 Service 进行双向通信。

* **生命周期**流程：onCreate()→ onBind()→ (运行中，与绑定者交互) → onUnbind()→ onDestroy()
* 通信桥梁 IBinder：服务通过 onBind()方法返回一个 IBinder对象，绑定组件通过这个对象来调用服务内部的方法，实现交互

#### 混合启动方式
一个 Service 可以同时被 startService()启动并被 bindService()绑定。这时，必须同时调用 stopService()和**所有绑定组件**的 unbindService()，服务才会销毁

### 代码实现

#### startService()启动方式示例

以开启后台日志服务为例：

1. 创建后台日志服务类LoggingService.kt，继承 Service类，需要重写onCreate()、onStartCommand()、onDestroy()方法，onBind()方法可返回null

~~~kotlin
class LoggingService: Service() {

    companion object{
        const val TAG = "LoggingService"
        const val EXTRA_MESSAGE = "extra_massage" // 用于Intent传递数据的键
    }

    // 服务创建时被调用（仅调用一次）
    override fun onCreate() {
        super.onCreate()
        Log.d(TAG, "日志服务已创建")
    }

    // 核心方法，每次startService都会调用
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Log.d(TAG, "收到启动命令，startID：$startId")

        // 1.从Intent中获取要记录的消息
        val message = intent?.getStringExtra(EXTRA_MESSAGE)?: "默认消息"

        // 2.开启新线程执行耗时操作，避免阻塞主线程
        thread(start = true) {
            // 模拟耗时的保存操作
            Log.d(TAG, "后台线程开始工作：${Thread.currentThread().name}")
            Thread.sleep(3000)
            Log.i("APP_LOG", "消息已记录：$message")

            // 3.任务完成后，服务自行停止
            stopSelf(startId)
            Log.d(TAG, "任务完成，服务已停止")
        }

        // 4.返回值，如果服务被杀死，希望系统重新创建并重传最后的Intent
        return START_REDELIVER_INTENT
    }

    // 必须实现的方法，但对于startService方式可返回null
    override fun onBind(intent: Intent?): IBinder? = null

    // 服务销毁时清理资源
    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, "日志服务已销毁")
    }
}
~~~

2. 在AndroidManifest.xml中注册服务，manifest/application标签内添加如下内容：

~~~xml
<!--注册LoggingService-->
<service android:name=".LoggingService"/>
~~~

3. 在需要使用服务时，调用startService()方法启动服务，并传递Intent对象，Intent对象中可以携带数据

示例在主活动中通过按钮点击启动服务：

~~~kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 启动服务按钮
        findViewById<Button>(R.id.btn_start_service).setOnClickListener {
            // 1. 创建Intent, 指定要启动的服务
            val intent = Intent(this, LoggingService::class.java).apply{
                // 2.通过Intent传递数据给服务
                putExtra(LoggingService.EXTRA_MESSAGE, "这是一条来自 Activity 的消息，时间：${System.currentTimeMillis()}")
            }

            // 3.启动服务
            startService(intent)
        }

        // 停止服务按钮
        findViewById<Button>(R.id.btn_stop_service).setOnClickListener {
            // 停止服务的Intent
            val stopIntent = Intent(this, LoggingService::class.java)
            stopService(stopIntent)
        }
    }
}
~~~



























