---
title: React Native 踩坑之路
date: 2018-04-19 23:03:24
tags: React Native
---

#### 1 build  Ordered comparison between pointer 报错

```
将报错提示的地方
 if (_total > 0) {

 修改成

 if ([_total integerValue] > 0) {

```

#### 2  红屏报错 React Native Version Mismatch
```
Close all terminals and run build again.
```

#### 3 iOS 图标 启动页生成

启动页--> 需要清空 Lanch Screen File   
[说明链接](https://www.jianshu.com/p/0b312627a376)   
MAC 可安装APP ICon Gear 


#### 4 项目新增字体
[流程文档](https://medium.com/react-native-training/adding-custom-fonts-to-react-native-b266b41bff7f)  


#### 5 红屏报错 this is either due to a require() error during initialization or failure。。。

这个错误的根本原因是根目录./index.ios.js中
```
AppRegistry.registerComponent('项目名',() => ...);
```
与./ios/项目名/appDelegate.m中的
```
RCTRootView*rootView = [[RCTRootViewalloc]initWithBundleURL:jsCodeLocation

moduleName:@"项目名" launchOptions:launchOptions];
```
或是./android/app/src/main/java/com/项目名/MainActivity.java中的
```
mReactRootView.startReactApplication(mReactInstanceManager, "项目名", null);
```
没有保持一致，解决方法很简单。编辑成相同的参数即可。

#### 6 红屏报错 undefined is not an object (evaluating '_react2.PropTypes.string') 问题

```
npm install prop-types --save

对所引用的组件原 。import React, {Component，PropTypes} from 'react'

改成：import React, {Component} from 'react' 和   import { PropTypes} from 'prop-types';

注：从React15.5起，React.PropTypes被移入到单独的package中。react提供了一个package(prop-types)去检查props的类型。首先需要将prop-types引用到文件中。 

```