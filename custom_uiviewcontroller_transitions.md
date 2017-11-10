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
模态弹出转场动画的发生有下面几个步骤：
	
1. 你通过代码或者故事版的 *segue* 出发转场动画。
2. UIKit获取目标视图控制器（将要展示的那个视图控制器）的**transitioningDelegate**属性。如果目标视图控制器没有定义这个属性，UIKit就会用内建的默认动画。
3. 接着UIKit会调用 **Transitioning Delegate** 的 **animationController(forPresented:presenting:source:)** 方法来创建一个 **Animation Controller** 对象。如果这个方法没有成功创建 **Animation Controller** 对象，那么UIKit还是会用默认动画。
4. UIKit创建 **Transitioning Context** 对象。
5. UIKit调用 **Animation Controller** 对象的 **transitionDuration(using:)** 方法获取整个转场动画的时间（duration）。
6. UIKit调用 **Animation Controller** 对象的 **animateTransition(using:)** 方法开始动画。
7. 最后，在动画结束后 **Animation Controller** 会调用 **Transitioning Context** 的 **completeTransition(_:)** 方法来结束动画。

模态关闭的转场动画步骤跟模态弹出的动画步骤相似。在模态关闭动画中，UIKit调用源视图控制器（将要被关闭的视图控制器）来获取 **Transitioning Delegate** 对象。然后 **Transitioning Delegate** 通过调用 **animationController(forDismissed:)** 方法完成模态关闭的动画。

## 开始创建一个自定义模态弹出动画
现在开始实践一下上面的理论。下面开始实现以下动画：

* 当用户点击一个卡片，它会翻转+所有目标视图控制器的view跟卡片一样大小。
* 翻转结束后，目标视图控制器的view放到到跟屏幕一样大小。

### 创建动画
现在开始创建 **Animation Controller** 对象。
首先创建一个类继承 **NSObject**，命名为 **FlipPresentAnimationController**。
**Animation Controller** 对象必须实现 **UIViewControllerAnimatedTransitioning** 协议，打开刚创建的文件修改如下:

```
class FlipPresentAnimationController: NSObject, UIViewControllerAnimatedTransitioning {

}
```
这时候Xcode报错点击 **Fix** 自动添加必须要实现的方法:

