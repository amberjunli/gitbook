# 插件
插件是webpack的支柱功能。webpack自身也是构建与，你在webpack配置中用到的相同的插件系统之上！插件目的在于解决loader无法实现的其他事。
## 剖析
webpack插件是一个具有apply属性的javascript对象。apply属性会被webpack compiler调用，并且compiler对象可在整个编译生命周期访问。
```
const pluginName = 'ConsoleLogBuildWebpackPlugin'
class ConsoleLogBuildWebpackPlugin {
	apply(compiler) {
    compiler.hooks.run.tap(pluginName, compilation => {
      console.log('webpack 构建过程开始')
    })
  }
}
```
compiler hook的tap方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有hook中复用。
## 用法
由于插件可以携带参数/选项，你必须在webpack配置中，向plugins属性传入new实例。

## 配置
```
const HtmlWebpackPlugin = require('html-webpack-plugin') // 通过npm安装
const webpack = require('webpack') // 访问内置的插件
const path = require('path')
const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(_dirname, 'dist')
  },
  module: {
    rules: [
      test: /.(js|jsx)$/,
      use: 'babel-loader'
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin()
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}
module.exports = config
```
