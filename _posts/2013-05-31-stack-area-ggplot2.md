---
layout: post
categories: datavis
title: ggplot2 - Stacked Area Graph
---

这一节重点介绍ggplot2中，关于堆积图的制作方法。
提纲挈领记住最重要的！

- **Geom_area()**

接下来的步骤大家可以想到了吧。
在拎清这个主题之后，我们就会慢慢来对图形进行微调即可

**Base Plot**

{% highlight r %}

	library(gcookbook) 
	library(ggplot2)
	# For the data set
	ggplot(uspopage, aes(x=Year, y=Thousands, fill=AgeGroup)) + geom_area()

{% endhighlight %}

![](/assets/images/ggplot2-stack/ZAghMVD.png)

---
**延伸问题1**

我们发现了图型和图例中的颜色顺序是相反的。这样很难看。有两种解决方案

1. 改图例：通过改变Scale中的breaks值

{% highlight r %}

	ggplot(uspopage, aes(x=Year, y=Thousands, fill=AgeGroup)) +
      geom_area(colour="black", size=.2, alpha=.4) +
	  scale_fill_brewer(palette="Blues", breaks=rev(levels(uspopage$AgeGroup)))
	#rev 表示将图例倒转

{% endhighlight %}
![](/assets/images/ggplot2-stack/G1umci8.png)

2. 改数据：引用Plyr中desc函数

{% highlight r %}

	library(plyr) # For the desc() function
	ggplot(uspopage, aes(x=Year, y=Thousands, fill=AgeGroup, order=desc(AgeGroup))) +
      geom_area(colour="black", size=.2, alpha=.4) +
      scale_fill_brewer(palette="Blues")

{% endhighlight %}

![](/assets/images/ggplot2-stack/xS9aP30.png)

---

**延伸问题2**
我们发现上边的图两侧也有黑色框线，不美观，如何设置去掉这些黑色框线？

- Geom_area 中的颜色设为NA
- 使用 **Geom_line**

{% highlight r %}

	ggplot(uspopage, aes(x=Year, y=Thousands, fill=AgeGroup, order=desc(AgeGroup))) +
      geom_area(colour=NA, alpha=.4) +
      scale_fill_brewer(palette="Blues") +
      geom_line(position="stack", size=.2)

{% endhighlight %}

![](/assets/images/ggplot2-stack/JUEp6T8.png)

**延伸问题3**
如果想制作成比例的堆积图，应该怎么办？

- 只需要在Plyr拓展包稍微改变下数据即可，和成比例柱形图的道理完全一样

{% highlight r %}

    library(gcookbook) # For the data set
    library(plyr)  # For the ddply() function
    # Convert Thousands to Percent
    uspopage_prop <-ddply(uspopage, "Year",transform, Percent =Thousands /sum(Thousands) * 100)
	# 在uspopage数据中，把Year中对应的Thousands值除以同一个Year中所有Thousands的总和。

{% endhighlight %}

下面的做法就和之间的完全一样了

{% highlight r %}

	ggplot(uspopage_prop, aes(x=Year, y=Percent, fill=AgeGroup)) +
      geom_area(colour="black", size=.2, alpha=.4) +
      scale_fill_brewer(palette="Blues", breaks=rev(levels(uspopage$AgeGroup)))


{% endhighlight %}


![](/assets/images/ggplot2-stack/dSxIwAu.png)



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1nCMPq)








