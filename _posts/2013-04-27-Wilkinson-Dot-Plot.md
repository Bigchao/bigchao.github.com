---
layout: post
categories: datavis
title: ggplot2 - Wilkinson Dot Plot
---


有别于之前我们看过的Cleveland dot plot, Wilknson Dot Plot可以观察一组变量的分布情况，在数据量不是很大的时候，这种形式较为新颖，也显得比较专业。入手不难，是一个好的选择。

- **geom_dotplot()**

首先看一个例子

{% highlight r %}

    library(gcookbook) # For the data set
	countries2009 <- subset(countries, Year==2009 & healthexp>2000)
	p <- ggplot(countries2009, aes(x=infmortality))
	p + geom_dotplot()
		
{% endhighlight %}

![](/assets/images/ggplot2-wdp/X8gxtci.png)	

我们发现其实图并不是很好看

- Y轴没有意义 用scale_y_continuous来调整
- bin size 默认为数据范围的1/30, 可以进行调整 binwidth 调整

{% highlight r %}

	p + geom_dotplot(binwidth=.25) + geom_rug() +
    scale_y_continuous(breaks=NULL) +   # Remove tick markers
    theme(axis.title.y=element_blank()) # Remove axis label

{% endhighlight %}

![](/assets/images/ggplot2-wdp/Tw1MtH7.png)
---

**延伸问题**：不同变量出现在一个图上


- 设置`binaxis`

{% highlight r %}

	library(gcookbook) # For the data set
	ggplot(heightweight, aes(x=sex, y=heightIn)) +
      geom_dotplot(binaxis="y", binwidth=.5, stackdir="center")

{% endhighlight %}

![](/assets/images/ggplot2-wdp/QgEY7D0.png)



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1mWQc5)