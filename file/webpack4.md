# 输出
配置output选项可以控制webpack如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置。
## 用法
在webpack中配置output属性的最低要求是，将它的值设置为一个对象，包括以下两点：
- filename用于输出文件的文件名。
- 目标输出目录path的绝对路径。
```
const config = {
	output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
}
module.exports = config
```
此配置将一个单独的bundle.js文件输出到/home/proj/public/assets目录中。
## 多个入口起点
如果配置创建了多个独立chunk(例如，使用多个入口起点或使用像CommonsChunkPlugin这样的插件)，则应该使用占位符来确保每个文件具有唯一的名称。
```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: _dirname + '/dist'
  }
}
// 写入到硬盘： ./dist/app.js, ./dist/search.js
```
## 高级境界
以下是使用CDN和资源hash的复杂实例：
```
output: {
  path: "/home/proj/cdn/assets/[hash]/",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```
在编译时不知道最终输出文件的publicPath的情况下，publicPath可以留空，并且在入口起点文件运行时动态设置。如果你在编译时不知道publicPath,你可以先忽略它，并且在入口起点设置_webpack_public_path_
```
_webpack_public_path_ = myRuntimePublicPath
```