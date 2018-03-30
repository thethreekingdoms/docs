## 基础信息配置

基础信息配置使用`ttk.spec`API：

```JavaScript
ttk.spec('basic',options);
```
`options`包括以下可配置项：

* `appname`：`String`，项目名称。如果使用[ttk脚手架](_start-scaffold.md)可以在创建项目时指定。`appname`将影响[模块化开发](_modules.md)中异步模块打包文件的命名；
* `source`：`String`，源文件所在一级目录，默认为`src`；
* `output`：`String`，编译输出文件一级目录，默认为`dest`；
* `libs`：`String`，第三方js库、css库文件所在的一级目录，默认为`libs`。
* `deployLibs`：`Boolean`，是否部署`libs`文件，默认为`false`。

> 需要说明的是，`libs`配置项只是为了**临时存放**本地的第三方库文件。ttk对于`libs`的定义是：
> * 不使用npm管理、由单独的`<script>`、`<link>`、`<img>`标签引入的资源，比如`jQuery.js`和`normalize.css`等;
> * 不参与构建。
> 
> 我们认为这类文件应该使用全站统一的CDN路径（比如`//static.ttk.com/common/jquery.js`）引入。所以本地的libs文件只是作为**临时文件**使用，默认也**不会被部署**。如果你想将libs文件一同部署，可以将`deployLibs`设为`true`。

* `limit`：`Object`，配置编译输出文件的体积上限，包括以下子选项：
  * `maxEntrypointSize`：`Number`，入口文件体积上限，默认为150000，即150kb；
  * `maxAssetSize`：`Number`，所有类型文件体积上限，默认为200000，即200kb。

### Example

```JavaScript
ttk.spec('basic',{
  // 项目名称，默认值app
  appname: 'app',
  // 源码目录
  source: './src/',
  // 编译输出目录
  output: './dest/',
  // 第三方库文件目录
  libs: './libs/',
  // 是否部署libs文件，默认关闭
  deployLibs: false,
  // 性能指标
  limit: {
    // 入口文件最大不超过150kb
    maxEntrypointSize: 150000,
    // 所有类型文件最大不超过200kb
    maxAssetSize: 200000
  }
});
```
