## 如何开发一个简单页



---
### Setp 1.创建一个空白的App
```
$ ttk app firstapp
运行后，在当前目录apps/firstapp下面创建了如下文件

    action.js
    component.js
    config.js
    data.js
    index.js
    mock.js
    reducer.js
    style.less
    webapi.js 
```

==如果要创建目录，可以使用ttk app new/firstapp的方式创建。（脚手架会将斜线转为文件夹）==

### Setp 2.配置页面模型(文件路径：firstapp/data.js)

```
{
	name: 'root',
	component: 'Layout',
	className: 'firstapp',
	children: [{
		name: 'demo',
		component: '::span',
		className: 'firstapp-div',
		children: [{
			name: 'tips',
			component: '::div',
			children: '{{data.content}}'
		}, {
			name: 'btnOk',
			component: 'Button',
			children: '确定',
			type: "primary",
			onClick: '{{$btnClick}}'
		}
		]
	}]
}
    
```

```
export function getInitState() {
	return {
		data: {
			content: 'Hello TTK',
			title: 'Hello World'
		}
	}
}
```
### Setp 3.事件触发入口(文件路径：firstapp/action.js)

```
    btnClick = () => {
        this.injections.reduce('modifyContent')
    }
```

### Setp 4.页面状态更新(文件路径：firstapp/reducer.js)

```
    modifyContent = (state) => {
        const title = this.metaReducer.gf(state, 'data.title')
        return this.metaReducer.sf(state, 'data.content', title + '!')
    }
```


相关API介绍


| 成员        | 说明           | 类型               |示例| 
|-------------|----------------|--------------------|--|
| config    | 配置函数（默认值、公共配置）   | function | |
| gf | 获取state中某个字段值，参数为(fieldPath)，如data.form.user | function | this.metaAction.gf('data.title')|
| sf | 设置state中某个字段值，参数为(fieldPath,value) | function |this.metaAction.sf('data.title','hello')|
| gm | 获取元数据，参数为(path,propertys), path:如root.form.user, propertys:如['style'] | function|this.metaAction.gm('root.form.list1,'title')|
| sm | 设置元数据，参数为(path,propertys,value), path:如root.form.user, propertys:如['style','color:@primaryColor'] | function |this.metaAction.sm('root.form.list1,'title','hello')|
| context | 上下文object,设置后所有app可以获取context中的信息 | object | this.metaAction.context.get("currentOrg")|