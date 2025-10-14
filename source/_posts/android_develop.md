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
      - [子视图排列属性](#子视图排列属性)
    - [LinearLayout](#linearlayout)
      - [自身属性](#自身属性-1)
      - [子视图属性](#子视图属性)
    - [RelativeLayout](#relativelayout)
      - [子视图属性](#子视图属性-1)
    - [ConstraintLayout](#constraintlayout)

## 前置条件
* [安装Android Studio](https://developer.android.google.cn/studio/index.html?hl=ro)

## 四大组件

[回到目录](#目录)

|组件名称|场景|用途|
|---|---|---|
|[Activity](#activity)|界面交互|是用户交互的入口点，代表一个界面，可以拉起其他的界面和组件|
|[Service](#service)|后台服务|处理耗时任务(即使应用退出)，比如下载，网络请求，跨进程访问等|
|[BroadcastReceiver](#broadcastreceiver)|事件响应|捕获系统事件(时区变更，短信到达)或者用户发送到自定义事件，然后根据事件决定接下来的业务逻辑|
|[ContentProvider](#contentprovider)|数据共享|对外安全暴露私有数据，提供统一的api访问，内部可以是不同存储方式的实现|

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
|生命周期|说明|
|---|---|
|onCreate()|首次创建Activity时调用，完成初始化工作，多用于创建视图、为列表绑定数据|
|onStart()|当Activity可见时调用，多用于启动后台任务|
|onResume()|当Activity开始与用户交互时调用，进入活跃状态，位于栈顶，可以接受用户输入|
|onPause()|要启动另一个Activity时调用，暂停当前Activity，但保留状态，可以保存数据，最后一个可控的回调|
|onStop()|当Activity不可见时调用|
|onDestroy()|当Activity销毁时调用，发生在finish()方法执行时，或由于系统内存不足而销毁|
|onRestart()|当Activity重新启动时调用，通常用于恢复数据|

#### Activity保存和恢复
|方法|调用时机|说明|使用场景|
|---|---|---|---|
|onSaveInstanceState(outState:Bundle)|系统未经“你的许可”，可能销毁了你的activity，则会被调用|用户主动销毁不调用，可能销毁即调用|从一个Activity启动另一个Activity <br> 屏幕方向切换 <br> home键返回，切换别的应用 <br> 关闭屏幕显示|
|onRestoreInstanceState(savedInstanceState:Bundle)|系统未经“你的许可”，确实销毁了你的activity，重新启动时则会被调用|用户主动销毁不调用，可能销毁即调用，与onSaveInstanceState()方法不一定成对调用|异常关闭了Activity，下次启动时则被调用 <br> onCreate() -> onStart() -> onRestoreInstanceState() -> onResume()|

#### Activity启动模式
在AndroidManifest.xml中,`<application>`标签下的`<activity>`中设置Activity的启动模式 `android:launchMode="启动模式`
|启动模式|说明|
|---|---|
|standard|默认启动模式，按Activity启动顺序压入Task Stack中|
|singleTop|栈顶复用模式，栈顶Activity实例已经存在，则不会创建新的实例，同一个Activity可以多次创建|
|singleTask|如果Activity存在于栈中，弹出其上所有Activity，目标Activity置于栈顶并获得焦点|
|singleInstance|全局唯一模式，为目标Activity创建一个栈，栈顶Activity获得焦点，如果已经创建过则唤醒|

除了从AndroidManifest.xml中设置启动模式外，也可以在代码中设置启动模式，比如通过Intent的setFlags()方法设置
~~~kotlin
val intent = Intent(this, MainActivity::class.java)
intent.setFlags(Intent.[Intent Flag])
~~~

|Intent Flag|说明|
|---|---|
|FLAG_ACTIVITY_NEW_TASK|从后台服务中启动新的Activity；从通知栏点开新的任务|
|FLAG_ACTIVITY_SINGLE_TOP|点击通知或状态栏，不希望重新创建已经存在的Activity，而是复用现有实例|
|FLAG_ACTIVITY_CLEAR_TOP|导航回已经存在的Activity，并清除其上的所有Activity，如从深层页面返回主页|
|FLAG_ACTIVITY_CLEAR_TASK|完全清空当前任务，并创建一个新任务，比如用户注销返回登录界面|
|FLAG_ACTIVITY_NO_HISTORY|启动一个页面时，不希望用户通过返回键回到该页面|
|FLAG_ACTIVITY_REORDER_TO_FRONT|需要将后台的Activity移到前台而不是重新创建，比如多个步骤的表格填写|

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
|回调方法|说明|
|---|---|
|onAttach()|当Fragment被附加到Activity时调用|
|onCreate()|当Fragment创建时调用|
|onCreateView()|当Fragment创建视图时调用|
|onActivityCreated()|当Fragment的Activity的onCreate()方法执行完毕时调用|
|onStart()|当Fragment可见时调用|
|onResume()|当Fragment获得焦点时调用|
|onPause()|当Fragment失去焦点时或被其他Fragment覆盖时调用|
|onStop()|当Fragment不可见时调用|
|onDestroyView()|当Fragment销毁视图时调用|
|onDestroy()|当Fragment销毁时调用|
|onDetach()|当Fragment被分离时调用|


#### Intent
Intent是Android提供的用来协助组件间的交互和通信机制，描述一次操作的动作和数据

* 显式Intent：指定目标组件，通过Intent对象启动目标组件
* 隐式Intent：不指定目标组件，通过Intent对象启动匹配的组件

##### Intent Flag
|Intent Flag|说明|
|---|---|
|Component|指定来源和目标|
|Action|指定动作行为|
|Category|指定哪种组件来处理|
|Data|uri，指定数据源|
|Type|指定消息数据类型|
|Extras|拓展信息|
|Flags|指定运行模式|

##### IntentFilter 匹配规则

| 条件 | 匹配规则 | 使用 |
| --- | --- | --- |
| Action | 1. `<intent-filter>`中必须包含一个Action, Intent也必须指定一个Action<br>2. `<intent-filter>`中可以有一个或者多个Action, Intent匹配其中的一个Action即可 | `Intent("xxxAction")` |
| Data | 1. `<intent-filter>`中描述了data, 必须在Intent中指定匹配的Data信息<br>2. `<intent-filter>`中没有描述data信息, Intent中可以不指定Data信息<br>3. `<intent-filter>`只指定了mimeType, 默认的URI的scheme是file或者content, 在Intent中必须同时设置mimeType和URI<br>4. `<intent-filter>`中有多个Data, Intent中只需要匹配一组即可 | `intent.setData()` |
| Category | 1. Intent中未指定Category, `<intent-filter>`中也可以不指定Category<br>2. 要隐式启动一个Activity是就必须指定android.intent.category.DEFAULT的Category<br>3. Intent中指定了一个Category时, 会在具有Category的`<intent-filter>`中去找匹配组件 | `intent.addCategory()`<br>`intent.removeCategory()`<br>`intent.categories` |
| 整体匹配规则 | Action+Data+Category同时匹配, 才能启动Activity<br>Activity可以有多个intent-filter, Intent只需匹配任何一组`<intent-filter>`即可启动Activity | - |
| 匹配过程 | 1. 加载`<intent-filter>`列表<br>2. 依次去掉action、Data、Category匹配失败的`<intent-filter>`<br>3. 剩余`<intent-filter>`按优先级排序 | - |


### Service

[回到目录](#目录)

* 无ui，可后台长时间运行
* 启动后，切换应用仍会运行一段时间
* 可用于应用处理网络事务、音频播放、数据同步等
* 默认在主线程[UI线程]运行，除单独指定线程，并且不会单独在进程中运行

#### Service的创建
* 在AndroidManifest.xml中注册Service

| 属性 | 说明 |
| --- | --- |
| android:name | 指定Service的完整类名 |
| android:enabled | 能否被实例化 |
| android:exported | 是否允许其他应用访问 |

~~~xml
<service
android:name=".MyService"
android:enabled="true"
android:exported="true" />
~~~

* 在Kotlin中创建Service

| 方法 | 说明 |
| --- | --- |
| onBind | 当Service被绑定时调用 |
| onCreate | 当Service创建时调用 |
| onStartCommand | 当Service启动时调用 |
| onDestroy | 当Service销毁时调用 |

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
| 启动方式 | 说明 |
| --- | --- |
| startService() | 适用于独立与其他组件长期运行的服务，如播放音乐、下载文件等 需要自身stopService()来停止服务|
| bindService() | 适用于需要与其他组件进行通信的服务，如获取传感器数据、访问数据库等 需要所有绑定的组件调用unbindService()解除绑定才停止服务 |

#### Service的生命周期
| 回调方法 | 说明 |
| --- | --- |
| `onCreate()` | 系统会在服务首次创建时（在 `onStartCommand()` 或 `onBind()` 之前）调用此方法来执行一次性设置过程。如果服务已在运行，不会调用此方法。 |
| `onDestroy()` | 当服务不再使用且被销毁时，系统会调用此方法。用于清理线程、已注册的监听器或接收器等资源。是服务接收的最后一个回调。 |
| `onStartCommand()` | 当其他组件（例如 Activity）请求启动服务时，系统会通过调用 `startService()` 来调用此方法。执行此方法时，系统会启动服务，该服务可以在后台无限期运行。如果实现了此功能，需要在服务完成工作后通过 `stopSelf()` 或 `stopService()` 来停止服务。如果只想提供绑定，无需实现。 |
| `onBind()` | 当其他组件想要与服务绑定（例如执行 RPC）时，系统会通过调用 `bindService()` 来调用此方法。在实现此方法必须提供一个接口，使客户端通过返回 `IBinder` 与服务进行通信。如果不允许绑定，则应返回 `null`。 |
| `onUnbind()` | 当所有绑定的客户端都解绑（通过 `unbindService()`）时，系统会调用此方法。 |


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
| 广播类型 | 方法 | 描述 |
| --- | --- | --- |
| 标准广播 | sendBroadcast(Intent intent) | 异步广播，发送给所有注册的接收器，但无法将处理结果发送给下一个接收器，无法终止广播传播 |
| 有序广播 | sendOrderedBroadcast(Intent intent, String receiverPermission) | 同步广播，同一时刻只有一个接收器收到，可以选择继续传播，拦截或修改广播 |
| 本地广播 | LocalBroadcastManager.registerReceiver(BroadcastReceiver receiver, IntentFilter filter) <br>LocalBroadcastManager.sendBroadcast(Intent intent) <br>LocalBroadcastManager.unregisterReceiver(BroadcastReceiver receiver)| exported=false，只能动态注册，只能发送给本应用，注册为本地广播的接收器无法接受标准广播 |

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


|属性|说明|参数说明|
|---|---|---|
|id|控件的id|@+id/textView: 创建一个id<br>@id/textView: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_margin|控件的外边距|具体数值: 边距大小|
|text|显示的文本|文本内容|
|textAllCaps|是否将文本转换为大写|true: 转换为大写<br>false: 不转换|
|textSize|文本的大小|具体数值: 尺寸大小|
|textColor|文本的颜色|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|textStyle|文本的样式|normal: 正常样式<br>bold: 加粗样式<br>italic: 斜体样式<br>bold_italic: 加粗斜体样式|
|autoLink|是否自动链接文本|web: 自动链接网址<br>email: 自动链接邮箱地址<br>phone: 自动链接电话号码|
|textColorHighlight|文本高亮颜色|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|drawableTop|文本顶部图标|@drawable/icon: 图标资源|
|drawablePadding|图标和文本之间的间距|具体数值: 间距大小|
|gravity|文本的对齐方式|center: 居中对齐<br>center_vertical: 垂直居中对齐<br>center_horizontal: 水平居中对齐<br>left: 左对齐<br>right: 右对齐<br>top: 顶部对齐<br>bottom: 底部对齐|
|letterSpacing|字符间距|具体数值: 间距大小|
|lineSpacingExtra|行间距|具体数值: 间距大小|
|maxLines|文本的最大行数|具体数值: 行数|
|ellipsize|文本超出显示范围时如何显示|start: 显示开头省略号<br>middle: 显示中间省略号<br>end: 显示末尾省略号<br>marquee: 滚动显示|

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

|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/button: 创建一个id<br>@id/button: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_margin|控件的外边距|具体数值: 边距大小|
|text|显示的文本|文本内容|
|background|按钮的背景|@drawable/button_background: 背景资源|
|padding|按钮的内边距|具体数值: 内边距大小|
|gravity|按钮的文本对齐方式|center: 居中对齐<br>center_vertical: 垂直居中对齐<br>center_horizontal: 水平居中对齐<br>left: 左对齐<br>right: 右对齐<br>top: 顶部对齐<br>bottom: 底部对齐|
|contentDescription|`ImageButton`按钮的描述|文本内容|
|src|`ImageButton`按钮的图标|@drawable/icon: 图标资源|
|drawableStart|按钮的图标|@drawable/icon: 图标资源|
|drawablePadding|图标和文本之间的间距|具体数值: 间距大小|
|enable|按钮是否可用|true: 可用<br>false: 不可用|
|clickable|按钮是否可点击|true: 可点击<br>false: 不可点击|

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

|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/editText: 创建一个id<br>@id/editText: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_margin|控件的外边距|具体数值: 边距大小|
|hint|输入框的提示文本|文本内容|
|inputType|输入框的类型|text: 文本输入框<br>number: 数字输入框<br>phone: 电话输入框<br>datetime: 日期时间输入框<br>email: 邮箱输入框<br>textMultiLine: 多行文本输入框<br>textCapSentences: 首字母大写输入框<br>textCapWords: 单词首字母大写输入框<br>textCapCharacters: 全字母大写输入框<br>textNoSuggestions: 无建议输入框<br>textPassword: 密码输入框<br>textVisiblePassword: 可见密码输入框<br>textMultiLine: 多行文本输入框<br>textAutoCorrect: 自动修正输入框<br>textAutoComplete: 自动完成输入框<br>textWebEditText: 网页输入框<br>textUri: URI输入框<br>textEmailAddress: 邮箱地址输入框<br>textPostalAddress: 邮政编码输入框<br>textPersonName: 人名输入框<br>textShortMessage: 短消息输入框<br>textLongMessage: 长消息输入框<br>textFilter: 过滤输入框<br>textPhonetic: 音标输入框<br>textWebEmailAddress: 网页邮箱地址输入框<br>textWebPassword: 网页密码输入框|
|maxLenght|输入框的最大长度|具体数值: 最大长度|
|cursorVisible|输入框的游标是否可见|true: 可见<br>false: 不可见|
|textColor|输入框的文本颜色|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|textColorHint|输入框的提示文本颜色|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|textColorHighlight|输入框的文本高亮颜色|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|digits|输入框只能输入的字符|具体数值: 输入内容|
|imeOptions|输入框的键盘(回车)完成按钮|done: 完成按钮<br>go: 跳转按钮<br>next: 下一个按钮<br>search: 搜索按钮<br>send: 发送按钮|


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

|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/imageView: 创建一个id<br>@id/imageView: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_margin|控件的外边距|具体数值: 边距大小|
|adjustViewBounds|图片是否适应控件|true: 适应<br>false: 不适应|
|contentDescription|图片的描述|文本内容|
|maxHeight|图片的最大高度|具体数值: 最大高度|
|maxWidth|图片的最大宽度|具体数值: 最大宽度|
|src|图片的资源|@drawable/image: 图片资源<br>@mipmap/image: 图片资源|
|background|图片的背景|@color/colorPrimary: 颜色资源<br>#RRGGBB: 十六进制颜色值|
|padding|图片的内边距|具体数值: 内边距大小|
|cropToPadding|图片是否剪切内边距|true: 剪切<br>false: 不剪切|
|scaleType|图片的缩放类型|fitXY: 填充父布局<br>fitStart: 适应内容并居左<br>fitCenter: 适应内容并居中<br>fitEnd: 适应内容并居右<br>center: 中心<br>centerCrop: 剪切并居中<br>centerInside: 适应内容并居中|

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
|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/progressBar: 创建一个id<br>@id/progressBar: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 固定大小|
|layout_margin|控件的外边距|具体数值: 边距大小|
|style|进度条样式|?android:attr/progressBarStyleHorizontal: 水平直条样式<br>？android:attr/progressBarStyleSmall: 小尺寸圆形样式<br>？android:attr/progressBarStyleLarge: 大尺寸圆形样式<br>？android:attr/progressBarStyleInverse: 反向样式<br>？android:attr/progressBarStyleSmallInverse: 小尺寸反向样式<br>？android:attr/progressBarStyleLargeInverse: 大尺寸反向样式|
|max|进度条的最大值|具体数值: 最大值|
|progress|进度条的当前进度|具体数值: 当前进度|
|secondaryProgress|进度条的当前进度|具体数值: 当前进度|
|indeterminate|进度条是否为不确定状态|true: 不确定<br>false: 确定|
|indeterminateDrawable|进度条的不确定状态的图片|@drawable/image: 图片资源<br>@mipmap/image: 图片资源|

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

|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/scrollView: 创建一个id<br>@id/scrollView: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局|
|fillViewport|滚动视图是否填充父布局|true: 填充<br>false: 不填充|
|scrollbars|滚动条样式|none: 无<br>horizontal: 水平滚动条<br>vertical: 垂直滚动条|
|scrollbarSize|滚动条大小|具体数值: 滚动条大小|
|scrollbarFadeDuration|滚动条淡出时间|具体数值: 淡出时间|
|layout_weight|控件的权重|具体数值: 权重大小|
|scrollbarStyle|滚动条样式|insideOverlay: 覆盖在内容上方<br>insideInset: 嵌入内容内<br>outsideOverlay: 覆盖在内容上方<br>outsideInset: 嵌入内容内|
|requiresFadingEdge|需要淡出边缘效果|vertical: 垂直<br>horizontal: 水平|
|fadingEdgeLength|淡出边缘长度|具体数值: 淡出边缘长度|
|overScrollMode|滚动模式|never: 不允许滚动<br>always: 允许滚动<br>ifContentScrolls: 内容滚动时允许滚动|

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
|组件|说明|
|----|----|
|Adapter|数据与视图绑定，创建ViewHolder，绑定数据到视图|
|ViewHolder|缓存视图引用，存储item布局中的控件引用|
|LayoutManager|定义布局方式，如垂直或水平，以及如何排列item|
|ItemDecoration|定义item的装饰，如分割线，边框，背景颜色等|
|ItemAnimator|定义item的动画，如插入，删除，移动，改变大小等|

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

|属性|说明|参数说明|
|----|----|----|
|id|控件的id|@+id/recyclerView: 创建一个id<br>@id/recyclerView: 引用一个id|
|layout_width|控件的宽度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局|
|layout_height|控件的高度|match_parent: 填充父布局<br>wrap_content: 适应内容<br>具体数值: 填充父布局|

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
| 属性 | 描述 | 参数说明 |
| --- | --- | --- |
| android:layout_width | 视图宽度 | match_parent:匹配父视图宽度<br>wrap_content:包裹内容宽度<br>具体数值:具体数值 |
| android:layout_height | 视图高度 | match_parent:匹配父视图高度<br>wrap_content:包裹内容高度<br>具体数值:具体数值 |
| android:layout_gravity | 视图对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐<br>可以通过竖杠连接多个值 |
| android:background | 视图背景 | 颜色值:颜色值<br>图片:图片路径 |
| android:foreground | 视图前景 | 颜色值:颜色值<br>图片:图片路径 |
| android:foregroundGravity | 视图前景对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐 |

#### 子视图排列属性

子视图默认排列在FrameLayout的左上角，且允许堆叠
最终位置为对其方式并加上边距值后的位置

| 属性 | 描述 | 参数说明 |
| --- | --- | --- |
| android:layout_gravity | 子视图对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>top:顶部对齐<br>bottom:底部对齐 |
| android:layout_marginStart | 子视图左边距 | 单位:dp |
| android:layout_marginEnd | 子视图右边距 | 单位:dp |
| android:layout_marginTop | 子视图上边距 | 单位:dp |
| android:layout_marginBottom | 子视图下边距 | 单位:dp |

### LinearLayout

核心优势在于方向性排列和权重分配，适合构建列表、表单等结构性界面，但嵌套或频繁使用layout_weight属性可能会导致性能问题。

#### 自身属性
| 属性 | 描述 | 参数说明 |
| --- | --- | --- |
| **android:orientation** | 排列方向 | horizontal：水平排列<br>vertical：垂直排列 |
| android:layout_width | 宽度 | match_parent：填充父布局<br>wrap_content：内容自适应<br>具体数值 |
| android:layout_height | 高度 | match_parent：填充父布局<br>wrap_content：内容自适应<br>具体数值 |
| android:background | 背景 | 颜色值 |
| android:gravity | 子视图对齐方式 | center：居中<br>center_vertical：垂直居中<br>center_horizontal：水平居中<br>left：左对齐<br>right：右对齐<br>top：上对齐<br>bottom：下对齐 |
| android:layout_gravity | 子视图在父容器对齐方式 | center：居中<br>center_vertical：垂直居中<br>center_horizontal：水平居中<br>left：左对齐<br>right：右对齐<br>top：上对齐<br>bottom：下对齐 |
| android:layout_marginTop | 设置子视图与父容器顶部的距离 | 单位：dp |
| android:layout_marginBottom | 设置子视图与父容器底部的距离 | 单位：dp |
| android:layout_marginLeft | 设置子视图与父容器左侧的距离 | 单位：dp |
| android:layout_marginRight | 设置子视图与父容器右侧的距离 | 单位：dp |
| ***android:divider*** | 设置ListView的分割线 | drawable资源文件 |
| android:showDividers | 设置ListView的分割线显示位置 | none：不显示；beginning：开头；middle：中间；end：结尾 |
| **android:weightSum** | 设置LinearLayout的权重和 | float类型 |

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
| 属性 | 描述 | 参数说明 |
| --- | --- | --- |
| android:layout_gravity | 设置子视图在父视图中的对齐方式 | left:左对齐<br>right:右对齐<br>center:居中对齐<br>fill:填充父视图 |
| android:layout_weight | 设置子视图在父视图中的权重 | 根据父视图的weightSum值来分配空间，默认weightSum为4，**需要将layout_width或layout_height设置为0dp** |

### RelativeLayout

[回到目录](#目录)

是基于相对位置关系的布局容器，通过定义子视图相对于父容器或同级视图的位置来组织界面元素

#### 子视图属性

1. 对齐父容器边

| 描述 | 属性 | 参数说明 |
| --- | --- | --- |
| android:layout_alignParentStart | 设置视图与父容器起始边对齐 | true:启用左对齐<br>false:禁用左对齐 |
| android:layout_alignParentEnd | 设置视图与父容器结束边对齐 | true:启用右对齐<br>false:禁用右对齐 |
| android:layout_alignParentTop | 设置视图与父容器顶部对齐 | true:启用顶部对齐<br>false:禁用顶部对齐 |
| android:layout_alignParentBottom | 设置视图与父容器底部对齐 | true:启用底部对齐<br>false:禁用底部对齐 |

2. 方向位置

| 描述 | 属性 | 参数说明 |
| --- | --- | --- |
| android:layout_toRightOf/ android:layout_toEndOf | 设置视图相对于某个视图的右侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_toLeftOf/ android:layout_toStartOf | 设置视图相对于某个视图的左侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_below | 设置视图相对于某个视图的底部对齐 | @+id/viewId:指定某个视图的id |
| android:layout_above | 设置视图相对于某个视图的顶部对齐 | @+id/viewId:指定某个视图的id |

3. 边缘对齐

| 描述 | 属性 | 参数说明 |
| --- | --- | --- |
| android:layout_alignTop | 设置视图与某个视图的顶部对齐 | @+id/viewId:指定某个视图的id |
| android:layout_alignBottom | 设置视图与某个视图的底部对齐 | @+id/viewId:指定某个视图的id |
| android:layout_alignLeft | 设置视图与某个视图的左侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_alignRight | 设置视图与某个视图的右侧对齐 | @+id/viewId:指定某个视图的id |
| android:layout_baseline | 设置视图的基线与指定视图的基线对齐 | @+id/viewId:指定某个视图的id |

4. 居中控制

| 描述 | 属性 | 参数说明 |
| --- | --- | --- |
| android:layout_centerInParent | 设置视图在父容器中居中显示 | true:启用居中显示<br>false:禁用居中显示 |
| layout_centerHorizontal | 垂直居中 | true:启用垂直居中<br>false:禁用垂直居中 |
| layout_centerVertical | 水平居中 | true:启用水平居中<br>false:禁用水平居中 |

### ConstraintLayout

[回到目录](#目录)




