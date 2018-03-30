## Mock服务
[![GitHub stars](https://img.shields.io/github/stars/thethreekingdoms/ttk-mock.svg?style=social&label=Stars)](https://github.com/thethreekingdoms/ttk-mock)
[![GitHub forks](https://img.shields.io/github/forks/thethreekingdoms/ttk-mock.svg?style=social&label=Fork)](https://github.com/thethreekingdoms/ttk-mock)

虽然默认集成到本地服务器中，但ttk的Mock服务是一个独立模块，你完全可以不使用集成模式。

之所以将Mock服务独立出来，是考虑到不同的团队对于Mock的理解和定位不同，并且部分团队可能已经具备完整的Mock服务平台，所以ttk将Mock服务从主核中分离出来。不集成Mock服务的本地服务器开启速度更快，运行效率更高。

### 配置
ttk的Mock服务配置支持两种接口类型：
* *常规的异步接口*指的是响应客户端发起的AJAX或者Jsonp请求的数据接口，返回由ttk配置的自定义数据；
* *代理接口*指的是将客户端对某个接口的请求转发的被代理的接口，最常用的场景是远程接口已经可用但是不支持跨域请求，中间加一层代理可以绕过跨域限制，方便开发人员在本地调试。


请看如下配置：
```JavaScript
// 定义常规异步接口/userinfo,支持get请求
ttk.mock('GET /userinfo').params({
  uid: {
    required: true
  },
  deviceId: {
    required: false
  }
}).custom({
  jsonpCallback: 'cb'
}).response({
  success: {
    code: 200,
    msg: '请求成功',
    data: {
      name: 'Joe',
      phone: '18888888888'
    }
  },
  fail: {
    code: 500,
    msg: '请求失败'
  }
});

// 定义代理接口/signup,支持post请求
ttk.mock('POST /signup').proxy('https://test.ttkteam.com/signup');
```

ttk的Mock服务不同于构建、部署、devServer等用json格式配置，而是由一连串的JavaScript函数链式调用进行细化配置。其中：
* `ttk.mock()`函数必须在链式调用的最起始位置，在此函数之后才可以调用`params()/custom()/respsonse()/proxy()`函数，如果直接调用`ttk.params()`等函数将会抛出错误；
* `respsonse()/proxy()`函数必须处于链式调用的结尾，在此函数之后不可调用任何其他函数，比如指定`ttk.mock().response().params()`将会抛出错误；
* `params()/custom()`函数的调用不分先后，比如`ttk.mock().params().custom()`等价于`ttk.mock().custom().params()`。

各函数的作用如下：
* `mock('<method> <path>')`，用于定义接口的请求方法和路径，请注意两者之间必须用空格分割。
  * `<method>`，`String`,支持GET和POST，大小写不敏感；
  > 更多方法后续支持

  * `<path>`，`String`，接口**绝对路径**。

* `params(<params>)`，用于定义异步接口的入参。
  * `<params>`，`Array|Object`，如果为`Object`类型，则需要按照上述配置代码中的格式编写，子配置项`required`代表此参数是否是必选的，将会影响Mock服务的参数验证从而绝对返回`success`还是`fail`数据。如果为`Array`类型，则数组元素为**必选参数**的key值，比如上述配置可以改写为`params(['uid'])`。

* `custom(<options>)`，用于定制异步接口的细节，目前版本仅支持定制`jsonpCallback`。
  * `<options>`，`Object`，可选值包括`jsonpCallback`，默认为`callback`。比如上述配置代码将jsonp的回调函数入参key定义为`cb`。

* `response(<data>)`，用于定义异步接口的返回数据。
  * `<data>`，`Object`，`success`数据为必选项，否则后抛出错误；`fail`数据为可选项，当Mock服务接收的请求中不包括由`params()`定义的所有**必选参数**时将会返回fail数据，如果未定义fail数据则会返回`'Invalid parammeters'`。

* `proxy(<url>)`，用于定义代理接口。代理接口模式的`params()/custom()`配置均无效，所以定义一个代理接口的正确语法是`ttk.mock(...).proxy(...)`，无需其他任何函数。
  * `<url>`，`String`，被代理的远程接口。
  > ttk并不会验证`<url>`的有效性。

### 运行模式

#### 本地服务集成
不添加`-s`选项运行dev server即可集成Mock服务：
```bash
ttk serve
```

* `-s`代表运行Dev server不会集成mock服务，详情请参阅[DevServer](_devserver.md)。

#### 独立运行 - ttk集成
ttk提供了单独的命令用于独立运行Mock服务，在项目根目录下运行：
```bash
ttk mock -p 9999
```
* `-p`指定监听端口号，默认为8889。

然后运行本地开发服务器时增加`-s`选型：
```bash
ttk server -s
```

**请注意**，独立运行模式下必须确保Mock服务与Dev server使用不同的端口。

### 独立模块 - Node.js集成
除了集成到ttk之外，`ttk-mock`还可以作为一个独立的npm模块运行，你无需安装ttk便可以自行集成到Node.js中。作为独立模块运行时，配置和运行均与ttk集成完全不同。

#### 配置
将如下配置保存为`mock.config.js`（命名随意）：
```JavaScript
module.exports = [{
  // 请求方法，对应mock()函数的method
  method: 'post',
  // 接口路径，对应mock()函数的path
  api: '/signup',
  // 入参，对应params()函数
  inParams: [{
    name: 'username',
    required: true
  },{
    name: 'passport',
    required: false
  }],
  // 返回数据，对应response()函数
  res: { 
    success: {
      code: 200,
      msg: '操作成功',
      data: {
        username: 'John'
      }
    }, 
    fail: {
      code: 500,
      msg: '操作失败',
    }
  },
  // 定制细节，对应custom()函数
  options: { 
    jsonpCallback: 'callback'
  } 
},{
  method: 'get',
  api: '/userinfo',
  // 代理接口，对应proxy()函数
  proxyApi: 'http://passport.ttkteam.com/userinfo',
}]
```

#### Node.js集成
将如下代码保存为`mock.js`（命令随意）：
```JavaScript
const ttkMock = require('ttk-mock');
const Config = require('./mock.config.js');

ttkMock(null, Config, 9999);
```

#### 运行
在终端中执行以下命令：
```bash
node mock.js
```

执行成功后将会得到以下提示：
```bash
Mock server is listening localhost:9999
```

### 为什么要将Mock服务独立
之所已将Mock服务单独分离出来主要有以下几方面的考虑：
* 多个项目可能会使用同一个Mock接口，这种场景下仅需要单独开启一个Mock服务即可支持多项目同时开发，减少了配置的繁琐程度；
* Mock接口本质上是静态的，与真实环境相比，缺点是无法根据入参的值决定返回数据。所以在DevServer集成模式下，如果需要根据返回数据分发客户端逻辑，那么必须重新配置Mock接口并且重启DevServer。这样不仅多了无畏的重启工作并且重启DevServer会对项目重新构建，影响开发效率。而将Mock服务独立运行的好处是，仅需要重启Mock服务即可，无需重新构建项目。