# 单例模式
单例模式的定义是：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

单例模式是一种常用的模式，有一些对象我们往往只需要一个，比如线程池，全局缓存，浏览器中的window对象等。在javaScript开发中，单例模式的用途同样非常广泛。

## 4.1 实现单例模式

要实现一个标准的单例模式并不复杂，无非是用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例是，直接返回之前创建的对象。代码如下：
```
var Singleton = function (name) {
	this.name = name;
  this.instance = null;
}
Singleton.prototype.getName = function () {
  alert(this.name);
}
Singleton.getInstance = function (name) {
  if (!this.instance) {
    this.instance = new Singleton(name);
  }
  return this.instance;
}
var a = Singleton.getInstance('sven1')
var b = Singleton.getInstance('sven2')
alert(a === b); // true
或者：
var Singleton = function (name) {
  this.name = name
}
Singleton.prototype.getName = function () {
  alert(this.name);
}
Singleton.getInstance = (function () {
  var instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  }
})();
```
我们通过Singleton.getInstance来获取Singleton类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的“不透明性”，Singleton类的使用者必须知道这是一个单例类，跟以往通过newXXX的方式来获取对象不同，这里偏要使用Singleton.getInstance来获取对象。
接下来顺便进行一些小测试，来证明这个单例类是可以信赖的；
```
var a = Singleton.getInstance('sven1')
var b = Singleton.getInstance('sven2')
alert(a === b); /// true
```
## 4.2 透明的单例模式
我们现在的目标是实现一个“透明”的单例类，用户从这个类中创建对象的时候，可以像使用其他任何普通类一样。在下面的例子中，我们将使用CreateDiv单例类，他的作用是负责在页面中创建唯一的div节点，代码如下：
```
var CreateDiv = (function () {
  var instance;
  var CreateDiv = function(html) {
    if (instance) {
      return instance
    }
    this.html = html
    this.init()
    return instance = this
  }
  CreateDiv.prototype.init = function () {
    var div = document.createElement('div')
    div.innerHTML = this.html
    document.body.appendChild(div)
  }
  return CreateDiv
})()
var a = new CreateDiv('sven1')
var b = new CreateDiv('sven2')
alert(a === b) // true
```
虽然现在完成了一个透明的单例类的编写，但它同样有一些缺点。
为了把instance封装起来，我们使用了自执行的匿名函数和闭包，并且让这个匿名返回真正的Singleton构造方法，这增加了一些程序的复杂度，阅读起开也不是很舒服。
观察现在的Singleton构造函数：
```
var CreateDiv = function(html) {
    if (instance) {
      return instance
    }
    this.html = html
    this.init()
    return instance = this
  }
```
在这段代码中，CreateDiv的构造函数实际上负责了两件事情。第一是创建对象和执行初始化init方法，第二是保证只有一个对象。虽然我们目前好没有接触过“单一职责原则”的概念，但可以明确的是，这是一种不好的做法，至少这个构造函数看起来很奇怪。
## 4.3 用代理实现单例模式
首先在CreateDiv构造函数中，把负责管理单例的代码移除出去，使它成为一个普通的创建div的类：
```
var CreateDiv = function(html) {
    this.html = html
    this.init()
  }
  CreateDiv.prototype.init = function () {
    var div = document.createElement('div')
    div.innerHTML = this.html
    document.body.appendChild(div)
  }
```
接下来引入代理类proxySingletonCreateDiv:
```
var ProxySingletonCreateDiv = (function () {
  var instance;
  return function (html) {
    if (!instance) {
      instance = new CreateDiv(html)
    }
    return instance
  }
})()
var a = new CreateDiv('sven1')
var b = new CreateDiv('sven2')
alert(a === b)
```
通过引入代理类的方式，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类proxySingletonCreateDiv中。这样一来，CreateDiv就变成了一个普通的类，它跟proxySingletonCreateDiv组合起来可以达到单例模式的效果。
## 4.4 javascript中的单例模式
前面提到的几种单例模式的实现，更多的是接近传统面向对象语言中的实现，单例对象从“类”中创建而来。在以类为中心的语言中，这是很自然的做法。比如在java中，如果需要某个对象，就必须先定义一个类，对象总是从类中创建而来的。
但javascript其实是一门无类(class-free)语言，也正因为如此，生搬单例模式的概念并无意义。在javascript中创建对象的方法非常简单，既然我们只需要一个“唯一”的对象，为什么要为它先创建一个“类”呢？

单例模式的核心是确保只有一个实例，并提供全局访问。
全局变量不是单例模式，但在javascript开发中，我们经常会把全局变量当成单例来使用。
例如：
```
var a = {}
```
当用这种方式创建对象a时，对象确实是独一无二的，如果a变量被声明在全局作用域下，则我们可以在代码中的任何位置使用这个变量，全局变量提供给全局访问是理所当然，这样就满足了单例模式的两个条件。
但是全局变量存在很多问题，它很容易造成命名空间污染。在大中型项目中，如果不加以限制和管理，程序中可能存在很多这样的变量。javascript中变量也很容易被不小心覆盖。
一下几种方式可以相对降低全局变量带来的命名污染。
### 1.使用命名空间
适当地使用命名空间，并不会杜绝全局变量，但可以减少全局变量的数量。
最简单的方法依然是用对象字面量的方式：
```
var namespace1 = {
  a: function () {
    alert(1)
  },
  b: function () {
    alert(2)
  }
}
```
把a和b都定义为namespace1的属性，这样可以减少变量和全局作用域打交道的机会。另外我们还可以动态地创建命名空间，代码如下：
```
var MyApp = {}
MyApp.namespace = function (name) {
  var parts = name.split('.')
  var current = MyApp
  for (var i in parts) {
    if (!current[parts[i]]) {
      current[parts[i]] = {}
    }
    current = current[parts[i]]
  }
}
MyApp.namespace('event')
MyApp.namespace('dom.style')
consoel.dir(MyApp)
// 上述代码等价于：
var MyApp = {
  event: {},
  dom: {
    style: {}
  }
}
```
### 2.使用闭包封装私有变量
这种方法把一些变量封装在闭包的内部，只暴露一些接口跟外界通信：
```
var user = (function () {
  var _name = 'sven'
      _age = 29
      return {
        getUserInfo: function () {
        return _name + '-' + _age
      }
    }
})()
```
我们用下划线来约定私有变量_name和_age，它们被封装在闭包产生的作用域中，外部是访问不到这两个变量的，这就避免了对全局的命名污染
## 4.5 惰性单例
惰性单例指的是在需要的时候才创建对象实例。惰性单例是单例模式的重点，这种技术在实际开发中非常有用。实际上在本章开头就使用过这种技术，instance实例对象总是在我们调用Singleton.getInstance的时候才被创建，而不是在页面加载好的时候就创建，代码如下：
```
Singleton.getInstance = (function () {
  var instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  }
})();
```
## 4.6 通用的惰性单例
用一个变量来标志是否创建过对象，如果是，则在下次直接返回这个已经创建好的对象；
```
var obj
if (!obj) {
  obj = xxx
}
```
现在我们就把如何管理单例的逻辑从原来的代码中抽离出来，这些逻辑被封装在getSingle函数内部，创建对象的方法fn被当成参数动态传入getSingle函数：
```
var getSingle = function (fn) {
  var result
  return function () {
    return result || (result = fn.apply(this, arguments))
  }
}
```
