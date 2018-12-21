# 配置
很少有webpack配置看起来很完全相同。这是因为webpack的配置文件，是导出一个对象的javascript文件。此对象，由webpack根据对象定义的属性进行解析。

因为webpack配置是标准的node.js CommonJS模块，你可以做到以下事情：
- 通过require(...)导入其他文件
- 通过require(...)使用npm的工具函数
- 使用javascript控制流表达式，例如?:操作符
- 对常用值使用常量或变量
- 编写并执行函数来生成部分配置

虽然技术上可行，但应避免以下做法：
- 在使用webpack命令行接口(CLI)(应该编写自己的命令行接口或使用 --evn)时，访问命令行接口参数。
- 导出不确定的值(调用webpack两次应该产生同样的输出文件)
- 编写很长的配置(应该将配置拆分为多个文件)

## 基础配置
```
var path = require('path')
module.exports = {
	mode: 'development',
	entry: './foo.js',
	output: {
    path: path.resolve(_dirname, 'dist'),
    filename: 'foo.bundle.js'
  }
}
```