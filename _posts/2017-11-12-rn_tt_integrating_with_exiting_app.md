---
layout: post
title: 在已有项目中添加React Native支持
---

# 在已有项目中添加React Native支持
 
*React Native* 让你可以用 *React* 的那套东西编写原生APP，通过 RN 你可以汲取网页开发的一些优点创建响应式app，比如不需要编译。

你是不是想要了解在不替换全部app的情况下，让 RN 集成到现有的项目中？ 这篇教程将会引导你把 RN 集成到一个已有的swift项目中。你将会完成一个叫做 ‘Mixer‘的项目，这是一个展示 Mixer 信息，并且你可以在上面对这些事件进行评分。

## 开始
下载 [基础工程](https://koenig-media.raywenderlich.com/uploads/2016/09/Mixer-Starter-3.zip) 并解压。运行这个项目，先简单了解一下这个项目：
app首页是一个列表，展示所有Mixers，点击每一个会跳转到详情页面，在详情页点击 添加评分，就可以对mixer进行评分，但你现在是一个空页面，这部分通过后面的教程用 RN 来实现。

![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-starter-overview.png)

## 添加一个 RN 的view组件
先关掉项目。在开会 RN 之前，我们需要对 RN 进行一下配置。

* 安装 cocoapod
* 安装 node
* 安装 watchman

如果上面这些环境都安装成功，现在就可以安装 RN 所需的依赖包了。
在 **控制台** 中切换工作目录到基础工程项目的 **js** 文件夹下面，然后创建一个 **package.json** 文件，并且在在文件中添加如下内容：
	
	{
	  "name": "mixer",
	  "version": "1.0.0",
	  "private": true,
	  "description": "Mixer",
	  "scripts": {
	    "start": "node node_modules/react-native/local-cli/cli.js start"
	  },
	  "dependencies": {
	    "react": "~15.3.1",
	    "react-native": "~0.34.0"
	  }
	}

这段代码列出了 RN 所需要的依赖包和 RN 启动的脚本，接下来在 **控制台** 中运行： ` npm install ` 

执行完成，你会在当前文件夹下面看到一个新的 **node_modules** 文件夹，里面有 *React* 和 *React Native* 两个模块。*React Native* 包含所有跟原生app交互的代码。接下来用 **cocoapod** 安装原生部分的依赖包。

将控制台的工作目录切换到 *iOS* 文件夹下面然后创建一个名称为 **Podfile** 的文件，然后在里面添加如下内容： 
	
	use_frameworks!
	target 'Mixer'
	pod 'React', :path => '../js/node_modules/react-native', :subspecs => [
	  'Core',
	  'RCTImage',
	  'RCTNetwork',
	  'RCTText',
	  'RCTWebSocket',
	]
	
上面这段配置文件指定了要从 [**React Podspec**](https://github.com/facebook/react-native/blob/master/React.podspec) 仓库中加载的库。当前配置文件让你可以调用 views，text，images等组件。

接下来在命令行执行 ``pod install`` 安装上面配置的依赖包。

此时控制台的输入应该像下面这样：

	Analyzing dependencies
	Fetching podspec for `React` from `../js/node_modules/react-native`
	Downloading dependencies
	Installing React (0.34.0)
	Generating Pods project
	Integrating client project
	
	[!] Please close any current Xcode sessions and use `Mixer.xcworkspace` for this project from now on.
	Pod installation complete! There are 5 dependencies from the Podfile and 1 total pod installed.
	
安装完成后在当前工作目录下面会有一个 **Mixer.xcworkspace** 文件，打开然后运行看看是不是正常。

![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-home-2.png)

## 创建一个单页面

在 *js* 目录下 创建一个名称为 **index.io.js** 的文件，添加如下内容

	'use strict';
	// 1
	import React from 'react';
	import ReactNative, {
	  AppRegistry,
	  StyleSheet,
	  Text,
	  View,
	} from 'react-native';
	
	// 2
	const styles = StyleSheet.create({
	  container: {
	    flex: 1,
	    justifyContent: 'center',
	    alignItems: 'center',
	    backgroundColor: 'green',
	  },
	  welcome: {
	    fontSize: 20,
	    color: 'white',
	  },
	});
	
	// 3
	class AddRatingApp extends React.Component {
	  render() {
	    return (
	      <View style={styles.container}>
	        <Text style={styles.welcome}>We're live from React Native!!!</Text>
	      </View>
	    )
	  }
	}
	
	// 4
	AppRegistry.registerComponent('AddRatingApp', () => AddRatingApp);
	
介绍一下上面代码

* 首先加载 *react* 和 *react-native* 模块， [结构赋值](http://es6.ruanyifeng.com/#docs/destructuring)帮你重命名这些方法，省去了在调用这些方法时候的 **React/ReactNative** 前缀。
* 第二部定义了用来布局UI的css样式。
* 接下来在 *render()* 方法中定义了一个 **AddRatingApp** 组件，用来展示欢迎文字。
* 注册 **AddRatingApp** 组件为应用的根视图。

在 Xcode 中打开  **AddRatingViewController.swift** 文件，然后添加

``import React`` 

接下来定义一个作为 **React Native** 主视图的UI对象。

``var addRatingView: RCTRootView!``

然后在 **viewDidLoad()** 方法后面添加

	addRatingView = RCTRootView(
	    bundleURL: URL(string: "http://localhost:8081/index.ios.bundle?platform=ios"),
	    moduleName: "AddRatingApp",
	    initialProperties: nil,
	    launchOptions: nil)
	self.view.addSubview(addRatingView)

上面这段代码通过 *APP* 的 **Bundle** 中依据 **AddRatingApp** 模块名初始化一个 **RCTRootView** 实例。

最后在 **viewDidLayoutSubviews** 方法中添加下面代码来设置 *rootview* 的大小布局。

``addRatingView.frame = self.view.bounds``

因为苹果从iOS9开始禁用http协议下面对iOS项目进行相关设置。
在 Xcode中，打开 **Info.plist** 文件然后进行下面操作：

* 添加 **NSAppTransportSecurity** 作为一个 **Dictionary** 的key
* 然后在 **NSAppTransportSecurity** 下面再添加一个 **NSExceptionDomains** key value也用 **Dictionary** 类型。
* 再在 **NSExceptionDomains** 下面添加一个 **localhost** key，value 同样为 **Dictionary** 类型。
* 然后在 **localhost** 下添加一个 **NSTemporaryExceptionAllowsInsecureHTTPLoads** key，value为YES

当完成上面的操作，大概是下面这个样子：

![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-info-plist-1.png)

在控制台中，工作目录切换到 js 文件夹下，执行下面命令来启动 React Native 服务

``npm start`` 

控制台的输入应该是下面这样：
![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-npm-start.png)
	
	Note: If you receive an error stating that none of the files are listed in global config root_files then run the following command to initialize Git: git init.
	If you still see the same error, run the following command to initialize a watchman configuration file in the js directory: echo "{}" > .watchmanconfig：

运行程序，点击一个mixer,然后在详情页点击 **Add Rating** ，如果一切正常，你会看到如下欢迎页面：

![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-bare-bones-react-native-app.png)

第一次打开 app packager 加载 bundle 的时候可能会慢一点。

## 开始正式的 RN 开发

在 RN 项目中原生代码和js通过 [JavaScriptCore Framework](https://developer.apple.com/library/tvos/documentation/Carbon/Reference/WebKit_JavaScriptCore_Ref/index.html) 框架进行交互。

![](https://koenig-media.raywenderlich.com/uploads/2016/06/mixer-theory-bridge-3.png)

