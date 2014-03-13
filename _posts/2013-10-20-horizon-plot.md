---
layout: post
categories: datavis
title: Horizon Chart - 惊艳到爆的哲学
---

对于Horizon Chart，可能大家还都比较陌生。
先来看一个比较经典的

![](/assets/images/horizon-intro/qSF60Mo.png)

我们在日后的ggplot2的教程里会详细讲解这样一个Horizon Chart 是如何做出来的。

但是大家应该和我差不多，第一次看到这个图的时候感觉是虽然看不太懂，但是觉得很绚丽。

时髦的词叫作“不明觉厉”。

想要做出一个漂亮的Horizon Chart 第一点就是要看懂它

这个商业做Horizon Chart 的软件里，介绍了它的作图方法 ：

原文在：[这里](http://www.stonesc.com/Vis08_Workshop/DVD/Reijner_submission.pdf)

Horizon Chart主要应用在时间序列的数据里，尤其在金融，物流等等领域可以说应用非常广泛。

原理比较简单：

**1** 

首先有一个最传统的折线图 

![](/assets/images/horizon-intro/ZexplPb.png)

**2**

分别用不同的颜色标记正值和负值，同时在一个色调里用颜色深浅来表明绝对值的大小

![](/assets/images/horizon-intro/uL1yC0Q.png) 

**3**

取负值的镜像，都画在正向的坐标里。这样一来就可以省去不少空间。

![](/assets/images/horizon-intro/p0O7zku.png)

**4**

![](/assets/images/horizon-intro/SDsgxgQ.png)

按照图层的不同，在几个不同的图层再次重叠到一起，这样可以再次节省不少空间。

**5**

![](/assets/images/horizon-intro/6GoZEHS.png)

当同时看不同类别时间序列的变化时，这种可视化的方法就显得非常有效。易懂信息量也够大。

比如上图，可以轻松看到50种股票价格的变化趋势。

关于Horizon Plot的具体实现方法，主要是ggplot2和LatticeExtra，

在这篇博客中，我们会讲到horizon plot 的具体实现方法： [Go](http://yangchao.me/2013/10/horizon-lattice-extra)

相关文章：


- [The Development of the Horizon Graph](http://www.stonesc.com/Vis08_Workshop/DVD/Reijner_submission.pdf)

- [Time on the Horizon](http://www.perceptualedge.com/articles/visual_business_intelligence/time_on_the_horizon.pdf)

- [Sizing the Horizon: The Effects of Chart Size and Layering 
on the Graphical Perception of Time Series Visualizations](http://vis.berkeley.edu/papers/horizon/2009-TimeSeries-CHI.pdf) 






