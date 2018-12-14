# 策略模式
策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。
## 5.6.2 用策略模式重构表单校验
```
var strategies = {
	isNonEmpty: function (value, errorMsg) { // 不为空
    if (value === '') {
      return errorMsg
    }
	},
  minLength: function (value, length, errorMsg) { // 限制最小长度
    if (value.length < length) {
      return errorMsg
    }
  },
  isMobile: function (value, errorMsg) { // 手机号码格式
    if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) {
      return errorMsg
    }
  }
}
```
接下来我们准备实现validator类。validator类在这里作为context，负责接收用户的请求并委托给strategy对象。在给出validator类的代码之前，有必要提前了解用户是如何向validator类发送请求的，代码如下：
```
var validataFunc = function () {
  var validator = new Validator() // 创建一个validator对象
  // 添加一些校验规则
  validator.add(registerForm.userName, 'isNonEmpty', '用户名不能为空')
  validator.add(registerForm.password, 'minLength', '密码长度不能少于6位')
  validator.add(registerForm.phoneNumber, 'isMobile', '手机号码格式不正确')
  var errorMsg = validator.start() // 获得校验结果
  return errorMsg // 返回校验结果
}
var registerForm = document.getElementById('registerForm')
registerForm.onsubmit = function () {
  var errorMsg = validataFunc() // 如果errorMsg有确切的返回值，说明未通过校验
  if (errorMsg) {
    alert(errorMsg) {
      return false // 阻止表单提交 
    }
  }
}
```