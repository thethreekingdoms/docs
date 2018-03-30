## Html编译配置

Html编译配置使用`ttk.spec`API：

```JavaScript
ttk.spec('html',options);
```

`options`包括以下可配置项：

* `ext`：`String`，源文件扩展类型，默认为`html`；

* `engine`： `String`，指定Html模板引擎类型，默认为`html`。ttk封装了Html、Pug、Ejs和Mustache对应的编译方案，其他模板引擎需自行[编写ttk插件](_advance-plugin.md)实现；

* `source`：`String`，源码html文件目录，相对于`[basic](_config-basic.md).source`。默认为`/`；

* `output`：`String`，编译输出html文件目录，相对于`[basic](_config-basic.md).output`。默认为`/`；

* `mainFilePrefix`：`String`，html文件的命名前缀，默认为`index`。也就是说，默认的html入口文件名为`index.[name].html`；

* `removePrefixAfterBuilt`:`Boolean`，编译输出的html文件是否去除`index`前缀，默认为`true`。比如一个[多页面项目](_multipage.md)中存在两个index文件`index.home.html`和`index.about.html`，开启`removePrefixAfterBuilt`编译输出的文件名分别为`home.html`和`about.html`，否则保持与源文件同名；

* `urlTimestamp`：`Boolean`，是否在静态资源url后加上时间戳（比如`//static.app.com/common.js?t=1476183177875`），默认为`false`。此配置项是处理浏览器缓存的一种方案，理想方案是使用文件hash指纹，而不是url query。开启此选项后不论项目中的静态资源是否更新，其Url都会加上时间戳query。
> 此选项是为了迁就部分仍然使用覆盖更新策略的团队，不推荐开启。

* `staticLocateMode`：`String`，[多页面项目](_multipage.html)采用的资源定位策略，可选值`loose`和`strict`，默认为`loose`。单页面项目此配置项无效，具体细节请参阅多页面项目的[资源定位](_multipage-location.md)章节；

* `renderData`：`String`，服务端渲染（Server Side Render）所需的初始数据，默认为`null`。

### SSR-服务端渲染
在使用ttk内置模板引擎（Pug/Ejs/Mustache）的前提下，ttk支持SSR并可以通过配置`renderData`所需数据，比如有以下配置：
```JavaScript
ttk.spec('html', {
    // 源文件后缀类型，默认为html
    ext: 'html',
    // 模板引擎，默认为html
    engine: 'ejs',
    // Html文件目录，相对于basic.source
    source: './',
    // Html文件编译输出目录，相对于basic.output
    output: './',
    // 模板入口文件的前缀，入口文件的命名规则为[mainFilePrefix].*.[ext]
    mainFilePrefix: 'index',
    // 静态资源js&css的url是否加上query时间戳
    urlTimestamp: false,
    // SSR data
    renderData: {
        content: 'Hello World'
    }
});
```

同时项目中存在`index.ejs`内容如下：
```ejs
<html>
    <head>
    </head>
    <body>
        <div><%= content %></div>
    </body>
</html>
```

执行`ttk build`之后产出的`index.html`内容如下：
```html
<html>
    <head>
    </head>
    <body>
        <div>Hello World</div>
    </body>
</html>
```

**需要注意的是**，`renderData`配置的数据将会被传入所有的html模板，[多页面项目](_multipage.md)中需要注意区分。
