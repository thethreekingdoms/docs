## 多环境支持

ttk支持为不同的环境配置格子的构建、部署、插件等选项。

### 内置环境变量
ttk内置了三种环境：`development`、`testing`和`production`，三者的定义与执行时机如下表：

| 名称 | value | 说明 | 执行时机 | uglify是否可用 | hash是否可用 | sourcemap | 备注 |
| :---: | :---: | --- | :---: | :---: | :---: | --- |
| development | dev     | 开发环境 | serve | N | N | Y | entry包括HMR&livereload模块 |
| testing     | testing | 测试环境 | build&deploy | Y | Y | N | -- |
| production  | prod    | 生产环境 | build&depploy | Y | Y | N | -- |


* `development`：本地开发环境，只在开启本地服务器时生效，所以针对`dev`环境的配置项不会影响最终的编译结果；
* `testing`：测试环境，编译结果可用于上线之前的内部测试；
* `production`：生产环境。

ttk之所以将以上三种环境进行区分，是为了覆盖一个前端项目从始至终的任何一个阶段。`development`环境下，ttk提供本地服务容器和mock服务，方便前端工程师不依赖后端接口进行独立开发；区分`testing`环境和`production`环境是由于前端资源都是静态的，代码写死了就不会根据运行环境改变。最常见的场景是：

* 接口地址与主站是异域的，进行跨域请求时需要携带域名的接口地址；
* 测试环境与生产环境的接口域名不相同；
* 测试完毕之后，如果需要提交上线，必须修改js代码中的接口地址。

最麻烦的是最后一步，要么人工手动修改，要么通过工具自动修改。即使使用工具，也需要手动修改工具的配置项。所以不论哪一种方式，都存在不必要的手动操作，不仅麻烦，而且容易出错。

ttk对以上场景的解决方案是：结合环境配置和[JavaScript的define功能](_config-js.md)，为不同环境配置不同的接口地址。比如：

```JavaScript
ttk.spec('js',{
  dev: {
    define: {
      LOGIN_API: '/login'
    }
  },
  testing: {
    define: {
      LOGIN_API: '//passport.test.com/login'
    }
  },
  prod: {
    define: {
      LOGIN_API: '//passport.app.com/login'
    }
  }
});
```

以上配置的表现效果为：

* `dev`环境下将login接口映射到本地，通过本地服务器的Mock服务进行开发调试；
* 执行`ttk build -e testing`，根据`testing`环境的配置项，编译输出的文件中login接口被替换为`'//passport.test.com/login'`；
* 执行`ttk build -e prod`，根据`production`环境的配置项，编译输出的文件中login接口被替换为`'//passport.app.com/login'`。

### 自定义环境变量
对于复杂的项目而言可能不止需要三种环境，比如有些产品需要仿真环境、灰度环境等。除了内置的三种环境以外，还可以通过`ttk.envs()`API扩展自定义环境：
```JavaScript
ttk.envs(envs);
```

* `envs`，`Array`，代表自定义的环境变量。

比如存在以下配置项：
```JavaScript
ttk.envs(['grey','sim']);
```

此时ttk可用的环境变量便扩展到5个：
* `dev`
* `testing`
* `prod`
* `grey`
* `sim`

接下来你可以针对这五种环境分别进行配置。

> 与`ttk.use()`一样，`ttk.envs()`在`ttk-conf.js`中的位置并不影响它的解析顺序，你可以将其置于任意位置。但是为了代码的整洁度和可读性，建议将其置于`ttk-conf.js`的最顶端。