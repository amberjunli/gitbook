# 发布-订阅模式
发布-订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知。

把发布-订阅地功能提取出来，放在一个单独的对象内：

```
var event = {
	clientList: [],
	listen: function (key, fn) {
		if (!this.clientList[key]) {
      this.clientList[key] = []
    }
    this.clientList[key].push(fn) // 订阅的消息添加进缓存列表
	},
  trigger: function () {
    var key = Array.prototype.shift.call(argument),
    fns = this.clientList[key] = []
    if (!fns || fns.length === 0) { // 如果没有绑定对应的消息
      return false
    }
    for (var i = 0, fn; fn = fns[i];) {
      fn.apply(this, arguments) // arguments是trigger时带上的参数
    }
  }
}
再定义一个installEvent函数，这个函数可以给所有的对象都动态安装发布-订阅功能：
var installEvent = function (obj) {
  for (var i in event) {
    obj[i] = event[i]
  }
}
```
## 全局的发布-订阅对象
命令模式是最简单和优雅的模式之一，命令模式中的命令指的是一个执行某些特定事情的指令