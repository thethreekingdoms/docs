## JavaScript编译配置

JavaScript编译配置使用`ttk.spec`API：

```JavaScript
ttk.spec('js',options);
```

`options`包括以下可配置项：

* `ext`：`String`，源文件扩展类型，默认为`js`；

* `source`：`String`，js源文件的二级目录名称，相对于`[basic](_config-basic.md).source`。默认为`js`；

* `output`：`String`，编译输出js文件的二级目录名称，相对于`[basic](_config-basic.md).output`。默认为`js`；

* `mainFilePrefix`：`String`，js主文件的命名前缀，默认为`main`。也就是说，默认的js主文件名为`main.[name].[ext]`；
> ttk会将js主文件作为编译入口，所以主文件的命名规则必须严格遵守，否则会导致找不到编译入口文件。

* `uglify`：`Boolean`，编译输出的js文件内容是否混淆压缩，默认为`true`；

* `useHash`：`Boolean`，编译输出的js文件名是否加上hash指纹，默认为`true`。也就是说，默认的编译输出js文件名为`main.[name].[hash].js`;

* `asyncModuleHash`：`Boolean`，编译输出的异步模块js文件名是否加上hash指纹，默认为`true`。也就是说，默认编译输出的异步js文件名为`[appname].[moduleName].[hash].js`；
> 更多细节请查阅[模块化开发](_modules.md)；

* `splitCommonModule`：`Boolean`，是否提取公共模块并将其单独打包成一个文件，默认为`true`。开启此选项后ttk将提取Webpack runtime编译为独立的`common.[appname].[hash].js`。如果配置了`files.common`不为空，则其声明的模块也会被打包；

* `splitDllModule`：`Boolean`，是否提取公共第三方模块并将其单独打包成一个文件，默认为`false`。开启此选项需要与`files.common`配合，ttk将提取`files.common`声明的第三方模块并编译为独立的`[appname].dll.js`；
> `splitDllModule`与`splitCommonModule`并不是重复的两个选项。
> * `splitCommonModule`提取的公共部分包括Webpack runtime，其中有与业务逻辑相关的代码，比如模块路径信息。也就是说`common.[appname].[hash].js`的hash会跟随业务代码的改变而改变。
> * `splitDllModule`的目标是提取*第三方模块*，比如`vue`。这些模块与业务逻辑无关，甚至可能在项目存活期间不会进行版本的迭代，可以被客户端长久缓存，这也是`[appname].dll.js`没有hash指纹的主要原因。
> 
> 需要注意的是，如果多页项目中提取dll文件，则跟commmon文件一样需要在html文档中手动声明。详情请参阅[多页面开发](_multipage.md)

* `lint`：`Boolean`，是否进行代码规范测试，默认为`true`。此选项只在运行`ttk build`命令是可用，开启后ttk将会在项目根目录下创建`.eslintrc`文件（需执行`ttk build -i`或`ttk serve -i`），并使用[eslint](http://eslint.cn)对源码进行规范测试；

* `define`：`Object`，定义编译过程需要替换的变量。通过此配置项，可以定义将源代码中指定的变量在编译之后替换为指定的对应内容。比如有如下ttk配置：

    ```JavaScript
    ttk.spec('js',{
      define: {
        API: '/login'
      }
    });
    ```

    源码中有以下代码：

    ```JavaScript
    $.ajax({
      url: API,
      dataType: 'jsonp',
      success: function(res){
        // success
      }
    });
    ```

    执行`ttk build`之后，编译输出的文件如下：

    ```JavaScript
    $.ajax({
      url: '/login',
      dataType: 'jsonp',
      success: function(res){
        // success
      }
    });
    ```

    源码中的`API`经编译后被替换为`/login`。

    结合`define`配置项和[多环境支持](_envs.md)，可以针对不同的环境自动生成对应的内容。比如有以下ttk配置：

    ```JavaScript
    ttk.spec('js',{
      dev: {
        define: {
          API: '/login'
        }
      },
      testing: {
        define: {
          API: '//passport.test.ttk.com/login'
        }
      },
      prod: {
        define: {
          API: '//passport.ttk.com/login'
        }
      }
    });
    ```

    * `dev`为本地开发环境，将`API`配置为本地接口以便使用[Mock服务](_mock.md)；
    * `testing`为测试环境，将`API`配置为测试服务器接口；
    * `prod`为生产环境，将`API`配置为线上服务器接口。

    > 关于编译环境的详细配置请参阅[多环境支持](_envs.md)。

* `files`：`Object`，指定需要编译的js主文件和待提取的公共模块。如果不配置此选项，ttk会自动查找**所有**符合命令规则的js文件作为入口文件，如果你只想编译某些指定的文件，可以使用此配置项指定。如下：

    ```
    ttk.spec('js',{
      files: {
        main: {
          'index': 'main.index.js',
          'auth': 'main.auth.js'
        },
        common: ['vue','vue-router']
      }
    });
    ```

    * `main`：`Object|null`，指定主js文件，也就是编译入口文件，key-value格式。其中：
      * key代表文件名`[name]`，决定编译输出的文件名`[name].[hash].js`；
      * value代表对应的源文件名，必须是存在于`source`指定目录下的文件名。请注意，**value的取值必须符合`mainFilePrefix`和`ext`命名规范**，否则无法识别。
    * `common`：`Array|undefined|null`，指定需要提取的公共模块。 上述代码将vue和vue-router文件提取出来，与webpack runtime&manifest共同打包成一个独立的`common.[appname].[hash].js`。此配置项与`splitCommonModule`的目标一致，为了更利于浏览器缓存。


### 示例
```JavaScript
ttk.spec('js', {
    // JavaScript文件后缀类型
    ext: 'js',
    // JavaScript文件目录，相对于basic.source
    source: 'js',
    // JavaScript文件输出目录，相对于basic.output
    output: 'js',
    // js入口文件的前缀，入口文件的命名规则为[mainFilePrefix].*.[ext]
    mainFilePrefix: 'main',
    // 是否启用文件hash指纹
    useHash: true,
    // 异步模块是否使用hash指纹
    asyncModuleHash: true,
    // 是否压缩混淆
    uglify: true,
    // 是否开启代码规范测试
    lint: true,
    // 否将公共模块提取出来
    splitCommonModule: true,
    // 自行指定入口文件，未指定的文件即使符合命名规范也不会参与编译
    files: {
        main: {
          'index': 'main.index.js'
        },
        common: ['vue','vue-router']
    },
    dev: {
        // 定义开发环境下编译过程替换的变量
        define: {
            'API_TEST': '/test'
        }
    },
    testing: {
        // 定义测试环境下编译过程替换的变量
        define: {
            'API_TEST': '//passport.test.ttk.com/test'
        }
    },
    prod: {
        // 定义生产环境下编译过程替换的变量
        define: {
            'API_TEST': '//passport.ttk.com/test'
        }
    }
});
```
