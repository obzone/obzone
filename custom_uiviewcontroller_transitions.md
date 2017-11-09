# 自定义 UIViewController 的转场动画
iOS 自带一些转场动画：push，pop，cover vertically(模态弹出动画),当然我们也可以自定义。
自定义转场动画能带来非常好的用户体验，让你的app与众不同。同事自定义转场动画并没有想象的那么难。
在接下来的教程里，你讲跟随教程完成一个猜动物的游戏的app，然后学会下面技能：

* 了解转场动画的API。
* 如果在模态弹出和模态关闭 ViewController 的时候运用转场动画。
* 做一个响应式的转场动画。

**注意：**
这一章的转场动画需要一点 UIView 动画的知识，如果你不是很了解UIView动画的知识，可以先通过 [iOS Animation](https://www.raywenderlich.com/146603/) 了解一下。

## 开始
先下载 [模版项目](https://koenig-media.raywenderlich.com/uploads/2017/10/GuessThePet-starter-1.zip)。运行一下，大概就是下面这个样子:

![](https://koenig-media.raywenderlich.com/uploads/2015/07/starter.gif)

接下来就是优化这个模版项目了。

## 转场动画的API概述

转场动画的API是一组协议，然后你自己根据你的app需求来选择是用已经提供的对象或者创建你自己的对象来管理转场动画。在这章的最后你将了解这些协议的是干什么用的以及他们之间的关系。下面的图展示了这些API的组成部分：

![](https://koenig-media.raywenderlich.com/uploads/2015/07/parts.001.jpg)

### API说明

等你了解了这些部分是如何共同工作的，你就发现这些组成部分其实很简单。

#### Transitioning Delegate
每一个视图控制器都会可以有一个 **transitioningDelegate** 的属性（iOS7开始）,这个属性对象遵从 **UIViewControllerTransitioningDelegate** 协议。
当你模态弹出或者模态关闭一个视图控制器的时候，UIKit 会从 **transitioningDelegate** 对象中获取 **Animation Controller**(参照上图) 来进行动画。如果要自定义动画，你就必须要实现一个 **Transitioning delegate**（参照上个图），这个对象返回一个 **Animation Controller**(参照上图)。

#### Animation Controller
**Animation Controller** 由 **Transitioning Delegate** 创建并且实现了 **UIViewControllerAnimatedTransitioning** 协议。**Animation Controller** 是转场动画的核心。

#### Transitioning Context
**Transitioning Context** 对象实现 **UIViewControllerContextTransitioning** 协议，**Transitioning Context** 在转场动画实现中也非常重要。它包含了在转场动画中用到的所有 view 和 view controller 的信息。
在转场动画的实现中，你不用自己实现这个协议。UIKit会在每次动画发生的时候创建并初始化好 **Transitioning Context** 对象并且会传给你的 **Animation Controller** 对象。

## 转场动画流程
* 模态弹出转场动画的发生有下面几个步骤：
	
	1.你通过代码或者故事版的 *segue* 出发转场动画。
	
	2.UIKit获取目标视图控制器（将要展示的那个视图控制器）的**transitioningDelegate**属性。如果目标视图控制器没有定义这个属性，UIKit就会用内建的默认动画。
	
	3.接着UIKit会调用 **Transitioning Delegate** 的 **animationController(forPresented:presenting:source:)** 方法来创建一个 **Animation Controller** 对象。如果这个方法没有成功创建 **Animation Controller** 对象，那么UIKit还是会用默认动画。
	
	4.UIKit创建 **Transitioning Context** 对象。
	
	5.UIKit调用 **Animation Controller** 对象的 **transitionDuration(using:)** 方法获取整个转场动画的时间（duration）。
	
	6.UIKit调用 **Animation Controller** 对象的 **animateTransition(using:)** 方法开始动画。
	
	7.最后，在动画结束后 **Animation Controller** 会调用 **Transitioning Context** 的 **completeTransition(_:)** 方法来结束动画。

模态关闭的转场动画步骤跟模态弹出的动画步骤相似。在模态关闭动画中，UIKit调用源视图控制器（将要被关闭的视图控制器）来获取 **Transitioning Delegate** 对象。然后 **Transitioning Delegate** 通过调用 **animationController(forDismissed:)** 方法完成模态关闭的动画。

## 开始创建一个自定义模态弹出动画
现在开始实践一下上面的理论。下面开始实现以下动画：

* 当用户点击一个卡片，它会翻转+所有目标视图控制器的view跟卡片一样大小。
* 翻转结束后，目标视图控制器的view放到到跟屏幕一样大小。

### 创建动画
现在开始创建 **Animation Controller** 对象