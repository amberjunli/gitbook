# 入口起点

## 单个入口(简写)语法
用法: entry: string|Array<string>
```
const config = {
	entry: './path/to/my/entry/file.js'
}
module.exports = config
```
entry属性的单个入口语法，是下面的简写
```
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
}
```
当你向entry传入一个数组时会发生什么？向entry属性传入文件路径（file path）数组将创建“多个主入口”在你想要多个依赖文件一起注入，并且将它们的依赖导向到一个chunk时，传入数组的方式就很有用。

## 对象语法
用法： entry: {[entryChunkName: srting]: string|Array<string>}
```
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/venders.js'
  }
}
```
## 常用场景
### 分离应用程序(app)和第三方库(vendor)入口
```
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/venders.js'
  }
}
```
这是什么？从表面上看，这告诉我们webpack从app.js和vendors.js开始创建依赖图。这些依赖图是彼此完全分离、互相独立(每个bundle中都有一个webpack引导)。这种方式比较常见于，只有一个入口起点(不包括vendor)的单页面应用程序中。
为什么？此设置允许你使用CommonsChunkPlugin从应用程序bundle中提取vendor引用到vendor bundle，并把引用vendor的部分替换为_webpack_require_()调用。如果应用程序bundle中没有vendor代码,那么你可以再webpack中实现被称为长效缓存的通用模式。
### 多页面应用程序
```
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index/js'
  }
}
```
这是什么？我们告诉webpack需要3个独立分离的依赖图
为什么？在多页应用中，（每当页面跳转时）服务器将为你获取一个新的HTML文档。页面重新加载新文档，并且资源被重新下载，然而，这给了我们特殊的机会去做很多事：
- 使用CommonChunkPlugin为每个页面间的应用程序共享代码创建bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益