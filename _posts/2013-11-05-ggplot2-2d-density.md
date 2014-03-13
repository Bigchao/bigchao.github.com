---
layout: post
categories: datavis
title: ggplot2 - 二维数据密度图
---

地理课上，我们经常会看到等高线。在ggplot2中，也有实现一个和等高线差不多的利器


- **stat_density2d()**


看个例子先
    
{% highlight r %}

	# The base plot
	p <- ggplot(faithful, aes(x=eruptions, y=waiting))
	p + geom_point() + stat_density2d()

{% endhighlight %}

![](/assets/images/ggplot2-density/nE3VPUs.png)

我们可以在原始图中添加类似登高线的表示，用以标记密集程度。

{% highlight r %}

	# Contour lines, with "height" mapped to color
	p + stat_density2d(aes(colour=..level..))

{% endhighlight %}

![](/assets/images/ggplot2-density/nghFH1z.png)

{% highlight r %}

	# Map density estimate to fill color
	p + stat_density2d(aes(fill=..density..), geom="raster", contour=FALSE)

{% endhighlight %}

![](/assets/images/ggplot2-density/bnGh3DC.png)

{% highlight r %}

	# With points, and map density estimate to alpha
	p + geom_point() +
    stat_density2d(aes(alpha=..density..), geom="tile", contour=FALSE)

{% endhighlight %}

![](/assets/images/ggplot2-density/j1b0kxp.png)

同一维的密度曲线一样，stat_density2d也可以调整binwidth用h()函数即可
体会下下面的这个例子

{% highlight r %}

	p + stat_density2d(aes(fill=..density..), geom="raster", contour=FALSE, h=c(.5,5))

{% endhighlight %}

![](/assets/images/ggplot2-density/aDAgAtw.png)



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1sw5uP)
