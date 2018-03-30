## 构建性能优化
ttk集成Webpack过程中封装了一些性能相关的逻辑，包括但不限于以下几点：
* 通过[Rule.include](https://doc.webpack-china.org/configuration/module/#rule-include)限制参与编译的JavaScript文件目录；
* 使用`try{}catch{}`配合`require.resolve`取代`npm list`判断模块是否安装；
* 本地服务器监听文件中排除`node_modules`目录。

除了ttk内部封装的优化手段以外，你也可以通过配置对性能进行优化和定制：
1. 使用webpack生态的一些插件解决工程化的效率问题；
2. 通过[`limit`配置](_config-basic.md)编译输出文件的体积上限，如果超出限制，ttk将会抛出警告，提醒你获取可以重新组织模块的划分结构；
3. 提取[dll模块](_config-js.md)。提取dll后会创建manifest.json文件，再次构建时可以节省大量时间。
