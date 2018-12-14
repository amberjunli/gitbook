# 命令模式

应用场景：
有时候需要向某些对象发送请求，但是并不知道请求的接收者实收，也不知道被请求的操作是什么，此时希望用一种松耦合的方式来设计软件，使得请求发送者和请求者能够消除彼此之间的耦合关系
接下来定义setCommand函数，setCommand函数负责往按钮上面安装命令。点击按钮会执行某个command命令，执行命令的动作被约定为调用command对象的execute()方法。虽然还不知道这些命令究竟代表什么操作，但负责绘制按钮的程序猿不关心这些事情，他只需要预留好安装命令的接口，commond对象自然知道如何和正确的对象沟通
```
var setCommand = function (button,command) {
	button.onclick = function () {
  command.execute()
  }
}
```
最后，负责编写点击按钮之后的具体行为的程序员总算交上了他们的成果，他们完成了刷新菜单界面，增加子菜单和删除子菜单这几个功能，这几个功能被分布在MenBar和SubMenu这两个对象中：
```
var MenuBar = {
  refresh: function () {
    console.log('刷新菜单目录')
  }
}
var SubMenu = {
  add: function () {
    console.log('增加子菜单')
  },
  del: function () {
    console.log('删除子菜单')
  }
}
在让button变得有用起来之前，我们要先把这些行为都封装在命令类中
var RefreshMenuBarCommand = function (receiver) {
  this.receiver = receiver
}
RefreshMenuBarCommand.prototype.execute = function () {
  this.receiver.refresh()
}
var AddSubMenuCommand = function (receiver) {
  this.receiver = receiver
}
AddSubMenuCommand,prototype.execute = function () {
  this.receiver.add()
}
var DelSubMenuCommand = function (receiver) {
  this.receiver = receiver
}
DelSubMenuCommand = function () {
  console.log('删除子菜单')
}
最后就是把命令接受者传入到command对象中，并且把command对象安装到button上面：
var refreshMenuBarCommand = new RefreshMenuBarCommand(MenuBar)
var addSubMenuCommand = new AddSubMenuCommand(SubMenu)
var delSubMenuCommand = new DelSubCommand(SubMenu)
setCommand(button1,refreshMenuBarCommand)
setCommand(button2,addSubMenuCommand)
setCommand(button3,delSubMenuCommand)
```

## 9.3 javascript中的命令模式
也许我们会感到很奇怪，所谓的命令模式，看起来就是给对象的某个方法取了execute的名字，引入command对象和receiver这两个无中生有的角色无非是把简单的事情复杂化了，即使不用什么模式，用下面的代码就可以实现相同的功能
```
var bindClick = function (button, func) {
  button.onclick = func
}
var MenuBar = {
  refresh: function () {
    console.log('刷新子菜单')
  }
}
var SubMenu = {
  add: function () {
    console.log('增加子菜单')
  }，
  del: function () {
    console.log('删除子菜单')
  }
}
bindClick(button1, MenuBar.refresh)
bindClick(button2, SubMenu.add)
bindClick(button3, SubMenu.del)
```
这种说法是正确的，9.2节中的实例代码是模拟传统面向对象语言的命令模式实现。命令模式将过程式的请求调用封装在command对象的excute方法里，通过封装方法调用，我们可以把运算块包装成形。command对象可以被四处传递，所以在调用命令的时候，客户不需要关心事情是如何进行的。
命令模式的由来，其实是回调（callback）函数的一个面向对象的替代品。
javascript作为将函数作为一等对象的语言，跟策略模式一样，命令模式也早已融入到javascript语言之中。运算块不一定要封装在command.excute方法中，也可以封装在普通函数中。函数作为一等对象，本身就可以被四处传递。即使我们依然需要请求“接收者”，那也未必使用面向对象的方式，闭包可以完成同样的功能。
在面向对象设计中，命令模式的接收者被当成command对象的属性保存起来，同时约定执行命令的操作调用command.execute方法。在使用闭包的命令模式实现中，接收者被封闭在闭包产生的环境中，执行命令的操作可以更加简单，仅仅执行命令的时候，接收者都能被顺利访问。用闭包实现的命令模式：
```
var setCommand = function (button, func) {
  button.onclick = function () {
    func()
  }
}
var MenuBar = {
  refresh: function () {
  console.log('刷新菜单界面')
  }
}
var RefreshMenuBarCommand = function (receiver) {
  return function () {
    receiver.refresh()
  }
}
var refreshMenuBarCommand = RefreshMenuBarCommand(MenuBar)
setCommand(button1, refreshMenuBarCommand)
```