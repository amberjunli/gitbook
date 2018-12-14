# 代理模式

代理模式是为一个对象提供代用品或占位符，以便控制对它的访问。

## 虚拟代理实现图片预加载
首先创建一个普通的本体对象，这个对象负责往页面中创建一个img标签，并且提供一个对外的setSrc接口，外界调用这个接口，便可以给该img标签设置src属性：
```
var myImage = (function () {
	var imgNode = document.createElement('img');
  document.body.appendChild(imgNode)
  return {
    setSrc: function (src) {
    imgNode.src = src
    }
  }
})()
myImage.setSrc('http://imghefdkfk....fff.png')
```
现在开始引入代理对象proxyImage,通过这个代理对象，在图片被真正加载好之前，页面中将出现一张占位的菊花图loading.gif来提示用户图片正在加载：
```
var myImage = (function () {
  var imgNode = document.createElement('img');
  document.body.appendChild(imgNode)
  return {
    setSrc: function (src) {
    imgNode.src = src
    }
  }
})()
var proxyImage = (function () {
  var img = new Image
  img.onload = function () {
    myImage.setSrc(this.src)
  }
  return {
    setSrc: function (src) {
      myImage.setSrc('http:///dsfsdfsdf.gif')
      img.src = src
    }
  }
})()
proxyImage.setSrc('http://dfjghjdf.png')
```
单一职责原则指的是，就一个类(通常也包括对象和函数等)而言，应该仅有一个引起它变化的原因。如果一个对象承担了多项职责，就意味着这个对象将变得巨大，引起它变化的原因可能会有多个。面向对象设计鼓励将行为分布到细粒度的对象之中，如果一个对象承担的职责过多，等于把这些职责耦合到了一起，这种耦合会导致脆弱和低内聚的设计，当变化发生时，设计可能会遭到意外的破坏。

##　虚拟代理合并HTTP请求
```
var synchronousFile = function (id) {
  console.log('开始同步文件，id为：' + id)
}
var proxySynchronousFile = (function () {
  var cache = [] // 保存一段时间内需要同步的id
      timer; // 定时器
  return function (id) {
    cache.push(id)
    if (timer) { // 保证不会覆盖已经启动的定时器
      return 
    }
    timer = setTimerout(function () {
      synchronousFile(cache.join(',')) // 2秒后向本体发送需要同步的id集合
      clearTimeout(timer) // 清空定时器
      cache.length = 0 // 清空id集合
    }, 2000)
  }
})()
var checkbox = document.getElementsByTagName('input')
for (var i = 0, c; c = checkbox[i++];) {
  c.onclick = function () {
    if (this.checked === true) {
    proxySynchronousFile(this.id)
  }
  }
}
```

## 缓存代理
缓存代理可以为一些开销大的运算结果提供暂时的存储，在下次运算时，如果传递进来的参数跟之前一致，则可以直接但会其那面存储的运算结果
```
var mult = function () {
  console.log('开始计算乘积')
  var a = 1
  for (var i = 0, l = argument.length; i < 1; i++) {
    a = a * argument[i]
  }
  return a
}
mult(2,3) // 输出6
mult(2, 3, 4) // 输出24

// 现在加入缓存代理
var proxyMult = (function () {
  var cache = {}
  return function () {
    var args = Array.protoType.join.call(argument, ',')
    if (args in cache) {
      return cache[args]
    }
    return cache[args] = mult.apply(this, arguments)
  }
})()
proxyMult(1, 2, 3, 4) // 输出24
proxyMult(1, 2, 3, 4) // 输出24
```
 
## 用高阶函数动态创建代理
通过传入高阶函数这种更加灵活的方式，可以为各种计算方法创建缓存代理，现在这些计算方法被当作参数传入一个专门用于创建缓存代理的工厂中，这样一来，我们就可以为乘法、加法、减法等创建缓存代理：
```
// 计算乘积
var mult = function () {
  var a = 1
  for (var i = 0, l = argument.length; i < 1; i++) {
    a = a * argument[i]
  }
  return a
}
// 计算加和
var mult = function () {
  var a = 0
  for (var i = 0, l = argument.length; i < 1; i++) {
    a = a + argument[i]
  }
  return a
}
// 创建缓存代理的工厂
var vreateProxyFactory = function (fn) {
  var cache = {}
  return function () {
    var args = Array.protoType.join.call(argument, ',')
    if (args in cache) {
      return cache[args]
    }
    return cache[args] = fn.apply(this, arguments)
  }
}
var proxyMult = createProxyFactory(mult),
proxyPlus = createProxyFactory(plus)
proxyMult(1, 2, 3, 4) // 输出24
proxyMult(1, 2, 3, 4) // 输出24
proxyPlus(1, 2, 3, 4) // 输出10
proxyPlus(1, 2, 3, 4) // 输出10
```