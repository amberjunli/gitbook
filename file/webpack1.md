# webpack
## 概念
本质上，webpack是一个现代javascript引用程序的静态模块打包器(module bundler)。当webpack处理应用程序时，它会递归地构建一个依赖关系图，其中包含引用程序需要的每个模块，然后将所有哲学模块打包成一个或多个bundle。
## 入口
入口起点指示webpack应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为bundles的文件中。
可以通过在webpack配置中配置`entry`属性，来指定一个入口起点（或多个入口起点）。默认值`./src`

`entry`配置的最简单例子
```
module.exports = {
	entry: './path/to/my/entry/file.js'
}
```
# loader特性
- loader支持链式传递。能够对资源使用流水线。一组链式的loader将按照相反的顺序执行。loader链中的第一个loader返回值给下一个loader。在最后一个loader，返回webpack所预期的javascript
- loader可以是同步的，也可以是异步的
- loader运行在node.js中，并且能够执行任何可能的操作
- loader接收查询参数。用于对loader传递配置
- loader也能够使用`options`对象进行配置
- 除了使用`package.json`常用的`main`属性，还可以将普通的npm模块导出为loader,做法是在`package.json`里定义一个`loader`字段
- 插件(plugin)可以为loader带来更多特性
- loader能够产生额外的任意文件

# 模块解析
resolve是一个库，用于帮助找到模块的绝对路径，一个模块可以作为另一个模块的依赖模块，然后被后者引用
```
import foo from 'path/to/module'
// 或者
require('/path/to/module')
```
所依赖的模块可以是来自应用程序代码或第三方的库，resolver帮助webpack找到bundle中需要引入的模块代码，这些代码在包含在每个`require/import`语句中。当打包模块时，`webpack`使用enhanced-resolve来解析文件路径

# webpack中的解析规则
## 绝对路径
```
import '/home/me/file'
import 'C:\\User\\me\\file'
```
## 相对路径
```
import '../src/file'
import './file'
```
# 模块路径
```
import 'module'
import 'module/lib/file'
```
