# 模板方法模式
## 11.1 模板方法模式的定义和组成

模板方法模式是一种只需使用继承就可以实现的非常简单的模式。

模板方法模式由两部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。

假如我们有一些平行的子类，各个子类之间有一些相同的行为，也有一些不同的行为。如果相同和不同的行为都混合在各个子类的实现中，说明这些相同的行为会在各个子类中重复出现。但实际上，相同的行为可以被搬移到另外一个单一的地方，模板方法模式就是为解决这个问题而生的。在模板方法模式中，子类实现中的相同部分被上移到父类中，而将不同的部分留待子类来实现。这也很好地体现了泛化的思想。

```
泡咖啡
var Coffee = function () {}
Coffee.prototype.boilWater = function () {
  console.log('把水煮沸')
}
Coffee.prototype.brewCoffeeGriends = function () {
  console.log('用沸水冲泡咖啡')
}
Coffee.prototype.pourInCup = function () {
  console.log('把咖啡倒进杯子')
}
Coffee.prototype.addSugarAndMilk = function () {
  consoel.log('加糖和牛奶')
}
Coffee.prototype.init = function () {
  this.boilWater()
  this.brewCoffeeGriends()
  this.pourInCup()
  this.addSugarAndMilk()
}
var coffee = new Coffee()
coffee.init()
``````
泡茶
var Tea = function () {}
Tea.prototype.boilWater = function () {
  console.log('把水煮沸')
}
Tea.prototype.steepTeaBag = function () {
  console.log('用沸水浸泡茶叶')
}
Tea.prototype.pourInCup = function () {
  console.log('把茶水倒进杯子')
}
Tea.prototype.addLemon = function () {
  consoel.log('加柠檬')
}
Tea.prototype.init = function () {
  this.boilWater()
  this.steepTeaBag()
  this.pourInCup()
  this.addLemon()
}
var tea = new Tea()
tea.init()
```
 ## 分离出共同点
 ```
var Beverage = function () {}
Beverage.prototype.boilWater = function () {
  console.log('把水煮沸')
}
Beverage.prototype.brew = function () {} // 空方法，应该由子类重写
Beverage.prototype.pourInCup = function () {} // 空方法，应该由子类重写
Beverage.prototype.addCondiment = function () {} // 空方法，应该由子类重写
Beverage.prototype.init = function () {
  this.boilWater()
  this.brew()
  this.pourInCup()
  this.addCondiments()
}
```
## 创建Coffee子类和Tea子类
```
var Coffee = function () {}
Coffee.prototype = new Beverage()
Coffee.prototype.brewCoffeeGriends = function () {
  console.log('用沸水冲泡咖啡')
}
Coffee.prototype.pourInCup = function () {
  console.log('把咖啡倒进杯子')
}
Coffee.prototype.addSugarAndMilk = function () {
  consoel.log('加糖和牛奶')
}
var coffee = new Coffee()
coffee.init()
```

## 抽象类
首先要说明的是，模板方法模式是一种严重依赖抽象类的设计模式。javascript在语言层面并没有提供对抽象类的支持，我们也很难模拟抽象类的实现。
## 11.3.1 抽象类的作用
