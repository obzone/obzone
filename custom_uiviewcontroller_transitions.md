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

## 瞅一瞅转场动画的API

转场动画的API是一组协议，然后你自己根据你的app需求来选择是用已经提供的对象或者创建你自己的对象来管理转场动画。在这章的最后你将了解这些协议的是干什么用的以及他们之间的关系。下面的图展示了这些API的组成部分：

![](https://koenig-media.raywenderlich.com/uploads/2015/07/parts.001.jpg)

### API说明

等你了解了这些部分是如何共同工作的，你就发现这些组成部分其实很简单。

#### Transitioning Delegate
每一个视图控制器都会可以有一个 **transitioningDelegate** 的属性（iOS7开始）,这个属性对象遵从 **UIViewControllerTransitioningDelegate** 协议。
当你模态弹出或者模态关闭一个视图控制器的时候，UIKit 会从 **transitioningDelegate** 对象中获取 **Animation Controller**(参照上图) 来进行动画。如果要自定义动画，你就必须要实现一个 **Transitioning delegate**（参照上个图），这个对象返回一个 **Animation Controller**(参照上图)。

#### Animation Controller
