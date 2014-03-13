---
layout: post
categories: datavis
title: ggplot2 - 为拟合曲线加置信空间
---

如何在拟合曲线图中增加置信空间呢？

答案很简单：

- **geom_ribbon()**

{% highlight r %}

     library(gcookbook) # For the data set
	 # Grab a subset of the climate data
	 clim <- subset(climate, Source == "Berkeley", select=c("Year", "Anomaly10y", "Unc10y"))

{% endhighlight %}

这时数据是泽个样子滴:
其中Unc10y是置信空间，Anomaly10y是我们关注的变量
<pre>
Year   Anomaly10y Unc10y
1800     -0.435  0.505
1801     -0.453  0.493
1802     -0.460  0.486
...
2003      0.869  0.028
2004      0.884  0.029
</pre>
<br></br>

下面就可以作图啦~带上我们的geom_ribbon

{% highlight r %}

	ggplot(clim, aes(x=Year, y=Anomaly10y)) +
      geom_ribbon(aes(ymin=Anomaly10y-Unc10y, ymax=Anomaly10y+Unc10y), alpha=0.2) +
      geom_line()

{% endhighlight %}

![](/assets/images/ggplot2-significance/1g0JO13.png)

当然了，我们可以把置信空间设置为曲线
代码如下：

{% highlight r %}

	# With a dotted line for upper and lower bounds
	ggplot(clim, aes(x=Year, y=Anomaly10y)) +
      geom_line(aes(y=Anomaly10y-Unc10y), colour="grey50", linetype="dotted") +
      geom_line(aes(y=Anomaly10y+Unc10y), colour="grey50", linetype="dotted") +
      geom_line()

{% endhighlight %}

![](/assets/images/ggplot2-significance/syubzvU.png)



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1gRnnX)

