---
layout: post
title: 单元测测试和UI测试
---

#ios 单元测测试和UI测试

---

此教程会向你展示如何用xcode的test引导来帮助你测试你app的数据模型和异步方法。以及模拟假的一些假的操作，来与系统交互。

##测试的定义

一个项目的测试内容应该涵盖以下内容：

* 核心功能：数据模型和方法，以及它们和 *controller* 的交互
* UI 流程
* 边界检查
* bug 修复

##首先的首先
下面是一些单元测试的基本原则：

* 快速的（运行速度）。
* 独立的 测试不应该执行 **setup** **teardown**。
* 每次执行要有相同的结果，同时外在的数据提供端要能产生失败的结果。
* 测试应该是完全自动化的，输出应该只有成功或者失败。
* 测试应该是在程序开始前就要写好

##开始

先下载[示例代码](https://koenig-media.raywenderlich.com/uploads/2016/12/Starters.zip),里面包括两部分 **BullsEye** 和 **HalfTunes**
**BullsEye** 继承自 [**iOS Apprentice**](https://www.raywenderlich.com/store/ios-apprentice) 应用


## 假的测试数据和模拟交互

这部分讲 你从网络接收到数据的测试 和 **userdefaults**、**CloudKit** 的操作正确性测试。

