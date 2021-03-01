---
title: Remote debug with Java Instrumentation
date: 2020-05-19 13:13:32
tags: Java
---

最近在线上问题排障时，发现了内部框架实现的一个非常有用的功能，可以对生产机器上的代码实现对运行类的动态埋点（类似于断点，但并不会阻塞应用运行），并获取断点处的栈帧。

翻看源码学习了下实现思路。

# 基本原理

利用 Java Agent 技术，通过 Java Instrumentation 接口编写 Agent 。JVM 启动时加载 Agent 。

# 加载 Agent

1. 实现 Agent 启动方法 `premain` 或 `agentmain`

2. Agent 打成一个 jar 包，并在 MANIFEST.MF 中指定

3. JVM 启动参数添加 `"-javaagent:xxx.jar"` 加载 Agent

# 断点注册

实现一个 http 接口，接受断点相关的信息。

调用 Instrumentation.retransformClasses 将断点所在的类重新加载。

实现 ClassFileTransformer 的 transform 方法，在断点处添加记录栈帧的代码。

通过 Instrumentation.addTransformer 添加到实现的 ClassFileTransformer

# 获取栈帧信息

实现一个 http 接口，返回断点处的栈帧。

删除断点，并将记录的栈帧数据返回。
