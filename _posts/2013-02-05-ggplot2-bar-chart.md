---
layout: post
categories: datavis
title: ggplot2 - Barchart
---

本章主要介绍有关条形图在GGPLOT2中的实现，代码来自
[R Graphics Cookbook](http://www.amazon.com/R-Graphics-Cookbook-Winston-Chang/dp/1449316956) 第三章

**Base Problem**：Basic Bar Chart 

**Solution** ：

- ggplot()
- geom_bar(stat="identity")

以上是固定搭配,记住即可

{% highlight r %}

	library（ggplot2）
	library(gcookbook) # For the data set
	ggplot(pg_mean, aes(x=group, y=weight)) + geom_bar(stat="identity")
	# aes 表示x轴和y轴对应的映射，Geom_bar表示显示为柱状图

{% endhighlight %}

![](/assets/images/ggplot2-barchart/VhPMUx7.png)

通常默认的barchart颜色是黑色，非常难看
可以换成自定义颜色
在fill中改，注意对比上下代码差别，同时可以修改自定义的柱状图外框颜色

{% highlight r %}

	ggplot(pg_mean, aes(x=group, y=weight)) +
    geom_bar(stat="identity", fill="lightblue", colour="black")

{% endhighlight %}

![](/assets/images/ggplot2-barchart/buxnSKT.png)

---
**延伸问题 1** ：Grouping Bars Together

**Solution** ： 在aes中，把第三个变量映射到fill中，同时把`postion`换成 **dodge**

{% highlight r %}

	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) +
    geom_bar(position="dodge")

{% endhighlight %}

![](/assets/images/ggplot2-barchart/CxRfugY.png)

- 如果要修改外框颜色，在`geom_bar() `中修改 `colour='black'`
- 修改柱子的颜色可以用`scale_fill_brewer() `或者`scale_fill_manual()`. 

{% highlight r %}

    ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) + 
      geom_bar(position="dodge", colour="black") + 
      scale_fill_brewer(palette="Pastel1")

{% endhighlight %}

![](/assets/images/ggplot2-barchart/84MFCnj.png)

---

**延伸问题 2** ： 在柱状图中使用不同颜色

**Solution** ： 把适当的变量映射到`Fill`中


首先数据源是这样的

{% highlight r %}

	library(gcookbook) # For the data set
	upc <-subset(uspopchange,rank(Change)>40)
	upc

{% endhighlight %}

State |	Abb	| Region	| Change
--- | --- | --- | ---
Arizona	| AZ |	West |	24.6
Colorado |	CO |	West |	16.9
Florida	|FL	| South|	17.6
Georgia	|GA|	South|	18.3
Idaho	|ID|	West|	21.1
Nevada	|NV|	West	|35.1
North Carolina|	NC|	South|	18.5
South Carolina|	SC|	South|	15.3
Texas|	TX|	South|	20.6
Utah|	UT|	West|	23.8

<br></br>
在`fill`中加上`region`，就会映射过来


{% highlight r %}

    ggplot(upc, aes(x=Abb, y=Change, fill=Region)) +
	geom_bar(stat="identity")

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/piTTpmj.png) 

- 用`reorder`函数，把柱状图按照大小排列会显得更专业
- 复习修改颜色的`scale_fill_manual`
- 对x轴修改坐标轴注释

{% highlight r %}

	ggplot(upc, aes(x=reorder(Abb, Change), y=Change, fill=Region)) +
	  geom_bar(stat="identity", colour="black")  +
	  scale_fill_manual(values=c("#669933", "#FFCC66")) + 
	  xlab("State")

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/IFu6DAK.png)

--- 

**延伸问题 3**：Coloring Negative and Postive Bars Differently

**Solution**: 设定新的变量，将新建变量映射到fill中


其实这个方法很常见，在以后讲坐标轴的时候还会再次提到，方法比较简单就是根据subset函数新建一个符合要求的函数，然后只要在GGPLOT的时候把新建函数映射即可，其他都一样

