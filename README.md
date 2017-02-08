# Zxing项目介绍之移植篇 #
目前二维码扫描功能在app上已经非常成熟了，而zxing项目也是目前可以说是最流行的二维码扫描方面的开源项目了，今天介绍下，怎么把zxing移植到我们android工程项目，最近做这方面的工作，发现网上这方面的介绍比较少，今天整理一篇。
[zxing github网址](https://github.com/zxing/zxing)
github项目代码很多，刚开始看的时候我看得有点懵逼，其实移植到android上只需要三个目录
![](https://github.com/TChengZ/ScanDemo/blob/master/png/zxing%E7%9B%AE%E5%BD%95.png)

上面用红色圈圈画出的三部分有需要，android目录是要导入到工程的源码，android-core和core两个目录我们需要编译成jar，因为里面的一些类在android中有用到。

## 工程导入 ##
 我这里用的是Android studio，首先把zxing github的工程download下来，然后在自己的工程中选择import module，选择zxing项目里的android，module名字改成zxing，成功之后变这样
 
 ![](https://github.com/TChengZ/ScanDemo/blob/master/png/%E5%AF%BC%E5%85%A5%E6%88%90%E5%8A%9F.png)

## jar编译 ##
 运行之后发现各种红色的Error，找不到类，这些找不到的类都在zxing项目中，我们分别对zxing项目里的android-core, core目录里的源码进行maven编译，[具体编译方式看这里](http://jingyan.baidu.com/article/37bce2be153dc91003f3a257.html)，然后我们就得到两个jar包，放到libs目录下，add as library，然后所有的error就消失了，这个时候会发现我们app的名称跟图标貌似被改了，这个小问题，要改动的话把zxing项目里的Androidmainfiest的appicon跟strings.xml里的app_name改成自己想要的。

## 修改成library ##
虽然上面我们导入了zxing并且顺利编译通过，但我们还需要要把zxing改成一个lib的module，并在主工程引用改lib module。
先要修改下zxing的build.gradle，把第一行的apply plugin
改成
```
apply plugin: 'com.android.library'
```
并删除applicationId，然后gradle 同步下就发现zxing变lib module了，这个时候还会有些Error，提醒需要常量表达式
![](https://github.com/TChengZ/ScanDemo/blob/master/png/%E5%B8%B8%E9%87%8F%E8%A1%A8%E8%BE%BE%E5%BC%8F%E9%94%99%E8%AF%AF.png)
解决方法很简单就是把switch改成if else。。。。

你可能还会遇到引入的zxing设置的minSdkVersion跟原来的项目不兼容，把zxing的minSdkVersion改成和主工程一样即可。

在主工程的build.gradle里面增加dependecies

```
compile project(':zxing')
```
并删除zxing里面Androidmainfiest对CaptureActivity的启动intent-filter。
到此为止，整个zxing项目就导入到我们的工程了，然后你可以针对自己的功能删除不必要的代码，我删除了大部分代码只留下的简单的扫描功能，[Demo地址](https://github.com/TChengZ/ScanDemo)。
