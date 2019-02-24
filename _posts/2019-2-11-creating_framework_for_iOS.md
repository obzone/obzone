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

选择 **KnobShowcase** 项目的根目录打开项目编辑目录：选择 **KnobShowcase** 编译目标，然后选择 **General** 标签页。

滚动到 **Embedded Binaries** 栏。把 **KnobControl.framework** 从 **KnobControl.xcodeproj** 的 **Products** 文件夹拖到当前栏目中。

现在你已经添加了静态库的入口到 **Embedded Binaries** 和 **Linked Frameworks and Binaries** 中。

    Note：至于**Embedded Binaries** 和 **Linked Frameworks and Binaries**分别是干什么的，请参考下面文章： https://stackoverflow.com/questions/32375687/what-is-the-difference-between-embedded-binaries-and-linked-frameworks

到 **ViewController.swift** 中在文件最上面添加 

    import KnobControl

目前为止仍然有报错的问题存在，这是因为swift的 **access control** 机制问题，默认情况下，Swift会让所有东西都是 `internal` 或者仅在模块内部可见。

为了解决这个问题，我们要修改 `Knob` 类的访问权限。

先介绍一下 Swift 的五个权限级别：

* **Open and public**：可以被app调用或者其他 frameworks 调用。
* **Internal**：可以被静态库内部调用。
* **Fileprivate**：文件内部调用。
* **Private**：在封闭的生命中调用，e.g. 比如 class 内部。

现在我们需要将 **Knob** 变成 public 的

Note：如果你想深入了解访问机制，open 和 public 的区别，请查看下面文档 [Access Control Documentation](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html)

# 更新权限

打开 **Knob.swift** 修改类生命的权限修饰符：

    public class Knob : UIControl {

现在 `Knob` 可以被所有引入 `KnobControl` 框架的app可见了

现在添加 `public` 修饰符到：

* **minimumValue**，**maximumValue**，**value**，**isContinuous**，**lineWidth****，startAngle**, **endAngle**, **pointerLength**, **color** 属性。
* **Init** 函数。
* **setValue(_:animated:)** 和 **tintColorDidChange()** 方法。

构建运行项目，error消失，但是有新的崩溃问题出现：

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-crash.png)

因为把 `Knob` 类放到静态库了，所有丢失了类和storyboard的引用：

1. 打开 **KnobShowcase** 中的 **Main.Storyboard**。
2. 在 **Document outline** 中，选择 **Knob** 类。
3. 在 **Identity inspector** 的 **Custom Class** 模块中修改 **Module** 中的值为 **KnobControl**，如下图。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-storyboard-1.png)

构建并运行，现在一切正常。

# Interface Builder 的实时渲染(额外优化)

打开 **Main.storyboard**。看到方块里是空白的，如果你看不到蓝色的边框，打开 **Editor** > **Canvas** > **Show Bounds Rectangles**。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-blank.png)

如果想要让引用你 静态库 的人能够通过代码或者XIB的方式修改 knob后，能实时显示效果，你还需要多一步去优化 **Live Views**。

打开 **Knob.swift** 在 `Knob` 声明前加上 `@IBDesignable` 修饰符：

    @IBDesignable public class Knob: UIControl {

这个修饰符可以让 XIB 开启 实时渲染模式。

打开 **Main.storyboard** 此时还是空白的蓝框，没有什么变化。

别急！苹果还提供了一个 `prepareForInterfaceBuilder()` 方法，这个方法只会在 Xib 渲染是调用。

打开 `Knob.swift` 文件，添加下面代码：

    extension Knob {
        public override func prepareForInterfaceBuilder() {
            super.prepareForInterfaceBuilder()

            renderer.updateBounds(bounds)
        }
    }

打开 **Main.storyboard**，同时确保 **Editor** > **Automatically Refresh Views** 是打开的，现在就能看到方框里有东西了：

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-live.png)

# 创建一个对应的 CocoaPod

Cocoapods 也是以一个静态库的形式包含在app中。

# 清除现有依赖

先把 **KnobControl** 项目从 **KnobShowcase** 中清除掉：

1. 把 **KnobControl.xcodeproj** 从项目目录中删掉。
2. 在确认框中，选择删除引用选项。

# 创建 Pod

打开命令行，切换到 `KnobControl` 目录下，然后执行下面命令：

    pod spec create KnobControl

执行后，会在当前目录下创建一个 **KnobControl.podspec** 目录，这是一个描述项目的模版文件，包含很多通用描述和默认设置。

1. 修改 `Spec Metadata` 组：

    s.name         = "KnobControl"
    s.version      = "1.0.0"
    s.summary      = "A knob control like the UISlider, but in a circular form."
    s.description  = "The knob control is a completely customizable widget that can be used in any iOS app. It also plays a little victory fanfare."
    s.homepage     = "http://raywenderlich.com"

