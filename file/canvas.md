# canvas简介
canvas 是 HTML5 新定义的标签，通过使用脚本（通常是 JavaScript）绘制图形。`<canvas>`  标签只是图形容器，相当于一个画布，canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成，相当于使用画笔在画布上画画。
默认情况下，`<canvas>` 没有边框和内容。默认是一个 300*150 的画布，所以我们创建了 `<canvas>` 之后要对其设置宽高。
## canvas能够做什么？
- 基础图形的绘制
- 文字的绘制
- 图形的变形和图片的合成
- 图片和视频的处理
- 动画的实现
- 小游戏的制作
## 关于canvas标签的基本概念
网页中定义canvas元素之后，它只是一张空白的画布，想要在画布上绘图，一定要经过下面几步。
1.获取canvas元素对应的DOM元素对象，这必须是一个canvas对象
2.调用canvas对象getContext()方法，该方法返回一个canvasRenderingContext2D 对象，该对象可以绘制图形。
3.调用 canvasRenderingContext2D 对象的方法进行绘图。

[Canvas API](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D)

```
// 获取canvas容器
var can = document.getElementById('canvas');
// 创建一个画布
var ctx = can.getContext('2d');
```
另外我们还可以得到容器的宽和高度
```
var canWid = can.width; // canvas的宽度
var canHei = can.height; // canvas的高度
```
canvas只是一个容器，本身没有绘制的能力，所以我们要得到一个画布ctx，使之具有绘制各种图形的能力。
基础API
```
// 绘制路径
beginPath() // 开始定义路径
closePath() // 关闭前面定义的路径
moveTo(float x,float y) // 把 canvas 的当前路径的结束点移动到 x, y 对应的点
lineTo(float x,float y) // 把 canvas 的当前路径从当前结束点连接到 x , y 对应的点
// 绘制矩形
strokeRect(float x,float y,float width,float height) // 绘制一个矩形边框
fillRect(float x,float y,float width,float height) // 填充一个矩形边框
fillStyle // 设置填充 canvas 路径所使用的填充风格
strokeStyle // 设置绘制 canvas 路径的填充风格
// 绘制字符串
fillText(String Text, float x, float y, [float maxWidth]) // 填充字符串
strokeText(String Text, float x, float y, [float maxWidth]) // 绘制字符串边框
textAlign // 设置绘制字符串的水平对齐方式(start、end、left、right、center等)
textBaseAlign // 设置绘制字符串的垂直对齐方式(top、hanging、middle、alphabetic、idecgraphic、bottom 
arc(float x,float y,float radius,float startAngle,float endAngle,boolean counterclockwise)
bezierCurveTo(float cpX1, float cpY1, float cpX2, float cpY2, float x, float y) //在当前路径上添加一段贝塞尔曲线
drawImage(Image image, float x, float y) // 把 image 绘制在 x, y 处。该方法不会对图片做任何缩放处理，绘制处理的图片保持原来的大小
drawImage(Image image, float x, float y, float width, float height)|把 image 绘制在 x, y 处。该方法会对图片进行缩放，绘制出来的宽度为 width，高度为 height。
drawImage(Image image, integer sx, integer sy, integer sw, integer sh, float dx, float dy,float dw, float dh) // 该方法将会从 image 上“挖出”一块绘制到画布上。其中 sx, sy 两个参数控制从原图片上的哪一个位置开始挖去, sw， sh 两个参数控制从原图片挖球的宽度和高度；dx, dy 两个参数控制把挖取的图片绘制到画布上的哪个位置，而 dw, dh 则控制对绘制图片进行缩放，绘制出来的宽度是 dw, 高度 dh
```
## canvas动效

动画本质上是图像按照事先设定好的顺序在一定的时间内的图像序列变化运动。
这种图像序列的变化运动给我们最为直观的感受就是图像仿佛真实的在运动一般，由此产生动画效果。
每一张图像的内容是事先设定好的，内容是不变的，变化的是图像序列按照规定的顺序在变动；
构成动画特效需要在单位时间内渲染一定量的图像，每张图像称之为帧（Frame），通常电影只需要24FPS就足够流畅，而游戏则需要60FPS，我们设计动画时通常选用60FPS。

实现动画效果的非常重要的API：
`window.requestAnimationFrame(callback)`
这个API的功能就是，你可以在回调函数里面写一个脚本改变图形的宽高，然后这一API就会根据浏览器的刷新频率而在一定时间内调用callback；然后，根据递归的思想，实现callback的反复调用，最终实现动画效果；
```
(function drawFrame(){
    window.requestAnimationFrame(drawFrame);
})();
```
上面的代码意思是立即执行drawFrame这个函数，发现  window.requestAnimationFrame(drawFrame)，根据浏览器的刷新频率，在一定时间之后执行；
接下来执行你所编写的改变图像内容（图像的位置、宽高、颜色等等）的脚本，执行回调；
循环反复，形成动画效果
循环调用自身，requestAnimationFrame是一个新的API,作用与setTimeInterval一样，不同的是它会根据浏览器的刷新频率自动调整动画的时间间隔。

