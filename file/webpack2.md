# 概念
本质上，webpack是一个现代javascript应用程序的静态模块打包器。当webpack处理应用程序时，它会递归地构建一个依赖关系图，其中包含应用程序需要的每个模块，然后将这些模块打包成一个或多个bundle.

## 入口
入口起点只是webpack应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack会找出有哪些模块和库是入口起点(直接和间接)依赖的。
每个依赖项随即被处理，最后输出到称之为bundles的文件中，可以通过在webpack配置中配置entry属性，来指定一个入口起点(或多个入口起点)。默认值为./src。
webpack.config.js
```
module.export = {
	entry: './path/to/my/entry/file.js'
}
```
## 出口
output属性告诉webpack在哪里输出它所创建的bundles,以及如何命名这些文件，默认值为./dist。基本上，整个引用程序结构，都会被编译到你指定的输出路径的文件夹中。
```
const path = require('path')
module.export = {
  entry: './path/to/my/entry/file.js'
  output: {
    path: path.resolve(_dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
}
```
在上面的示例中，我们通过output.filename和output.path属性，来告诉webpack bundle的名称，以及我们想要bundle生成(emit)到哪里。
## loader
loader让webpack能够去处理那些非javascript文件(webpack自身只理解javascript)。loader可以将所有类型的文件转换为webpack能够处理的有效模块，然后你就可以利用webpack的打包能力，对它们进行处理。
本质上，webpack loader将所有类型的文件，转换为应用程序的依赖图(和最终的bundle)可以直接引用的模块。

注意，loader能够import导入任何类型的模块(例如.css文件)，这是webpack特有的功能，其他打包程序或任务执行器的可能并不支持。

在更高层面，在webpack的配置中loader有两个目标：

1. test属性，用于标识出应该被对应的loader进行转换的某个或某些文件。
2. use属性，表示进行转换时，应该使用哪个loader.
```
const path = require('path')
const config = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      {test: /\.txt$/, user: 'raw-loader'}
    ]
  }
}
module.exports = config
```
以上配置中，对一个单独的module对象定义了rules属性，里面包含两个必要属性：test和use。这告诉webpack编译器如下信息：
“webpack编译器，当你碰到在require()/import语句中被解析为'.txt的路径'时，在你对它打包之前，先使用raw-loader转换一下。”

## 插件
loader被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量，插件接口功能极其强大，可以用来处理各种各样的任务。
想要使用一个插件，你只需要require()它，然后把它添加到plugins数组中。多数插件可以通过选项自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这是需要通过使用new操作符来创建它的一个实例。
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
const webpack = require('webpack') // 用于访问内置插件
const config = {
  module: {
    rules: [
      {test: /\.txt$/, use: 'raw-loader'}
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}
module.exports = config
```
## 模式
通过选择development或production之中的一个，来设置mode参数，你可以启用相应模式下的webpack内置的优化
```
module.exports = {
  mode: 'production'
}
```