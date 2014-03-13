---
layout: post
categories: related
title: 在Github中使用MathJax编辑公式
---

因为近期想写一点关于数据挖掘，机器学习的东西时要用到公式，所以就寻思着在自己github上的博客上加上公式编辑的功能。

找了网上相关的资料，其实实现起来并不难。

主要步骤：

**1 在html加入指向MathJax的命令**

{% highlight html %}
<script type="text/javascript"
 src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}

注意通常情况下，我们把这段话放在Jekyll默认的html头文件下面。这样就省得再今后每次使用都继续修改了。

一般这个文件的位置是在`/_layout/index.html` 中。

如果想稍微折腾下的话，其实Mathjax有一些设定的参数可以调整

比如我想让Mathjax支持公式的编号和引用，就在还是上面的文件中加上了如下代码。

{% highlight html %}
<script type="text/x-mathjax-config">
 MathJax.Hub.Config({
 TeX: {equationNumbers: {autoNumber: ["AMS"], useLabelIds: true}},
 "HTML-CSS": {linebreaks: {automatic: true}},
 SVG: {linebreaks: {automatic: true}}
 });
</script>
{% endhighlight %}

**2 安装kramdown解析器**

一般默认的rdiscount会出现一些问题。所以用kramdown比较方便。有网友给出了rdiscount相关问题的解决方法，我也懒得折腾了，直接改成了kramdown。

安装过程非常简单

{% highlight ruby %}
gem install kramdown
{% endhighlight %}

**3 修改默认值**

修改_config.yml，把markdown选项修改为:

`markdown: kramdown`

安装就应该没有问题了。

测试：

{% highlight latex %}
$$
a^2 + b^2 = c^2
$$
{% endhighlight %}

在想加入公式前后两端加入两个`$`符号就可以了。
最后效果是这个样子的。

$$
a^2 + b^2 = c^2
$$

kramdown还给出了一个稍微复杂一点的例子。关于对齐的。

{% highlight latex %}
$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$
{% endhighlight %}

得到的效果是这个样子的：

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

刚我加过的小小指令就是可以给公式加入标号。

{% highlight latex %}
\begin{equation}\label{exampleone}r = r_F+ \beta (r_M - r_F) + \epsilon\end{equation}\
{% endhighlight %}


并且可以引用公式(\ref{exampleone})：

\begin{equation}\label{exampleone}r = r_F+ \beta (r_M - r_F) + \epsilon\end{equation}\

有关使用，强烈建议去看看kramdown和mathjax的官网，有非常详尽的介绍。和相关的Latex介绍数学公式的书籍。



有用链接：
---
[kramdown官网的相关链接](http://kramdown.gettalong.org/syntax.html#math-blocks)
[Math into LATEX](www.ctan.org/tex-archive/info/mil/mil.pdf)
[MathJax让你爱上数学公式](http://zhiqiang.org/blog/it/mathjax-make-mathematics-beautiful.html#mjx-eqn-exampleone)