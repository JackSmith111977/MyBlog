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

## 核心架构图解

### AIDL 通信流程图

~~~mermaid
sequenceDiagram
    participant Client as 客户端进程 (App)
    participant Proxy as Proxy (本地代理)
    participant Binder as Binder 驱动 (内核层)
    participant Stub as Stub (服务端桩)
    participant Service as 服务端进程 (Service)

    Note over Client, Proxy: 看起来像调用本地方法
    Client->>Proxy: calling method(args)
    activate Proxy
    
    Note over Proxy, Binder: 数据序列化 (Marshalling)
    Proxy->>Binder: transact()
    deactivate Proxy
    activate Binder
    
    Note over Binder, Stub: 跨进程传输
    Binder->>Stub: onTransact()
    deactivate Binder
    activate Stub
    
    Note over Stub, Service: 数据反序列化 (Unmarshalling)
    Stub->>Service: calling real method(args)
    activate Service
    
    Service-->>Stub: return result
    deactivate Service
    
    Stub-->>Binder: reply data
    deactivate Stub
    activate Binder
    
    Binder-->>Proxy: reply data
    deactivate Binder
    activate Proxy
    
    Proxy-->>Client: return result
    deactivate Proxy
~~~
* **Client (客户端)**：调用方，想要使用远程服务的功能。

* **Proxy (代理)**：运行在客户端进程，它把客户端的请求把数据包装好（序列化），扔给 Binder。

* **Stub (桩)**：运行在服务端进程，它是一个抽象类（Binder 的子类）。它从 Binder 接收数据，拆包（反序列化），然后调用服务端真正的实现方法。

* **Service (服务端)**：实际干活的地方。

## AIDL 支持的数据类型
在 .aidl 文件中，你只能使用以下类型。如果使用自定义类型（如 Book 对象），必须实现 Parcelable 接口。

1. **Java 基本数据类型** (int, long, boolean, float, double, char, byte, short)
2. **String 和 CharSequence**
3. **List**：只支持 ArrayList，且里面的元素必须也是 AIDL 支持的。
4. **Map**：只支持 HashMap，且里面的元素必须也是 AIDL 支持的。
5. **Parcelable 实现类**：自定义对象必须实现此接口。
6. **AIDL 接口本身**：所有的 AIDL 接口本身也可以作为参数。