{% highlight r %}

	csub <- subset(climate, Source=="Berkeley" & Year >= 1900)
	csub$pos <- csub$Anomaly10y >= 0
	ggplot(csub, aes(x=Year, y=Anomaly10y, fill=pos)) +
  	  geom_bar(stat="identity", position="identity")

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/gS0niDp.png)

对这个图的小微调
- 再次复习scale_fill_manual()进行修改颜色
- 通过设定guide=FALSE 去掉图例
- 改变柱子外框黑线的厚度，通过设定size

{% highlight r %}

    ggplot(csub, aes(x=Year, y=Anomaly10y, fill=pos)) + 
	  geom_bar(stat="identity", position="identity", colour="black", size=0.25) +
	  scale_fill_manual(values=c("#CCEEFF", "#FFDDDD"), guide=FALSE)

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/gLFTMbs.png)
---   

**延伸问题 4**： Adjusting Bar Width and Spacing

**Solution**： 在`geom_bar`中调整width参数

- 在geom_bar中调整width改变柱子宽度，也就是改变了柱子之间的距离
- 如果用dodge方法做的图，可以用`position_dodge（）`参数来改，默认的参数设置是0.9，如果不写则为两个贴在一起

{% highlight r %}

	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) +
    geom_bar(stat="identity", width=0.5, position="dodge")

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/FPaVWds.png)

以下设置了在`geom_bar`中设置了`width，postion_dodge()`

{% highlight r %}

	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) +
      geom_bar(stat="identity", width=0.5, position=position_dodge(0.7))

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/iqy952E.png)
---

**延伸问题 5** ：Making a Stacked Bar Graph

**Solution** ： 用`geom_bar`，同时映射到`fill`中

 {% highlight r %}
	library(gcookbook) # For the data set
	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) +
      geom_bar(stat="identity")

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/opTysNC.png)

注意上段代码和**延伸问题1**的唯一不同之处之处就在于在geom_bar
()中

- 堆积图：`stat="indentity"`
- 聚集图：`position="dodge"`

我们看到图和图例中的是反的，要修正这个问题，用如下代码

{% highlight r %}

	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar)) +
      geom_bar(stat="identity") +
      guides(fill=guide_legend(reverse=TRUE))

 {% endhighlight %}

![](/assets/images/ggplot2-barchart/52ChlFe.png)

当然，我们也可以改变图中堆积的颜色。这需要用到pylr拓展包，这个拓展包可谓非常强大，在R下载量最多的拓展包中排名保持第一名。

具体做法

- 用order指令
- desc函数

{% highlight r %}	

	library(plyr) # Needed for desc()
	ggplot(cabbage_exp, aes(x=Date, y=Weight, fill=Cultivar, order=desc(Cultivar))) +
      geom_bar(stat="identity")

{% endhighlight %}

![](/assets/images/ggplot2-barchart/9k0Y2le.png)    

---
**延伸问题 6**：Making a Propotional Stacked Bar Graph 

**Solution**：用到Pylr拓展包

其实这个问题可以转化为另外一个问题

1. 新建一个函数，算出比例
2. 根据比例在作图

其实这本身和GGPLOT2没有特别关系了，就是我之前提到的数据预处理的问题

用Plyr比较简单，当然用其他工具也都可以胜任，技术难度不高

{% highlight r %}	

	library(gcookbook) # For the data set
	library(plyr)
	# Do a group-wise transform(), splitting on "Date"
	ce <- ddply(cabbage_exp, "Date", transform,
            percent_weight = Weight / sum(Weight) * 100)
	ggplot(ce, aes(x=Date, y=percent_weight, fill=Cultivar)) +
      geom_bar(stat="identity")

{% endhighlight %}

![](/assets/images/ggplot2-barchart/pZAfWxF.png)

着重解析下plyr里ddply的语法

- cabbage是数据集
- "Date" 通俗来说就是x轴的变量
- transform是要做的变形，在ddply中还有summarize等
- 最后一项是是新建的变量和变型方法




- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1ch4sr)

