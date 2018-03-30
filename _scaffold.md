## 脚手架

ttk-tool脚手架完整的命令格式如下：
```bash
ttk new <newapp>

```
如下图

![Alt text](/assets/17920902119109153.png)

```bash
执行npm start
打开浏览器：输入http://localhost:8082 如果出现如下图示样，说明项目创建成功。
```
![Alt text](/assets/18790312857778.png)

* `--template`选项指定项目模板，简写为`-t`。如果不指定项目模板，ttk将使用默认模板,后期会扩展更多模板(https://github.com/thethreekingdoms/ttk-generator-app)；
* `<newapp>`指定创建项目的文件夹名称，可以有以下取值：
  * `./`或者`.`或者为空，将当前目录指定为项目目录；
  * 任何合法的文件夹名称，比如"newapp",将在当面目录下创建新的文件夹作为项目目录。

> ttk的脚手架功能是基于[yeoman](http://yeoman.io/)构建的，理论上可以安装任何yeoman的脚手架方案。你可以根据yeoman的规范自定义ttk脚手架模板，详细的开发规范请查阅[自定义脚手架](_advance-scaffold.md)。


```bash
执行成功后一个单页项目的初始目录如下图所示：
```
![Alt text](/assets/17950515105145125.png)


根目录下的`ttk-conf.js`是ttk的配置文件，脚手架已经为你创建了基础的配置项，你可以参照[配置文档](_config.md)进行更细化的配置。
