# loader
loader用于对模块的源代码进行转换。loader可以使你在import或加载模块时预处理文件。因此，loader类似于其他构件工具中任务，并提供了处理前端构建步骤的强大方法。loader可以将文件从不同的语言(typescript)转换为javascript，或将内联图像转换为dataURL。loader甚至允许你直接在javascript模块中import CSS文件。
## 示例
例如，你可以使用loader告诉webpack加载css文件，或者将typescript转为javascript。为此，首先安装相对应的loader:
```
npm install --save-dev css-loader
npm install --save-dev ts-loader
```
然后指示webpack对每个.css使用css-loader，以及对所有.ts文件ts-loader:
```
module.exports = {
	module: {
    rules: [
      {test: /\.css$/, use: 'css-loader'},
      {test: /\.ts$/, use: 'ts-loader'},
    ]
  }
}
```
## 使用loader
在你的应用程序中，有三种使用loader的方式：
配置：在webpack.config.js文件中指定loader
内联：在每个impor语句中显式指定loader
CLI：在shell命令中指定它们
## 配置
module.rules允许你在webpack配置中指定多个loader。这是展示loader的一种简明方式，并且有助于使用代码变得简洁。同时让你对各个loader有个全局概览：
```
module: {
  rules: [
    {
      test: /\.css$/,
      use: [
        {loader: 'style-loader'},
        {
          loader: 'css-loader',
          options: {
            modules: true
          }
        }
      ]
    }
  ]
}
```
## 内联
可以在import语句或任何等效于import的方式中指定loader。使用！将资源中的loader分开。分开的每个部分都相对当前目录解析。
```
import Styles from 'style-loader!css-loader?modules!./styles.css'
```
通过前置所有规则及使用！，可以对应覆盖到配置中的任意loader.
选项可以传递查询参数，例如?key=value&foo=bar,或者一个JSON对象，例如?{"key": "value", "foo": "bar"}
## loader特性
- loader支持链式传递。能够对资源使用流水线。一组链式的loader将按照相反的顺序执行。loader链钟的第一个loader返回值给下一个loader。在最后一个loader，返回webpack所预期的javascript。
- loader可以使同步的，也可以是一步的。
- loader运行在node.js中，并且能够执行任何可能的操作。
- loader接收查询参数。用于对loader传递配置
- loader也能够使用options对象进行配置。
- 除了使用package.json常见的main属性，还可以将普通的npm模块导出为loader，做法是在package.json里定义一个loader字段。
- 插件(plugin)可以为loader带来更多特性。
- loader能够产生额外的任意文件。