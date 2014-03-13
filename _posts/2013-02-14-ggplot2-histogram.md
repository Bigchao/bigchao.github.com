---
layout: post
categories: datavis
title: ggplot2 - Histogram
---

**基本问题**：想做直方图

- **geom_histogram**

**Basic Plot**

{% highlight r %}

    library(ggplot2)
    library(gcookbook)
	ggplot(faithful, aes(x=waiting)) + geom_histogram()

{% endhighlight %}

![](/assets/images/ggplot2-histogram/wMSbdnr.png)

geom_histogram可以对数据框和单一向量做处理，如果想快速看看一个变量的分布是什么样子的可以用下面的代码：

{% highlight r %}

	# Store the values in a simple vector
	w <- faithful$waiting
	ggplot(NULL, aes(x=w)) + geom_histogram()

{% endhighlight %}

即把数据框位置设为NULL即可。

---

**延伸问题1**: 改变直方图中柱子的个数。
默认中，默认的bin个数是30。
- geom_histogram(binwidth)中修改

{% highlight r %}

	# Divide the x range into 15 bins
	binsize <- diff(range(faithful$waiting))/15
	ggplot(faithful, aes(x=waiting)) +
      geom_histogram(binwidth=binsize, fill="white", colour="black")

{% endhighlight %}

![](/assets/images/ggplot2-histogram/wh9T1UM.png)

---

**延伸问题2**： 多变量的直方图

如果想观察多变量直方图

- facet_grid()
- map the grouping variable to fill

**1.facet_grid**

{% highlight r %}

	library(MASS) # For the data set
	# Use smoke as the faceting variable
	ggplot(birthwt, aes(x=bwt)) + geom_histogram(fill="white", colour="black") +
      facet_grid(smoke ~ .)

{% endhighlight %}

![](/assets/images/ggplot2-histogram/DXoLuNH.png)

注意这里有一个小问题，就是Y轴的坐标上限都是一样的。有时会出现如果不同类别的变量绝对值相差很大，图看起来就不够清爽。

- facet_grid(,scales="free")来修改

{% highlight r %}

	ggplot(birthwt, aes(x=bwt)) + geom_histogram(fill="white", colour="black") +
      facet_grid(race ~ ., scales="free")

{% endhighlight %}

![](/assets/images/ggplot2-histogram/2pU3Wiv.png)

**2.map the grouping variable to fill**

把变量放在aes（,fill=)中

{% highlight r %}

	birthwt1 <-birthwt # Make a copy of the data
	# Convert smoke to a factor
	birthwt1$smoke <- factor(birthwt1$smoke)
	# Map smoke to fill, make the bars NOT stacked, and make them semitransparent
	ggplot(birthwt1, aes(x=bwt, fill=smoke)) +
      geom_histogram(position="identity", alpha=0.4)

{% endhighlight %}

![](/assets/images/ggplot2-histogram/wn5suUY.png)

---
**延伸问题3**： 为直方图上加密度曲线

- geom_density()
- geom_line(stat="density")

首先看看geom_density()的用法

{% highlight r %}

	ggplot(faithful, aes(x=waiting)) + geom_density()

{% endhighlight %}

![](/assets/images/ggplot2-histogram/fp2Ng9T.png)

但是发现在侧边和底部也有黑框包围，不好看。这时候就用到了geom_line()

{% highlight r %}

	# The expand_limits() increases the y range to include the value 0
	ggplot(faithful, aes(x=waiting)) + geom_line(stat="density") +
      expand_limits(y=0)

{% endhighlight %}

![](/assets/images/ggplot2-histogram/VG10TJ6.png)

但是援引原书中原句

> A kernel density curve is an estimate of the population distribution, based on the sample data. The amount of smoothing depends on the kernel bandwidth: the larger the band‐width, the more smoothing there is. The bandwidth can be set with the adjust parameter, which has a default value of 1.

感受如下代码

{% highlight r %}

	ggplot(faithful, aes(x=waiting)) +
    geom_line(stat="density", adjust=.25, colour="red") +
    geom_line(stat="density") +
    geom_line(stat="density", adjust=2, colour="blue")

{% endhighlight %}

![](/assets/images/ggplot2-histogram/NMIwC4u.png)

我们发现adjust的值越大，曲线就越平滑。
回到最基本的延伸问题：如果想把曲线加到直方图上
- geom_histogram() + geom_density
就可以啦

{% highlight r %}

	ggplot(faithful, aes(x=waiting, y=..density..)) +
      geom_histogram(fill="cornsilk", colour="grey60", size=.2) +
      geom_density() +
      xlim(35, 105)

{% endhighlight %}

![](/assets/images/ggplot2-histogram/Q6wvUTN.png)



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1pEGo8)
