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
|[Activity](#activity)|界面交互|是用户交互的入口点，代表一个界面，可以拉起其他的界面和组件|
|Service|后台服务|处理耗时任务(即使应用退出)，比如下载，网络请求，跨进程访问等|
|BroadcastReceiver|事件响应|捕获系统事件(时区变更，短信到达)或者用户发送到自定义事件，然后根据事件决定接下来的业务逻辑|
|ContentProvider|数据共享|对外安全暴露私有数据，提供统一的api访问，内部可以是不同存储方式的实现|

### 入口文件
AndroidManifest.xml

### Activity

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