2. 修改 `Spec License` 组：

    s.license      = "MIT"

3. 你可以根据自己的情况修改 `Author Metadata` 组的信息。

4. 修改 `Platform Specifics` 组：

    s.platform     = :ios, "12.0"

5. 修改 `Source Location` 组（真正发布的时候，这里通常是 gitbuh 的项目地址）：

    s.source       = { :path => '.' }

6. 修改 `Source Code` 组：

    s.source_files = "KnobControl"

7. 在 `end` 上面最后一行添加：

    s.swift_version = "4.2" 

8. 解开所有 `#` 注释

到现在你已经完成了开发版的 Podspec

    Note：如果此时你用 `pod spec lint` 命令去检测 `Podspec` 此时会报错说 `source` 不是一个有效的URL，但是这不影响本地开发，等后面发布到github之后，这个问题就会消失。

# 使用 Pod

命令行进入 **KnobShowcase** 目录下，执行下面命令：

    pod init

执行之后，会悄悄生成一个 **Podfile** 文件，文件中会列出所有项目中用到的三方库。

打开 **Podfile** 文件，修改内容如下：

    platform :ios, '12.0'

    target 'KnobShowcase' do
    use_frameworks!

    pod 'KnobControl', :path => '../KnobControl'

    end

    # Workaround for Cocoapods issue #7606
    post_install do |installer|
        installer.pods_project.build_configurations.each do |config|
            config.build_settings.delete('CODE_SIGNING_ALLOWED')
            config.build_settings.delete('CODE_SIGNING_REQUIRED')
        end
    end

保存之后，在命令行中执行：

    pod install

这一步会安装或者更新项目中的依赖包。

最后会生成一个 **KnobShowcase.xcworkspace** 文件，之后我们要用这个文件打开项目。

打开 **KnobShowcase.xcworkspace** 文件。

Note：当执行 `pod install` 之后，你可能会收到如下警告![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-warnings-2.png)
，我们需要选择 **KnobShowcase** 根目录，然后选择 **KnobShowcase** 编译目标。选择 **Build Settings** 标签页，在搜索目录中找 **ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES**。选择之后，在弹出框中选择 **Other…** 然后修改内容为：`$(inherited)`
重新执行 `pod install` 此时警告消失。

# Pod 的组织

看一下 `Pod` 项目，能看到两个targets：

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-pods-targets.png)

* **Pods-KnobShowcase**：pod 项目，他会把所有独立的pods构建成他自己的静态库，最终合并成一个。
* **KnobControl**：

在项目的目录下，能看到很多分组，**KnobControl** 在 **Development Pods** 目录下。因为你定义这个pod的时候用了 `:path` 指向 应用的 Podfile。你可以直接修改 **KnobControl** 中的代码。

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-pods-structure.png)

但是修改在 **Pods** 文件夹中的项目之后，下次重新安装项目，之前的项目就会复原。

# 发布 Pod

这一部分，会把你的Pod发布到github上，让它像其他三方项目那样使用。

## 创建一个仓库

如果还没有账号，来[这里注册](https://github.com/join)

像下面这样创建一个 GitHub 仓库

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-new-repo-1.png)

点击 **Create repository** 按钮创建一个仓库。

## 克隆仓库

在 **KnobShowcase** 目录下创建一个 `repo` 文件夹，并且进入文件夹：

    mkdir repo && cd repo

把刚刚创建的 GitHub 仓库克隆到当前文件夹下面：

    git clone URL

正常会有如下输出：

    Cloning into 'KnobControl'...
    remote: Counting objects: 4, done.
    remote: Compressing objects: 100% (4/4), done.
    remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
    Unpacking objects: 100% (4/4), done.

## 添加代码到仓库

接下里把 **KnobControl** 文件夹下的所有内容复制到 **repo/KnobControl** 文件夹下。

打开 **KnobControl.podspec** 文件，修改 `s.source` 内容如下：

    s.source       = { :git => "URL", :tag => "1.0.0" }

把 `URL` 换成你的 GitHub 仓库的地址。

## 把内容提交到 GitHub

    cd KnobControl/
    git add .
    git commit -m "Initial commit"
    git push -u origin master

提交成功之后到 GitHub 下面可以看到：

![](https://koenig-media.raywenderlich.com/uploads/2018/05/how-to-create-a-framework-ios-first-create-framework-repo-files.png)

## 打标签

打开 **KnobControl.podspec** 文件，设置版本如下：

    s.version      = "1.0.0"

同时标记仓库版本，执行下面命令：

    git tag 1.0.0
    git push --tags

检查 podfile：

    pod spec lint

# 更新 Podfile

修改文件中的

    pod 'KnobControl', :path => '../KnobControl'

为

    pod 'KnobControl', :git => 'URL', :tag => '1.0.0'

修改 `URL` 为你Github仓库的地址

然后在命令行中执行

    pod update

完成！