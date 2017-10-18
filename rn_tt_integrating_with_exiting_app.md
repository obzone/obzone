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

```
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

```

这段代码列出了 RN 所需要的依赖包和 RN 启动的脚本，接下来在 **控制台** 中运行： ` npm install ` 

执行完成，你会在当前文件夹下面看到一个新的 **node_modules** 文件夹，里面有 *React* 和 *React Native* 两个模块。*React Native* 包含所有跟原生app交互的代码。接下来用 **cocoapod** 安装原生部分的依赖包。