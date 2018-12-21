# 模块解析
resolver是一个库，用于帮助找到模块的据对路径。一个模块可以作为另一个模块的依赖模块，然后被后者引用。
```
import foo from 'path/to/module'
// 或者
require('path/to/module')
```
所依赖的模块可以是来自应用程序或第三方的库。resolver帮助找到bundle中需要引入的模块代码，这些代码在包含在每个require/import语句中。当打包模块式，webpack使用enhanced-resolve来解析文件路径
