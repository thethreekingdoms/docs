## 构建

[![GitHub stars](https://img.shields.io/github/stars/thethreekingdoms/ttk-compiler.svg?style=social&label=Stars)](https://github.com/thethreekingdoms/ttk-compiler)
[![GitHub forks](https://img.shields.io/github/forks/thethreekingdoms/ttk-compiler.svg?style=social&label=Fork)](https://github.com/thethreekingdoms/ttk-compiler)

执行如下命令查看完整的build命令
```bash
$ ttk build -h
  
  Usage: build|compile [options]

  run build progress

  Options:
    -e, --env [env]  specify build environment
    -i, --init       init all configurations and install plugins&dependencies
    -h, --help       output usage information

  Examples:
    $ ttk build -e testing
```

`Options`:
* `-e`/`--env`指定构建的目标环境，默认为`testing`。ttk会根据`<env>`指定的环境变量解析`ttk-conf.js`中的配置选项。虽然`<env>`有默认值，但是强力建议保持此选项开启，否则可能会出现构建产出的代码与部署环境不符；
* `-i`/`--init`是否初始化项目对应的ttk配置，并且安装ttk插件以及构建所需的依赖模块，默认为`false`。在初始化项目之后或者修改了如下配置之后必须开启此选项，否则会找不到依赖模块从而造成构建失败：
    * `style`-`ext`类型/开启`autoprefix`/开启`sprites`;
    * `html`-`engine`类型;

### 别名
你也可以使用`compile`取代`build`：
```bash
ttk compile -e prod -i
```

### 构建配置
ttk构建的产出与以下配置相关：
* [项目基本信息配置](_config-basic.md)；
* [JavaScript编译配置](_config-js.md)；
* [Style编译配置](_config-style.md)；
* [Html编译配置](_config-html.md)；
* [Image编译配置](_config-image.md)；
* [部署相关配置](_deploy.md);
* [ttk插件配置](_plugins.md)。