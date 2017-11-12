---
layout: post
title: Core Data 钩子函数配置细节 (fetched properties 属性)
---

##coredata 关联关系

####弱关系（fetched properties）

*fetched properties* 表示一种弱、单向的关联关系。在雇员和部门关系中，*fetched property* 关系可以用来表示部门最近雇用的员工的关系。员工不需要一个跟部门有一个反向的关系来表示自己是部门最近雇用的关系。
通常 *fetched properties* 关系最适合管理一些松散的，临时的关系。
*fetched property* 有点像 *relationship* ，但是又有区别：

 * 并不是一个直接的关联关系，*fetched property* 属性的值是通过 *fetchedrequest* 搜索来的。
 * *fetched property* 返回 NSArray类型，而不是 NSSet类型。在设置 *fetchrequest* 的时候可以添加排序限制。
 * *fetched property* 会被懒加载，在查询过后会被缓存下来。
 
下面演示 添加 *fetched property* 属性：
![](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreData/Art/Fetched_Property_2x.png)

有些情况下 *fetched property* 会与智能播放列表比较相似，但是有一点不同是，他不是动态的。如果目标对象变过，你必须重新计算 *fetched property*属性，以保证数据的准确。你可以使用 `refreshObject:mergeChanges:` 手动去更新*fetched property*属性，这个方法会让属性关联的查询语句再次执行，当这个对象的fault状态被fired（即获取这个属性,e.g .这个属性）。

在*fetched property*中你可以用两个特别的查询限制谓词，`$FETCH_SOURCE` 和 `$FETCHED_PROPERTY`

