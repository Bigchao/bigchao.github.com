---
layout: post
categories: datavis
title: ggplot2 - 作图哲学
---


在真正开始GGPLOT2的旅程之前，从亲身感受我觉得有必要跟大家分享下，其实真正的数据可视化也好，机器学习，数据挖掘也好，一半的功夫都在做数据清理，和预处理。而这方面在本系列中涉及不多，但有的关于其他拓展包的语法如，Reshape，Pylr包等等，我会在注释中写清楚。
这方面我比较喜欢用SQL，Python，和R （按喜爱顺序排列），个人不同爱好啦。

如果你现在一头雾水，没什么任何R基础，请看这个懒人速速起步，对于大多数人来说是够用了。

本章主要介绍有关条形图在GGPLOT2中的实现，代码来自
[R Graphics Cookbook](http://www.amazon.com/R-Graphics-Cookbook-Winston-Chang/dp/1449316956) 

ggplot2基本要素

- 数据（Data）和映射（Mapping）
- 几何对象（Geometric）
- 标尺（Scale）
- 统计变换（Statistics）
- 坐标系统（Coordinante）
- 图层（Layer）
- 分面（Facet）
- 主题（Theme）

我的理解是一张图，我们把数据映射到图层上
比如散点图我们除了x，y轴表示了不同的变量之后，还可以用颜色表示第三个变量，用点的大小表示第四个变量，当然也可以设置散点的形状，这就是第五个变量了。

这几个要素中，可以任意变换任意参数。

更加详细的如果有兴趣可以看[这里](http://ygc.name/stats/ggplot2.html)

需要安装部分拓展包除了ggplot2，还包括gcookbook（数据集），plyr，reshape等等。

接下来的教程中，我会选取非常有代表性的图形来给大家做详细讲解，但是最重要的思想大家这里要体会下。