setTimeout 其实就是通过设置一个间隔时间来不断的改变图像的位置，从而达到动画效果的。利用seTimeout实现的动画在某些低端机上会出现卡顿、抖动的现象。 这种现象的产生有两个原因：
- setTimeout的执行时间并不是确定的。在Javascript中， setTimeout 任务被放进了异步队列中，只有当主线程上的任务执行完以后，才会去检查该队列里的任务是否需要开始执行，因此 setTimeout 的实际执行时间一般要比其设定的时间晚一些。
- 刷新频率受屏幕分辨率和屏幕尺寸的影响，因此不同设备的屏幕刷新频率可能会不同，而 setTimeout只能设置一个固定的时间间隔，这个时间不一定和屏幕的刷新时间相同。

与setTimeout相比，requestAnimationFrame最大的优势是由系统来决定回调函数的执行时机。具体一点讲，如果屏幕刷新率是60Hz,那么回调函数就每16.7ms被执行一次，如果刷新率是75Hz，那么这个时间间隔就变成了1000/75=13.3ms，换句话说就是，requestAnimationFrame的步伐跟着系统的刷新步伐走。它能保证回调函数在屏幕每一次的刷新间隔中只被执行一次，这样就不会引起丢帧现象，也不会导致动画出现卡顿的问题。
- CPU节能：使用setTimeout实现的动画，当页面被隐藏或最小化时，setTimeout 仍然在后台执行动画任务，由于此时页面处于不可见或不可用状态，刷新动画是没有意义的，完全是浪费CPU资源。而requestAnimationFrame则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统步伐走的requestAnimationFrame也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了CPU开销。
- 函数节流：在高频率事件(resize,scroll等)中，为了防止在一个刷新间隔内发生多次函数执行，使用requestAnimationFrame可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销。一个刷新间隔内函数执行多次时没有意义的，因为显示器每16.7ms刷新一次，多次绘制并不会在屏幕上体现出来。

### loading动效
```
// 模拟数据
  let images = [
    {"name": "loading1", "url": "loading/loading1.png"},
    ... // 省略
  ]
  // 使用DOM方法获取画布
  var myCanvas = document.querySelector("canvas");
  // 设置画布满屏
  myCanvas.width = document.documentElement.clientWidth;
  myCanvas.height = document.documentElement.clientHeight;

```
加载图片资源
```
for (var i = 0; i < images.length; i++) {
    R[images[i].name] = new Image();
    R[images[i].name].src = images[i].url;
    R[images[i].name].onload = function () {
      alreadyLoadNumber++;
      if (alreadyLoadNumber == images.length) {
        // 加载完毕
        load();
      }
    }
  }
```
画图
```
ctx.clearRect(0, 0, myCanvas.width, myCanvas.height);
setInterval(() => {
  ctx.clearRect(0, 0, myCanvas.width, myCanvas.height);
  ctx.drawImage(img, myCanvas.width / 2 - 100, myCanvas.height * 0.3 , 200, 280);
  step++;
}, 100);
```
### 小球动效

```
  // 小球类
  function Ball (x, y) {
    this.x = x; // 圆心坐标x
    this.y = y; // 圆心坐标y
    this.r = 30;
    this.color = "rgba(" + parseInt(Math.random() * 256) +"," + parseInt(Math.random() * 256) + ","+ parseInt(Math.random() * 256) + "," + 0.5 +")"
    this.dx = parseInt(Math.random() * 18) - 9; // x的变化值
    this.dy = parseInt(Math.random() * 18) - 9; // y的变化值
    ballsArr.push(this); // 让自己进入数组
  }
  Ball.prototype.render = function () {
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2 , true);
    ctx.closePath();
    ctx.fillStyle = this.color;
    ctx.fill();
  }
  Ball.prototype.update = function () {
    this.x += this.dx;
    this.y += this.dy;
    this.r --;
    // 如果半径为0此时从数组中删除自己
    if (this.r < 0) {
      this.godie()
    }
  }
  // 自杀
  Ball.prototype.godie = function () {
    for (var i = 0; i < ballsArr.length; i++) {
      if (ballsArr[i] === this) {
        ballsArr.splice(i, 1);
      }
    }
  }
  // 小球数组
  var ballsArr = [];
  // 监听
  myCanvas.onmousemove = function(event) {
    new Ball(event.clientX, event.clientY)
  }
  function draw () {
    window.requestAnimationFrame(draw);
    ctx.clearRect(0, 0, myCanvas.width, myCanvas.height)
    // 渲染、更新所有小球
    for (var i = 0; i < ballsArr.length; i++) {
      ballsArr[i].update();
      // 因为update可能会删除这个小球（半径小于0），所以要验证一下这个小球是否存在
      ballsArr[i] && ballsArr[i].render()
    }
  }
  window.requestAnimationFrame(draw);
```

### canvas游戏开发

同样是无数的静态图片组合，但不同的是，每一帧生成的图片都是通过游戏内部逻辑进行生成的，如：玩家通过鼠标点击一个按钮，游戏内部逻辑会对此事件进行处理，后生成鼠标点击这个行为的图片。
canvas提供的API太基础了，不足以满足游戏特效、性能等需求，[ImpactJS](http://impactjs.com/)
目前相当不错的一款基于canvas的游戏引擎，不管是新手还是资深的开发者，使用这个库都可以快速开发出来游戏。