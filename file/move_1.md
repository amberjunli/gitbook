# 移动web滚动
1. 滚动事件： 即onscroll事件，形成原因通俗解释是当子元素的高度超过父元素的高度时，且父元素的高度时定值window除外，就会形成滚动条，滚动分为两种：局部滚动和body滚动。

2. onscroll方法： 一般情况下当我们需要监听一个滚动事件时通常会用到onscroll方法来监听滚动事件的触发。
如果在浏览器上调试这个方法在浏览器上很好用，但是如果跑在手机端就没有想象中的效果了。

3. body滚动： 在移动端如果使用body滚动，意思就是页面的高度由内容自动撑大，body自然形成滚动条，这时我们监听window.onscroll，发现onscroll并没有实时触发，只在手机触摸的屏幕上一直滚动时和滚动停止的那一刻才触发，采用了wk内核的webview除外。

4. 局部滚动： 在移动端如果使用局部滚动，意思就是我们的滚动在一个固定宽高的div内触发，将该div设置成overflow:scroll/auto；来形成div内部的滚动，这是我们监听div的onsrcoll发现触发的时机区分android和ios两种情况，具体可以看下面表格：
5. 不同机型onscroll时间触发情况：

|body滚动|局部滚动
---|:--:|---
IOS|不能实时触发|不能实时触发
Android|实时触发|实时触发
IOS wkwebview内核|实时触发|实时触发

1. wkwebview内核：这里说明一下关于ios的wkwebview内核是IOS从ios8开始提供的新型webview内核，和之前的uiwebview相比，性能要好，具体大家可以自行查看关于wkwebview的相关概念。

2. body滚动和局部滚动：在采用wkwebview内核的页面中scroll是可以实时触发的，如果使用的是原来的uiwebview则不能够实时触发。

## 关于模拟滚动
1. 正常的滚动：我们平时使用的scroll，包括上面讲的滚动都属于正常滚动，利用浏览器自身提供的滚动条来实现滚动，底层室友浏览器内核控制。

2. 模拟滚动：最典型的例子就是iscroll了，原理一般有两种：

(1) 监听滚动圆度的touchmove事件，当事件触发时修改元素的transform属性来实现元素的位移，让手指离开是触发touched事件，然后采用requestanimationframe来在一个线型函数下不断的修改元素的transform来实现手指离开是的一段惯性滚动距离。

(2) 监听滚动元素的touchmove事件，当事件触发时修改元素的tranform属性来
