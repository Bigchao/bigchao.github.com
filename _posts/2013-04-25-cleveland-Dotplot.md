---
layout: post
categories: datavis
title: ggplot2 - Cleveland Dot Plot
---


在[The Elements of Graphing Data](http://www.amazon.com/Elements-Graphing-Data-William-Cleveland/dp/0963488414)中，作者Cleveland提出了一种新的数据表现形式，以后就命名为Cleveland Dot Plot。（实在没有找到对应的中文名是什么）

用GGPLOT2可以非常容易的实现

- 用 **geom_point**即可
- 其他的进行小小的微调

**Basic Plot**
{% highlight r %}

	library(gcookbook) 
	# For the data set
	tophit <- tophitters2001[1:25, ] 
	# Take the top 25 from the tophitters data set
	ggplot(tophit, aes(x=avg, y=name)) + 
	geom_point()
{% endhighlight %}

![](/assets/images/ggplot2-clp/hVabevA.png)

从图中看出，数据的排列顺序是按照名称的字母排列来的。效果就是造成的图不直观，比较乱

- 用reorder函数进行重新排序
- 对图形背景进行微调

{% highlight r %}

	ggplot(tophit,aes(x=avg,y=reorder(name,avg))) +
    geom_point(size=3) +  # Use a larger dot
    theme_bw() +
    theme(panel.grid.major.x =element_blank(),
          panel.grid.minor.x =element_blank(),
          panel.grid.major.y =element_line(colour="grey60",linetype="dashed"))
      # 以上两行是将x轴的坐标轴主刻度线和副刻度线都去掉,同时y轴的刻度线改为间断线型，同时颜色改为灰色
{% endhighlight %}

![](/assets/images/ggplot2-clp/mUmcaIv.png)

当然也可以调换x，y轴的顺序

{% highlight r %}

	ggplot(tophit, aes(x=reorder(name, avg), y=avg)) +
    geom_point(size=3) +                        # Use a larger dot
    theme_bw() +
    theme(axis.text.x = element_text(angle=60, hjust=1),
          panel.grid.major.y = element_blank(),
          panel.grid.minor.y = element_blank(),
          panel.grid.major.x = element_line(colour="grey60", linetype="dashed"))

{% endhighlight %}
![](/assets/images/ggplot2-clp/3z81WG7.png)

如果想要根据不同类别再次作图
- 重新进行排序
- 微调图型元素

因为reorder只能对一个变量排序，如果想按照两个排序则必须要手动来。书中给出的方法如下

{% highlight r %}

	# Get the names, sorted first by lg, then by avg
	nameorder <- tophit$name[order(tophit$lg, tophit$avg)]
	# Turn name into a factor, with levels in the order of nameorder
	tophit$name <- factor(tophit$name, levels=nameorder)

{% endhighlight %}

- geom_segment:实现将直线只到圆点位置
- 把图例放在图中需要在theme中修改`legend.position()`


整个代码

{% highlight r %}

	ggplot(tophit, aes(x=avg, y=name)) +
    geom_segment(aes(yend=name), xend=0, colour="grey50") +
    geom_point(size=3, aes(colour=lg)) +
    scale_colour_brewer(palette="Set1", limits=c("NL","AL")) +
    theme_bw() +
    theme(panel.grid.major.y = element_blank(),   
		  # No horizontal grid lines
          legend.position=c(1, 0.55),             
		  # Put legend inside plot area
          legend.justification=c(1, 0.5))

{% endhighlight %}
![](/assets/images/ggplot2-clp/cKWISqQ.png)

也可以用facet_grid来实现相关
代码改动量不大

{% highlight r %}

    ggplot(tophit,aes(x=avg,y=name)) +
      geom_segment(aes(yend=name),xend=0,colour="grey50") +
      geom_point(size=3,aes(colour=lg)) +
      scale_colour_brewer(palette="Set1",limits=c("NL","AL"),guide=FALSE) +
      theme_bw() +
      theme(panel.grid.major.y =element_blank()) +
      facet_grid(lg ~ .,scales="free_y",space="free_y")
    # 只是把scale_colour_brewer部分和后面改了一点
    

{% endhighlight %}

![](/assets/images/ggplot2-clp/3u738ru.png)

注意在facet_grid中的参数

- scales：表示是否允许不同面（facet）的坐标轴变化，或者固定。 `scales=free_y` 表示y轴随意变化。我们看到因为两个facet所含数据是不一样的，所以改成`free_y`显得更加好看
- space： 也是一个道理



- 文中代码和图片下载在： [这里](http://pan.baidu.com/s/1LAKA)








