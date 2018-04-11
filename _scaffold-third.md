## 使用脚手架克隆第三方app
[[TTK提供的第三方APP](https://www.npmjs.com/~jeffycai)](应用商店)

### 如何克隆
```bash
ttk clone <应用商店提供的app名称> [app存放路径]
```

* > *应用商店*指由第三方组织或个人，根据ttk框架开发出来的app应用，发布到(npmjs)[npmjs.org],供大家使用。关于如何开发第三方APP，请参考[开发自己的app应用](_dev-app.md)

![Alt text](/assets/17860610746683.png)

参考事例：
```bash
ttk clone ttk-edf-app-login apps/edf/ttk-edf-app-login
```
### 如何批量克隆
```
ttk apps apps/edf ttk-edf-app-login ttk-edf-app-portal ttk-edf-app-home
```

![Alt text](/assets/20180411150725.png)
