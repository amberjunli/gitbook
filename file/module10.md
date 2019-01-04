# 装饰者模式
装饰者模式将一个对象嵌入另一个对象之中，实际上相当于这个对象被另一个对象包装起来，形成一条包装链，请求随着这条链依次传递到所有的对象，每个对象都有处理这条请求的机会。
## 回到javascript的装饰者
javascript语言动态改变对象相当容易，我们可以直接改写对象或者对象某个方法，并不需要使用"类"来实现装饰者模式，
```
var plane = {
	fire: function () {
    console.log('发射普通团子弹')
  }
}
var missileDecorator = function () {
  cobnsole.log('发射导弹')
}
var atomDecorator = function () {
  console.log('发射原子弹')
}
var fire1 = plane.fire
plane.fire = function () {
  fire1()
  missileDecorator()
}
var fire2 = plane.fire
plane.fire = function () {
  fire2()
  atomDecorator()
}
plane.fire()
// 分别输出： 发射普通子弹、发射导弹、发射原子弹
```
## 装饰函数
在javascript中，几乎一切都是对象，其中函数又被称为一等对象。在平时的开发工作中，也许大部分时间都在和函数打交道，在javascript中可以很方便地给某个对象扩展属性和方法，但却很难再不该能某个函数原地阿妈的情况下，给该函数添加一些额外的功能。在代码的运行期间，去哦们很难切入某个函数的执行环境。
