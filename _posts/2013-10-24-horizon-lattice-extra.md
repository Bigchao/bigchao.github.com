---
layout: post
categories: datavis
title: latticeExtra - Horizon Plot的简单实现
---

上次给大家介绍了一下Horizon Plot的作图哲学，这次我们尝试着用LatticeExtra 这个包来实现以下。

基本代码量不大，不过可以自定义的地方不少。

有关Horizon Plot的作图哲学和一些其它预备知识，请看：[这里](http://yangchao.me/2013/10/horizon-plot)

文中实现的是按照原作者的Manual中的源代码, 在: **[这里](http://latticeextra.r-forge.r-project.org/man/horizonplot.html)**

并对代码进行详细注释。


{% highlight r %}
	
 	require(latticeExtra)
	## 产生数据集
	set.seed(1)
	dat <- ts(matrix(cumsum(rnorm(200 * 12)), ncol = 12))
	colnames(dat) <- paste("series", LETTERS[1:12])

{% endhighlight %}

在安装latticeExtra的时候如果遇到问题，请参见一下代码重新安装

{% highlight r %}
	
	install.packages("xtsExtra", repos="http://R-Forge.R-project.org") 	

{% endhighlight %}

horizon plot是将时间序列的数据可视化。所以我们需要将原来随机出来的数据转变成为随机数列， 用到的就是 `ts`命令

产生之后的数据是大概是这个样子


{% highlight r %}
	
 	head(dat,n=20)

{% endhighlight %}

<pre>

        series A  series B series C series D  series E   series F  series G  series H  series I  series J  series K
 [1,] -0.6264538  7.517331 16.30991 6.529042 -14.14928 -10.513177 -29.42972 -19.38421 -14.69748 -32.70429 -28.79620
 [2,] -0.4428105  9.206204 18.20556 8.031466 -15.97536  -9.401245 -27.50656 -19.20529 -14.68476 -32.12291 -30.71846
 [3,] -1.2784391 10.792793 17.60257 8.559774 -14.98008 -10.272023 -29.36339 -19.48783 -15.55821 -32.26963 -29.09876
 [4,]  0.3168417 10.461885 17.21170 9.101965 -14.99194 -10.061291 -31.46951 -20.25513 -15.21541 -30.76265 -28.57949
 [5,]  0.6463495  8.176649 16.79548 8.965292 -15.59157  -9.991895 -30.77186 -20.83154 -15.39280 -31.04218 -28.63534
 [6,] -0.1741189 10.674311 16.41982 7.828558 -15.76952 -11.654544 -29.86441 -21.74639 -14.47136 -29.01444 -27.93892
 [7,]  0.3133101 11.341377 16.05319 6.331931 -16.19550 -10.843704 -30.06040 -21.37648 -14.17042 -30.21018 -27.88540
 [8,]  1.0516348 11.882704 15.75751 6.108545 -15.19884 -12.756050 -30.26722 -22.84417 -13.47703 -28.89786 -29.19569
 [9,]  1.6274162 11.869305 17.19933 8.110265 -14.47118 -14.002804 -29.54218 -24.58953 -13.15156 -29.42187 -31.31875
[10,]  1.3220278 12.379413 16.50179 8.331968 -16.19781 -13.004649 -28.14346 -25.25691 -12.74351 -29.06762 -31.52683
[11,]  2.8338090 12.215037 16.11363 8.496341 -15.84441 -13.545522 -29.73402 -24.17263 -11.98367 -29.13930 -31.83962
[12,]  3.2236522 12.635732 16.76616 8.828965 -15.11760 -13.761898 -28.42952 -24.04980 -14.27082 -29.27244 -32.89785
[13,]  2.6024116 12.235485 17.89093 8.443757 -14.44934 -15.383835 -28.23351 -24.54405 -13.75298 -29.36052 -32.48063
[14,]  0.3877118 10.865277 17.11882 7.045003 -16.87365 -16.834799 -28.57285 -23.87446 -15.10022 -28.44273 -32.79608
[15,]  1.5126427 11.853116 16.61074 9.720744 -17.10901 -16.483889 -27.30771 -24.22594 -14.73706 -28.41129 -31.97053
[16,]  1.4677091 13.372861 17.13436 9.297058 -15.12938 -16.658436 -26.36793 -24.34092 -13.42231 -27.05236 -30.67926
[17,]  1.4515188 13.064120 18.15211 8.998456 -14.33258 -17.249865 -25.08998 -24.26793 -13.87061 -26.93886 -31.30437
[18,]  2.3953550 11.810830 17.90095 7.206114 -16.04186 -18.583892 -25.38114 -23.17916 -14.67878 -26.76456 -32.17951
[19,]  3.2165762 12.453072 16.47095 6.958106 -17.70553 -19.681190 -24.60497 -21.88659 -14.76505 -26.81804 -32.03616
[20,]  3.8104775 12.408363 18.18008 6.710802 -17.21442 -17.645087 -24.30924 -20.60408 -13.17962 -27.24280 -30.61097
       series L
 [1,] -35.67228
 [2,] -34.86300
 [3,] -35.57462
 [4,] -38.26421
 [5,] -38.83126
 [6,] -37.53206
 [7,] -37.72235
 [8,] -38.36040
 [9,] -40.39578
[10,] -41.00015
[11,] -41.80832
[12,] -42.41509
[13,] -42.32819
[14,] -42.56248
[15,] -41.29996
[16,] -42.86375
[17,] -44.35723
[18,] -45.45436
[19,] -45.48347
[20,] -43.30978

</pre>

<br>

我们可以基本认定这个就是一个时间序列的数据了。

因为我们是纯介绍这个Horizon Plot 这个图的作图格式，上面的代码只是模拟产生一个数据集。

首先我们可以将数据简单的可视化一下子。

{% highlight r %}
	
 	## show simple line plot first, for reference.
	xyplot(dat, scales = list(y = "same"))

{% endhighlight %}

![](/assets/images/horizon-latticeextra/zvu0wfQ.png)

数据的大概变化就是如此吧

下面开始我们的**Horizon Plot**

{% highlight r %}
	
 	## 先设定基本的图层设置
	infolayers <-
  	layer(panel.scaleArrow(x = 0.99, digits = 1, col = "grey",
                         srt = 90, cex = 0.7)) +
  	layer(lim <- current.panel.limits(),
        panel.text(lim$x[1], lim$y[1], round(lim$y[1],1), font = 2,
                   cex = 0.7, adj = c(-0.5,-0.5), col = "#9FC8DC"))

{% endhighlight %}


这两个图层的大概意思是

- 第一个图层： 在右侧画出每个空间，然后箭头
- 第二个图层： 计算出每个不同的变量的Horizon Plot中的基准值，并标图形的左边
- 有关颜色的编码信息可以参照这个网站，并推荐之。[颜色信息](http://www.colorhexa.com/9fc8dc)

如果大家对基准值的这个概念还不是很熟悉的话，可以快速看看我之前的这篇博客 [《Horizon Chart - 惊艳到爆的哲学》](http://yangchao.me/2013/10/horizon-plot)

下面我们通过一系列的变通来分别体会下不同的参数的含义。
命令都接近于自然语言，所以有些很显而易见的就不提了。

**Case 1**：each panel has a different origin and scale:

{% highlight r %}
	
 	## ('origin' default is the first data value in each series).
	horizonplot(dat, layout = c(1,12), colorkey = TRUE) +
  	  infolayers

{% endhighlight %}

![](/assets/images/horizon-latticeextra/2i7atuA.png)

短短一行代码，已经把Horizon Plot画出来了。

- colorky = TRUE 的意思是最右面是否把表明色阶的注释标出。大家可以试一试如果改成`FALSE`会怎样
- 每一个Panel的初始值和范围变化都是不一样的。


**Case 2**: fixed scale but different origin (baseline):

{% highlight r %}
	
 	## (similar in concept to scales = "sliced")
	horizonplot(dat, layout = c(1,12), horizonscale = 10, colorkey = TRUE) +
  	  infolayers

{% endhighlight %}

![](/assets/images/horizon-latticeextra/q3TGAps.png)


- 通过`horizonscale=10`, 所有的变化范围都变成了相同的值



**Case 3**: fixed scale and constant origin (all same scales):

{% highlight r %}
		
	horizonplot(dat, layout = c(1,12), origin = 0, horizonscale = 10, colorkey = TRUE) +
      infolayers

{% endhighlight %}

![](/assets/images/horizon-latticeextra/0Rs2hLj.png)


- 通过`origin=0`把所有的初始值都设为0



**Case 4**：same scales with full coverage of color scale:

{% highlight r %}
		
	horizonplot(dat, layout = c(1,12), origin = 0,
            scales = list(y = list(relation = "same")),
            colorkey = TRUE, colorkey.digits = 1) +
      infolayers

{% endhighlight %}

![](/assets/images/horizon-latticeextra/8CwVN62.png)


- 通过`scales = list(y = list(relation = "same"))`将右边的scales显示为绝对值, 而不是变化值。


**Case 5**: use ylab rather than strip.left, for readability.

{% highlight r %}
		
	horizonplot(dat, horizonscale = 10, colorkey = TRUE,
            	layout = c(1,12), strip.left = FALSE,
            	ylab = list(rev(colnames(dat)), rot = 0, cex = 0.7)) +
  	  layer_(panel.fill(col = "gray90"), panel.xblocks(..., col = "white"))

{% endhighlight %}

![](/assets/images/horizon-latticeextra/F0tqAw5.png)


- 通过`strip.left = FALSE`把原来竖起来的图例去掉
- 再通过`ylab = list(rev(colnames(dat)), rot = 0, cex = 0.7))`把原来的加上

<br>


Horizon Plot基本上常用的就是这一些用法。当然还有不少应用到实际中的，特别是在表现在股票价格变化中的时候，就可以明显感觉它的强大之处。我们会在之后介绍具体的特别应用

相关文章：



- [颜色信息](http://www.colorhexa.com/9fc8dc)

- [LatticeExtra: R Forger](http://latticeextra.r-forge.r-project.org/#segplot&theme=default)

- [官方Manual](http://cran.r-project.org/web/packages/latticeExtra/latticeExtra.pdf)
- 文中代码和图片在: [这里](http://pan.baidu.com/s/1DvW29)