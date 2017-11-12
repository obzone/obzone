---
layout: post
title: react 的组件基类
---
#组件
**组件**让你可以模块化界面，同时更方便模块的重用

##概览

*React.Component* 是一个抽象基类，你需要继承它，并必须实现一个 **render()**方法  

通常组件回单独定义成一个javascript类：

	class Greeting extends React.Component {
		 render() {
 	 	 return <h1>Hello, {this.props.name}</h1>;
 	 }
	}
##组建的生命周期
你可以重写组件的生命周期函数来在合适的时机运行你的逻辑代码。可以重写的周期函数中以**will**开头的方法会在某些功能之前执行，以**did**开头的函数会在某些功能执行后执行。
###挂载
当组件被创建并且被添加到DOM上这个过程会依次执行以下函数：

* [constructor()](https://facebook.github.io/react/docs/react-component.html#constructor)
* [componentWillMount()](https://facebook.github.io/react/docs/react-component.html#componentwillmount)
* [render()](https://facebook.github.io/react/docs/react-component.html#render)
* [componentDidMount()](https://facebook.github.io/react/docs/react-component.html#componentdidmount)

###更新
组件的更新在它的 ***props*** 和 ***state*** 属性被改变时触发。下面这些函数在组件重新渲染时候调用：

* [componentWillReceiveProps()](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops)
* [shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)
* [componentWillUpdate()](https://facebook.github.io/react/docs/react-component.html#componentwillupdate)
* [render()](https://facebook.github.io/react/docs/react-component.html#render)
* [componentDidUpdate()](https://facebook.github.io/react/docs/react-component.html#componentdidupdate)

### 卸载
当一个组件从DOM上移除时会触发：

* [componentWillUnmount()](https://facebook.github.io/react/docs/react-component.html#componentwillunmount)

###其他接口
每个组件还提供以下API：

* [setState](https://facebook.github.io/react/docs/react-component.html#setstate)
* [forceUpdate](https://facebook.github.io/react/docs/react-component.html#forceupdate)

###类属性

* [defaultProps](https://facebook.github.io/react/docs/react-component.html#defaultprops)
* [displayName](https://facebook.github.io/react/docs/react-component.html#displayname)

###实例属性

* [props](https://facebook.github.io/react/docs/react-component.html#props)
* [state](https://facebook.github.io/react/docs/react-component.html#state)

---
##参考
**render()**

	render()
*render()* 方法是没个组件必须要实现的方法。
当被调用时需要在方法中检查 *this.props* 和 *this.state* 属性，最终返回一个react元素。最终返回的元素可以是原始DOM元素，就像 `<div />`，或者其他之前定义过的组件的组合。
当在 *render()* 方法中返回**null**或者**false**表示你不想渲染这个标签，并且后续代码中调用 **ReactDOM.findDOMNode(this)** 方法会返回 null。

**render()**