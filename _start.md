## 开始使用

### 安装

ttk以命令行工具的形式工作，使用npm全局安装：

```bash
npm i ttk-tool -g
```

国内网络环境下访问npm仓库比较缓慢，可以将npm仓库地址修改为国内淘宝镜像：

```bash
npm config set registry https://registry.npm.taobao.org
```


安装成功后，你可以使用ttk[脚手架](_scaffold.md)创建一个新的ttk项目，或者按照[构建配置文档](_build.md)将已存项目改造为ttk项目。

### 脚手架

使用下述命令创建一个新的ttk项目：
```bash
ttk new <webapp>
```

* `webapp`取值为空或`.`或`./`时将会在当前目录创建项目；如果`webapp`为合法的目录名称，将会在当前目录创建以`webapp`命名的新文件夹并在新文件夹内创建项目文件;
* `template`为项目脚手架模板，如果不指定（即执行`ttk new <dirname>`）则使用ttk默认模板[`generator-ttkapp`](https://github.com/thethreekingdoms/generator-ttkapp)。

> 更多的脚手架使用细节请参照[脚手架文档](_scaffold.md)。

### 构建

在项目根目录下执行：

```bash
ttk compile <env>
```

* `<env>`指定构建项目的环境。如果不指定则默认在`testing`环境下编译。ttk内置支持以下环境：
    * `dev`- 开发环境；
    * `testing`- 测试环境；
    * `prod`- 生产环境；
    
> 你可以使用`envs()`扩展更多环境支持，具体细节请参考[多环境支持](./_envs.md)

* `-i`/`--init`(可选)选项的作用是指明是否让ttk自动检查依赖(包括ttk自身的插件、配置以及项目的dependencies)并自动安装。以下情况必须指定此选项：
  * 项目初始化后第一次运行`build`或`serve`（取其一）；
  * 修改`ttk-conf.js`中以下选项后：
    * `style`-`ext`类型/开启`autoprefix`/开启`sprites`;
    * `html`-`engine`类型;

> 由于检查依赖需要花费大量时间，所以除以上情况以外，尽量避免使用`init`选项。

> 关于ttk环境配置的细节请参阅[多环境支持](_envs.md)。

编译成功后，默认输出目录为`dest`。

> 关于构建的详细功能配置请参阅[构建](_build.md)。

### 本地服务器
ttk提供一个本地服务容器，方便前端工程师独立开发。在项目根目录下运行`serve`命令：

```bash
ttk serve [-i]
```

> `-i`/`--init`(可选)选项作用同编译功能。

* 本地服务器默认监听`8888`端口；
* 如果项目中只存在一个`index.html`文件，直接访问`localhost:8888`即可；
* 如果项目中存在多个`index.*.html`文件:
  * 直接访问`localhost:8888`默认返回`index.html`；
  * 如果要访问其他入口，地址格式为`localhost:8888/index.*.html`或者完整路径`localhost:8888/<views dir>/index.*.html`(views路径和html文件名可自行[配置](_config-html.md))；
  * 如果开启了`html`-`removePrefixAfterBuilt`，则可以省略每个html文件的“index”前缀。比如源码存在`index.home.html`和`index.about.html`则可以直接访问`localhost:8888/home.html`和`localhost:8888/about.html`

#### HMR与Livereload

本地开发服务器开启期间，对于JavaScript和CSS源文件的修改无需刷新浏览器即可由HMR即时更新至客户端，而HTML源文件的修改会触发Livereload。

开发过程中**已存文件**的内容修改无需重启本地服务器，但如果**增加新文件或者已存文件重命名，则必须重启本地服务器**才可看到效果。

> 本地服务器详细功能配置请参阅[本地服务器](_devserver.md)。

#### Mock服务

ttk的Mock服务有两种运行模式：独立运行和Dev server集成。两种模式的配置项完全一致，如下：

```javascript
ttk.mock('Get /userinfo').params(['name']).custom({
  jsonpCallback: 'cb'
}).response({
  success: {
    code: 200,
    msg: '请求成功'
  },
  fail: {
    code: 500,
    msg: '请求失败'
  }
});

ttk.mock('Post /signup').proxy('https://passport.ttkteam.com/signup');
```

上述代码定义了两个Mock接口：
* 支持Get请求的`/userinfo`接口。必选参数`name`，支持jsonp并且jsonpCallback识别为`cb`(默认为`callback`)，返回数据自定义数据；
* 代理接口`/signup`。将本地接口`/signup`的Post请求代理到'https://passport.ttkteam.com/signup'接口。

##### 独立运行
在项目根目录下运行：
```bash
ttk mock -p 9999
```
* `-p`指定监听端口号，默认为8889。

然后运行本地开发服务器时增加`-s`选型：
```bash
ttk server -s
```
* `-s`代表运行dev server不会集成mock服务。

> 之所以支持独立Mock服务是为了多项目共用同一Mock接口的场景，增强Mock的复用性。

##### Dev server集成
不添加`-s`选项运行dev server即可集成Mock服务：
```bash
ttk serve
```

> 关于Mock的详细配置请参阅[Mock](_mock.md)。

### 部署

编译完成之后，执行以下命令将生成的代码部署到远程服务器：

```bash
ttk deploy -e <env>
```

* `env`可选**`dev`以外**的任意有效环境变量，包括ttk内置的`testing/prod`以及通过`envs()`API定义的环境变量；
* 部署功能使用sftp作为传输协议，所以需要接收文件的服务器提供相应权限。

> 部署的详细功能配置可参阅[远程部署](_deploy.md)。

#### 浏览器支持

| ![Chrome](https://raw.github.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.github.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.github.com/alrra/browser-logos/master/src/archive/internet-explorer_9-11/internet-explorer_9-11_48x48.png) | ![Opera](https://raw.github.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.github.com/alrra/browser-logos/master/src/safari/safari_48x48.png) | ![Edge](https://raw.github.com/alrra/browser-logos/master/src/edge/edge_48x48.png) |
| ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| ✔                                                                                 | ✔                                                                                    | 10+ ✔                                                                                                                        | ✔                                                                              | ✔                                                                                 | ✔                                                                           |