![](https://koenig-media.raywenderlich.com/uploads/2017/10/fix-it.png)

模态弹出动画的开始值是从你点击的那个视图（View）开始，在刚添加的类中添加下面代码，用来存储动画的开始位置：

```
private let originFrame: CGRect

init(originFrame: CGRect) {
  self.originFrame = originFrame
}
```

接下来你要实现刚添加的 **UIViewControllerAnimatedTransitioning** 协议里所要求的方法。在 **transitionDuration(using:)** 方法中添加下面的代码：

```
func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
  return 2.0
}
```

跟方法名一样，上面实现的方法指定转场动画的时间长度（duration）。

添加下面代码到 **animateTransition(using:)** 中

```
// 1
guard let fromVC = transitionContext.viewController(forKey: .from),
  let toVC = transitionContext.viewController(forKey: .to),
  let snapshot = toVC.view.snapshotView(afterScreenUpdates: true)
  else {
    return
}

// 2
let containerView = transitionContext.containerView
let finalFrame = transitionContext.finalFrame(for: toVC)

// 3
snapshot.frame = originFrame
snapshot.layer.cornerRadius = CardViewController.cardCornerRadius
snapshot.layer.masksToBounds = true
```

上面代码做的事情：

1. 获取目标视图控制器和源控制器对象。创建一个动画结束后要展示的屏幕快照。
2. UIKit会把所有转场动画和涉及到的view放到一个 container view 中来统一管理。先从 **Transitioning Context** 中获取 **container view** 和确定新view最后的大小。
3. 设置要展示的屏幕快照的大小和圆角等绘图设置，让他完全覆盖你点击的那个卡片视图（view）；

向 **animateTransition(using:)** 方法继续添加下面方法：

```
// 1
containerView.addSubview(toVC.view)
containerView.addSubview(snapshot)
toVC.view.isHidden = true

// 2
AnimationHelper.perspectiveTransform(for: containerView)
snapshot.layer.transform = AnimationHelper.yRotation(.pi / 2)
// 3
let duration = transitionDuration(using: transitionContext)
```
系统创建的 **container view** 只包含源视图（"from" view）,如果要完成转场动画你需要添加一些其他的视图到 **containerView** 上，这里有个细节要注意：通过 **addSubview(_:)** 添加的的视图（view）会放在所有 **containerView** 的最上面。
接下来解释一下上面代码：

1. 添加目标视图控制器的view到 **container view** 上并且隐藏它，再把目标视图控制器的快照截图添加到 **container view** 上。
2. 设置 *动画对象* 的初始状态，围绕Y轴旋转90度。这会让 **container view** 在动画开始的时候是不可见的。
3. 获取动画的时长。

```
**AnimationHelper** 是一个小的工具类，帮助我们添加旋转动画到View上。
```
现在已经完成了所有必要的配置，现在只剩下实现动画方法了：

```
// 1
UIView.animateKeyframes(
  withDuration: duration,
  delay: 0,
  options: .calculationModeCubic,
  animations: {
    // 2
    UIView.addKeyframe(withRelativeStartTime: 0.0, relativeDuration: 1/3) {
      fromVC.view.layer.transform = AnimationHelper.yRotation(-.pi / 2)
    }
    
    // 3
    UIView.addKeyframe(withRelativeStartTime: 1/3, relativeDuration: 1/3) {
      snapshot.layer.transform = AnimationHelper.yRotation(0.0)
    }
    
    // 4
    UIView.addKeyframe(withRelativeStartTime: 2/3, relativeDuration: 1/3) {
      snapshot.frame = finalFrame
      snapshot.layer.cornerRadius = 0
    }
},
  // 5
  completion: { _ in
    toVC.view.isHidden = false
    snapshot.removeFromSuperview()
    fromVC.view.layer.transform = CATransform3DIdentity
    transitionContext.completeTransition(!transitionContext.transitionWasCancelled)
})
```
代码解释：

1. 调用UIView的 关键帧动画。动画的时间要跟转场动画的时间一样长。
2. 先沿着Y轴旋转目标视图控制器的view 90度把自己隐藏起来。
3. 把目标视图控制器的快照旋转回原来的位置。
4. 把目标视图控制器的快照放大到填满整个屏幕。
5. 现在目标视图控制器的快照完全覆盖了目标视图控制器的view，现在可以去除快照，展示源视图控制器的view了。然后恢复源视图控制器的状态（因为源视图控制器的view是在动画结束后隐藏状态，如果不恢复源视图控制器的状态，等模态关闭的时候，源视图控制器的view是看不到的）。最后调用 **completeTransition(_:)** 方法通知UIKit动画完成,这会让UIkit保存最后的状态，并且从 **container view** 上移除 源视图控制器的view。

现在 **Animation Controller** 的定义已经完成了。

### 让动画动起来

UIKit还需要一个 **Transitioning Delegate** 对象来调用 **Animation Controller** 来实现最后的转场动画。再继续，你要创建一个实现 **UIViewControllerTransitioningDelegate** 的对象。在这个例子中，**CardViewController** 可以作为一个 **Transitioning Delegate** 对象。

打开 **CardViewController.swift** 文件，在文件最后添加一个实现类拓展的方法。

```
extension CardViewController: UIViewControllerTransitioningDelegate {
  func animationController(forPresented presented: UIViewController,
                           presenting: UIViewController,
                           source: UIViewController)
    -> UIViewControllerAnimatedTransitioning? {
    return FlipPresentAnimationController(originFrame: cardView.frame)
  }
}
```
上面代码返回一个用与被点击卡片相同的frame初始化自定义的 **Animation Controller** 实例。

最后一步是设置 **CardViewController** 类为 转场动画的代理。 没个视图控制器都有一个 **transitioningDelegate** 属性，这个属性会被UIKit调用，来确定是否需要使用自定义转场动画。

添加下面代码到 **prepare(for:sender:)** 方法的最后面：

```
destinationViewController.transitioningDelegate = self
```

It’s important to note that it is the view controller being presented that is asked for a transitioning delegate, not the view controller doing the presenting!

构建运行项目。点击任意卡片，会有下面效果：

![](https://koenig-media.raywenderlich.com/uploads/2015/07/frontflip-slow.gif)

