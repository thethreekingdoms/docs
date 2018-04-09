## 模块化开发

TTK的前端构建内核为Webpack，所以支持的模块化规范类型与Webpack相同，包括ES6 Modules、CommonJS和AMD。

### 按需加载
按需加载是前端优化很重要的一点，将当前页面暂不需要的js/css等资源单独打包成一个文件，当需要使用它们的时候（比如scroll到对应位置）再动态加载到客户端。TTK前端代码支持以下几种动态加载方案：
* `require.ensure`；
* `import()`函数；
* AMD

#### require.ensure
`require.ensure`是Webpack提供加载动态模块的API，可以搭配E6 Module和CommonJS规范使用（由于AMD规范通常已经占用了`require`命名空间，所以可能会与`require.ensure`产生冲突。并且AMD自身便具备动态加载功能，无需额外支持）。比如存在以下代码：
```JavaScript
import ModuleA from './part/module.a.js';

window.onload = function() {
    ModuleA();
    document.body.onclick = function() {
        // 第三个参数是chunk name，决定编译打包的文件名称
        require.ensure([], (require) => {
            const ModuleB = require('./part/module.b');
            ModuleB();
        },'async');
    }
};
```

异步模块`module.b.js`将会被编译为：
```bash
app.async.36a23b99.js    1.49 kB       1  [emitted]
```

#### import()函数
`import()`是被[ECMAScript提案](https://github.com/tc39/proposal-dynamic-import)专门用于实现动态加载的函数，目前已经处于Stage-3，基本可以确定会被加入未来的正式规范中。目前babel和webpack均已支持。

`import()`的缺陷是无法定义异步文件的名称（上述示例中的`async`），针对这一缺陷Webpack提供了弥补方案：使用特殊注释进行定义。如下：
```JavaScript
import ModuleA from './part/module.a.js';

window.onload = function() {
    ModuleA();
    document.body.onclick = import(/* webpackChunkName: "aysnc" */'./part/module.b').then(moduleB => {
        moduleB();
    }).catch(err => {
        throw new Error(err);
    });
};
```

构建输出的结果与`require.ensure`一致：
```bash
app.async.36a23b99.js    1.49 kB       1  [emitted]
```

> `import()`函数加载异步文件后返回一个Promise，所以需要客户端支持Promise或者使用polyfill。

#### AMD
AMD是对CommonJS的一种实现，它提供了浏览器的模块化runtime。AMD本身便具备动态加载的功能，以[require.js](http://www.requirejs.org/)为例：
```JavaScript
require(['./part/module.a.js'],function(moduleA){
    window.onload = function() {
        ModuleA();
        document.body.onclick = function(){
            require(['./part/module.b.js'], function(moduleB) {
                moduleB();
            });
        }
    };
})
```

AMD与`import()`有同一个缺陷：无法定义异步文件的名称，并且Webpack的特殊注释也并不适用于AMD，所以最终构建输出的结果如下：
```bash
1.36a23b99.js    1.49 kB       1  [emitted]
```

其中`1`是`chunk id`，由Webpack自动生成，开发者无法干预。

从可移植性、语法的整洁度、构建结果的语义性以及未来可期等方面综合考虑，`import()`是目前实现动态加载最佳的方案。
