## JavaScript代码规范

ttk使用[eslint-loader](https://github.com/MoOx/eslint-loader)实现对JavaScript代码的规范检查，执行`ttk build -i`或`ttk serve -i`后会在项目根目录下创建`.eslintrc`文件，内容如下：
```json
{
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "script"
  },
  "env": {
    "node": true,
    "es6": true
  },
  "extends": "eslint:recommended",
  "rules": {
    "indent": [2, 2,{ "SwitchCase": 1 }],
    "brace-style": [2, "1tbs"],
    "quotes": [2, "single"],
    "semi": [2, "always"],
    "comma-style": [2, "last"],
    "one-var": [2, "never"],
    "no-console": 1,
    "no-use-before-define": [2, "nofunc"],
    "no-underscore-dangle": 0,
    "no-constant-condition": 0,
    "space-before-function-paren": [2, {"anonymous": "always", "named": "never"}],
    "func-style": [2, "declaration"]
  }
}
```

### 自定义规范
你可以更加业务和团队需求自行修改`.eslintrc`的规范。

**请注意**：每次执行`ttk build -i`或`ttk serve -i`均会重新创建`.eslintrc`文件，所以请确保做好备份。

> ttk提倡项目所采用的技术栈在初次初始化后便不再变化，每次初始化均会取代之前的设置。
