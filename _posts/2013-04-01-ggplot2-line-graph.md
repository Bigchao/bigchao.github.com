---
layout: post
categories: datavis
title: ggplot2 - Line Graph
---

上次讲过了GGPlot的条形图，大家对GGPLOT2的画图理念一定有了基本的理解。这次我们来学习折线图。

基本的语法可谓非常简单

**Base Plot**

{% highlight r %}
	
	ggplot (BOD,aes(x=Time,y=demand)) + geom_line

{% endhighlight %}

![](/assets/images/ggplot2-line/GTtCqKV.png)

我们查看BOD的原数据
<pre>
Time demand
    1    8.3
    2   10.3
    3   19.0
    4   16.0
    5   15.6
    7   19.8</pre>
<br></br>

这个时候用到Factor函数，将Time进行因子化

{% highlight r %}


	BOD1 <- BOD # Make a copy of the data
	BOD1$Time <- factor(BOD1$Time)
	ggplot(BOD1, aes(x=Time, y=demand, group=1)) + geom_line()

{% endhighlight %}

- 尤其要注意，因子化之后必须用到aes(group=1),才能正常画出图来。
- 若想改变坐标的上下限可以用到如下代码，放在之前代码的后面。具体的用法我们以后会专门讲

{% highlight r %}

	ylim(0,max(BOD$demand)
	expand_limits(y=0)

{% endhighlight %}

---

**延伸问题1**：如果现在我想在原来折线图上加散点怎么办？

其实也很简单！

{% highlight r %}

	ggplot(BOD,aes(x=Time,y=demand))+geom_line()+geom_point()

{% endhighlight %}

![](/assets/images/ggplot2-line/p25J3mB.png)

同样，个人觉得，这个图做出来尚且没有Excel方便，也没有Excel好看。 LOL


---
**延伸问题2** ：如果想用Log Y的坐标轴的话，其实也很简单，还稍微可以体现点GGPLOT2的价值了。

{% highlight r %}
	library(gcookbook)
	ggplot(worldpop,aes(x=Year,y=Population) + geom_line()
	 + geom_point() + scale_y_log10()
		

{% endhighlight %}

![](/assets/images/ggplot2-line/E0c7o7t.png)

- 最后的一行代码用处就是把Y轴设为log10形式的，大家感受下

---
**延伸问题3**： 如果想一图多线怎么办？

其实随着上次的bar图中，我们已经大致了解了GGPLOT的思路，想一图多线，其实就是在aes的映射过程中多加一个变量即可。可谓思想方法非常明了，有法可依。

{% highlight r %}

	library(plyr)
	# 要用Plyr拓展包做数据的预处理。关于这个包，我写了另一篇教程，建议大家看看
	tg <- ddply(ToothGrowth, c("supp", "dose"), summarise, length=mean(len))
	# 对ToothGrowth数据集中的supp，dose两个求len变量的平均

{% endhighlight %}


在这个例子中，dose应该被因子化：0.5,1.0,1.5 只包含这三个剂量。
{% highlight r %}

	ggplot(tg, aes(x=factor(dose), y=length, colour=supp, group=supp)) + geom_line()

{% endhighlight %}

![](/assets/images/ggplot2-line/VCyTzUO.png)

**这里特别要注意！**

**这里特别要注意！**

**这里特别要注意！**

在因子化之后，aes中一定要包含group=supp 这个语句。
否则会出现如下报错：

**geom_path: Each group consists of only one
observation. Do you need to adjust the group aesthetic?**

我当时自己作图的时候，出现过这个问题找了好多资料都没有给出一个特别好的解答。还是要啃官方的manual，一个字一个字的读，这样才能理解透彻。

有关这个问题的最好解答：[这里](http://kohske.wordpress.com/2010/12/27/faq-geom_line-doesnt-draw-lines/)

---
**延伸问题4**： 改变线中散点的颜色

改变其中的这两个参数即可
- **shape**
- **fill**

体会如下语句

{% highlight r %}

	ggplot(tg, aes(x=dose, y=length, shape=supp)) + geom_line() +
    geom_point(size=4)           
	# Make the points a little larger

{% endhighlight %}

![](/assets/images/ggplot2-line/JrojSxN.png)

{% highlight r %}

	ggplot(tg, aes(x=dose, y=length, fill=supp)) + geom_line() +
    geom_point(size=4, shape=21) 
	# Also use a point with a color fill

{% endhighlight %}

![](/assets/images/ggplot2-line/cAvavrJ.png)

我们发现上面的作图方法会出现点重合的情况
所以要可以稍稍微调下

{% highlight r %}

	ggplot(tg, aes(x=dose, y=length, shape=supp)) +
      geom_line(position=position_dodge(0.2)) +         
	  # Dodge lines by 0.2
      geom_point(position=position_dodge(0.2), size=4) 
	  # Dodge points by 0.2

{% endhighlight %}

![](/assets/images/ggplot2-line/BqSaXvs.png)

---
**延伸问题5**： 如果想改变折线和散点的颜色和类型怎么办？

修改下列括号中的参数即可

- **geom_line()**
- **geom_point()**

{% highlight r %}

	ggplot(BOD, aes(x=Time, y=demand)) +
      geom_line(linetype="dashed", size=1, colour="blue")

{% endhighlight %}

![](/assets/images/ggplot2-line/wXPFtXP.png)

{% highlight r %}

	ggplot(tg, aes(x=dose, y=length, colour=supp)) +
      geom_line(linetype="dashed") +
      geom_point(shape=22, size=3, fill="white")	

{% endhighlight %}

![](/assets/images/ggplot2-line/KRXvejA.png)


上面给大家基本介绍了一下有关折线图的简单画法。一直在纠结的问题就是，这样的作图其实并不太方便。相比之下还是Excel来得快。
但是，在R作图有个好处是可以自定义的东西很多，可以精雕细琢的很多，尤其通常在数据分析之后直接出图很是方便。写成代码可以重复使用。而Excel如果不用VBA，或者其他插件，相比R还是有不少差距的。建议大家还是学学ggplot。




- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1mqTOL)

