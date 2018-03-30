## Style编译配置

Style编译配置使用`ttk.spec`API：

```JavaScript
ttk.spec('style',options);
```

`options`包括以下可配置项：

* `ext`：`String`，源文件扩展类型，默认为`css`。ttk会根据`ext`类型自动分配对应的编译方案，内置支持以下类型：
  * `css`
  * `less`
  * `scss`
> 其他预编译类型请自行[编写ttk插件](_advance-plugin.md)实现。

* `source`：`String`，style源文件的二级目录名称，相对于`[basic](_config-basic.md).source`。默认为`style`；

* `output`：`String`，编译输出style文件的二级目录名称，相对于`[basic](_config-basic.md).output`。默认为`style`；

* `mainFilePrefix`：`String`，style主文件的命名前缀，默认为`main`。也就是说，默认的style主文件名为`main.[name].[extType]`；

* `useHash`：`Boolean`，编译输出的css文件名是否加上hash指纹，默认为`true`。也就是说，默认的编译输出js文件名为`main.[name].[hash].css`;

* `extract`: `Boolean`；

* `autoprefix`：`Boolean`，是否自动补全前缀，默认为`false`。
> ttk使用postcss实现自动补全前缀，但是可能会引起一些**中间版本**浏览器无法兼容的问题。比如，flex布局在IOS8的Safari下的写法应该是`display:-webkit-box-flex`，而大多数编译工具的自动补全会将其修复为`display: -webkit-flex`。所以，我们不推荐使用编译工具的自动补全功能，而是由开发人员编写预编译mixins实现。

* `sprites`：`false|Object`，配置CSS Sprites自动生成功能。取值为`false`时关闭此功能；取值为`Object`时有以下子配置项：
  * `source`：`String`，指定散列图标的目录。取值可以是项目目录内的路径，比如`./assets/icons`。也可以是文件夹的名称，比如`icons`。生成的Sprites图片输出的目录是[image](_config-image.md)模块指定的`output`;
  * `split`：`Boolean`，是否根据子目录分别编译输出。如果设置为`true`，ttk会根据子目录将散列图标单独打包。比如`icons`内有两个子目录`home`和`auth`，子目录内分别是两个页面对应的散列icon。ttk会编译输出`sprites.<appname>.home.png`和`sprites.<appname>.auth.png`两个雪碧图。其中`appname`是通过[basic配置](_config-basic.md)指定；
  * `retina`：`Boolean`，是否识别多分辨率标识并生成多分辨率sprites图片。如果设置为`true`，ttk可识别以`@2x`形式后缀命名的图片并单独打包。

  > 关于css sprites的实现方案可以参考这篇文章[基于webpack的css sprites实现方案](http://www.caiziguoguo.com/cj21j94e5000uwj0hlbvlisk6/)。

### CSS Sprites规范
如果使用Vue/React等可编写CSS in JS的框架或类似工具，你可能会在JavaScript代码中引用散列的图标文件，然后试图使用ttk将其合并为Sprites图片。比如存在以下源码`app.vue`：
```Vue
<template>
<!-- ... -->
</template>
<script>
export default {};
</script>
<style lang='scss'>
.icon{
  &__home{
    background-image: url('../assets/icons/icon-home.png');
  }
  &__about{
    background-image: url('../assets/icons/icon-about.png');
  }
}
</style>
```

上述代码经ttk构建之后并不能得到正确的Sprites图片。

我们针对类似场景指定了与CSS Sprites相关的一系列规范，如下：
* 声明散列图标的源码应当存放于`main.<appname>.css`中；
* 不会将在Vue/React等源码文件中声明的散列图标合并为Sprites图片。

依照规范将上例进行改进如下：
1. 在`main.app.scss`中声明散列图标如下：
```css
.icon{
    &__home{
      background-image: url('../assets/icons/icon-home.png');
    }
    &__about{
      background-image: url('../assets/icons/icon-about.png');
    }
}
```
2. 在`app.vue`中引入`main.app.scss`:
```Vue
<style lang='scss'>
@import '../style/main.app.scss';
</style>
```
也可以在`main.app.js`中引入：
```JavaScript
import '../style/main.app.scss';
```


### 示例
```JavaScript
ttk.spec('style', {
    // Style文件后缀类型
    ext: 'scss',
    // Style文件目录，相对于basic.source
    source: 'style',
    // Style文件输出目录，相对于basic.output
    output: 'style',
    // 使用hash
    useHash: true,
    // 是否自动补全hack前缀
    autoprefix: false,
    // 主文件前缀
    mainFilePrefix: 'main',
    // 是否启用CSS Sprites自动生成功能
    sprites: {
        // 散列图片目录
        source: 'icons',
        // 是否根据子目录分别编译输出
        split: true,
        // 是否识别retina命名标识
        retina: true
    }
});
```
