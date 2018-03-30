## Image编译配置

Image编译配置使用`ttk.spec`API：

```JavaScript
ttk.spec('image',options);
```

`options`包括以下可配置项：

* `ext`：`Array`，图片后缀名数组，默认值为`['jpg','jpeg','svg','png','eot','ttf','ico','gif','woff','woff2']`；

* `output`：`String`，编译输出的二级目录，相对于`[basic](_config-basic.md).output`。默认为`'assets'`；

* `base64`：`Boolean`，是否对小体积图片使用base64嵌入，默认为`true`；

* `base64Limit`：`Number`，参与base64编码的文件体积上限，以**byte**为单位，默认为`10000`，即10KB。此配置项单独配置无效，必须在`base64`配置项为`true`时才会生效；

* `useHash`：`Boolean`，编译输出的文件名是否加上hash指纹，默认为`true`。

### 示例
```JavaScript
ttk.spec('image', {
    // 图片后缀类型
    ext: ['png', 'jpg', 'gif', 'jpeg'],
    // 编译输出目录
    output: 'assets',
    // 开启base64检测
    base64: true,
    // 10KB以下的图片将以base64编码的形式嵌入
    base64Limit: 10000,
    // 编译输出文件加上hash指纹
    useHash: true
});
```
