---
layout: post
title: 为iOS开发framework静态库
---

在这篇文章中，你将学到如何为iOS创建一个framework静态，从而实现代码的复用、模块化功能、或者提供三方类库。[原文地址](https://www.raywenderlich.com/5109-creating-a-framework-for-ios)

# 静态库有三个主要的功能

* 代码封装
* 模块化代码
* 代码重用

在 Swift 的语法中, **模块** 是一组编译之后的代码组，*framework* 是模块的一种。

下面的教程中，你会用到另一篇教程 [How To Make a Custom Control Tutorial: A Reusable Knob](https://www.raywenderlich.com/?p=190768)。

# 开始

下载上面教程的代码 **KnobShowcase** 并运行

![](https://koenig-media.raywenderlich.com/uploads/2018/04/final_navigation.gif)

整个代码分为两个部分

* **Knob.swift**包含所有视图逻辑
* **ViewController.swift**负责创建视图

# 静态库

如果你之前用过其他语言，你可能听说过 *node modules*，*packegs*，*gems* 或者 *jars*。Frameworks 就是类似的东西。iOS SDK里一些类似像 **Foundation**，**UIKit**，**AVFoundation**，**CloudKit**。

**Note** 如果想学习更多关于 Framework，可以阅读 [What are Framework](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)

# 开始

在 Xcode 6中苹果已经介绍过带 **access control** 的 **Cocoa Touch Framework**，所以创建 framework 变得很简单。首先要创建一个 framework 可以依赖的项目。

1. 在Xcode中，选择 **File** > **New** > **Project...**
2. 选择 **iOS** > **Framework & Library** > **Cocoa Touxh Framework**

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-framework.png)

3. 点击 **Next**
4. 设置 **Product Name** 为 **KnobControl**。设置自己的 **Organization Name** 和 **Organization Identifier**。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-create.png)

5. 点击 **Next**
6. 在文件选择器中，把项目常见到与 **KnobShowcase** 相同的根目录下

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-3.png)

7. 点击 **Create**

# 添加代码

现在添加的框架是没有代码的，从 **KnobShowcase** 源代码目录下把 **Knob.swift** 拖到 **KnobControl** 中，要选中 **Copy items if needed** 选项，这样文件就从旧目录下复制到新项目中了，而不是只添加了文件的引用(framework是需要自己的稳健的，而不是引用，这样才能作为一个独立的模块)。

![](https://koenig-media.raywenderlich.com/uploads/2018/06/how-to-create-a-framework-ios-first-create-framework-copy.gif)

最后检查一下 **Knob.swift** 文件的 **Target Membership** 是不是在 **KnobControl** 项目下。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/target-membership.png)

**Build** 一下项目，看看是否报错。

如果你已经读过 [How to Make a Custom Control](https://www.raywenderlich.com/?p=190768) 这篇教程，你应该知道 **Knob.swift** 包含三个类。

`Knob`：自定义控制器

`KnobRenderer`：一个私有类，负责渲染 knob

`RotationGestureRecognizer`：一个私有类，负责响应 knob

接下来的工作是分离这些组件到不同的文件中，先从`KnobRenderer`开始：

1. 选择 **File** > **New** > **File…** 选择 **iOS** > **Source** > **Swift File**。
2. 点击 **Next**。
3. 给文件命名为 **KnobRenderer** 然后选择 **KnobControl** > **KnobControl** 文件夹。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/KnobRendererCreation.png)

4. 点击 **Create**。
5. 打开 **Knob.swift**，把 `KnobRenderer` 类的所有内容剪切下来(Command+X)，然后黏贴到 **KnobRenderer.swift** 文件的末尾。

6. 去掉 `KnobRenderer` 类的 `private` 修饰符。

像上面6个步骤一样，把 **RotationGestureRecognizer** 也进行这样的修改。在第5步的时候记得引入 `import UIKit.UIGestureRecognizerSubclass`。

现在 **Knob.swift** 文件只包含 `Knob` 类了。

    Note：把文件分离到多个文件中，并不是必须的，但是它可以更好的组织你的项目，并且让项目更好理解和维护。

# 把静态库加入到项目中

在 **KnobShowcase** 项目中把 **Knob.swift** 文件删掉。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-delete.png)

右键项目目录根结点的 **KnobShowcase**，点击 **Add Files to “KnobShowcase”**。在文件选择器中，选择 **KnobControl.xcodeproj**。点击 **Add** 把 **KnobControl.xcodeproj** 添加为一个子项目。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/add_knobcontrol_project.png)

    Note：把 framework添加到项目中，不是必须的，你可以只添加 **KnobControl.framework** 编译后的.a文件。

    但是添加项目的好处是，可以同步的开发项目和静态库，你在修改静态库的时候，结果会直接影响到app。也让Xcode在修改静态库之后，能马上编译项目。

到此为止，项目中还会有一个报错

![](https://koenig-media.raywenderlich.com/uploads/2018/05/Screen-Shot-2018-05-27-at-12.12.57-PM.png)

虽然现在两个项目在一起了，但是 **KnobShowcase** 仍然找不到 **KnobControl** 项目。

这时需要我们连接静态库到项目中。首先，打开 **KnobControl** 项目的 **Products** 目录，
会看到 **KnobControl.framework** 文件，这个文件是框架的编译文件，包括头文件、资源、和metadata文件。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/Screen-Shot-2018-05-27-at-3.13.54-PM.png)

