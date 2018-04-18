## 如何开发一个hellworld

---


> ### Setp 1.创建一个空白的App
```
ttk app firstapp
* 在当前目录apps/firstapp下面创建了如下文件

    action.js
    component.js
    config.js
    data.js
    index.js
    list.txt
    mock.js
    reducer.js
    style.less
    webapi.js 
```
* 如果要创建目录，可以使用ttk app new/firstapp的方式创建空App

> ### Setp 2.配置页面模型(文件路径：firstapp/data.js)

![image](./assets/data.gif)
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
> ### Setp 3.事件触发入口(文件路径：firstapp/action.js)


```
    btnClick = () => {
        this.injections.reduce('modifyContent')
    }
```

> ### Setp 4.页面状态更新(文件路径：firstapp/reducer.js)



```
    modifyContent = (state) => {
        const title = this.metaReducer.gf(state, 'data.title')
        return this.metaReducer.sf(state, 'data.content', title + '!')
    }
```
