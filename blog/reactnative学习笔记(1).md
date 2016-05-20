<!--
author: 滇西之王
head: http://q.qlogo.cn/qqapp/100229475/C96DA226D9D07DECADAA54A78E9FEDF9/100
date: 2016-05-20
title: React-Native学习笔记(1)
tags: react,js
category: react-native,app
status: publish
summary: 本文是自己react-native学习笔记的记录
-->
#### React-Native的安装
React-Native基于npm包安装，但是由于国外网络的原因，我们可以将npm仓库源替换为国内镜像：
``` 
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```
####**安装react-native**
```
npm install -g react-native-cli
react-native init AwesomeProject //创建AwesomeProject项目
```
####**运行iOS应用**
- 在ios中项目中，进入AwesomeProject目录，输入命令`react-native run-ios`
-  或者在Xcode中打开`/AwesomeProject/ios/AwesomeProject.xcodeproj` ，然后单击`运行按钮`
####**运行Android应用**
- cd AwesomeProject
- react-native run-android
- 使用喜欢的文本编辑器打开`index.android.js` 并随便改上几行
- 按Menu键（在Genymotion模拟器中是`⌘+M`）然后选择*Reload JS*就可以看到我的最新修改。
- 在终端下运行`adb logcat *:S ReactNative:V ReactNativeJS:V`可以看到你的应用的日志。
#### 开发
`react-native init`命令会创建一个指定名字的应用，
#### reactNative的好处
使用React Native，你可以使用标准的平台组件，例如iOS的UITabBar或安卓的Drawer。 这使你的app获得平台一致的视觉效果和体验，并且获得最佳的性能和流畅性。 使用对应的React component，就可以轻松地把这些原生组件整合到你的React Native应用中， 例如TabBarIOS和DrawerLayoutAndroid。
#### 异步执行
在JavaScript代码和原生平台之间的所有操作是异步执行的，并且原生模块还可以根据需要创建新的线程。这意味着我们可以在主线程解码图片，然后在后台将它保存到磁盘，或者在不堵塞UI的情况下计算文字大小和界面布局等等。





