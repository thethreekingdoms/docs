## Html代码规范

ttk使用[html-webpack-plugin-htmlhint](https://github.com/thethreekingdoms/html-webpack-plugin-htmlhint)实现html代码的规范测试，测试内核为[htmllint](http://htmlhint.com/)。

### 自定义规范

你可以根据自身的业务需求自定义规范细节，步骤如下：

1. 在项目中创建json类型的规范配置文件，比如在根目录下创建`htmllint_rules.json`；
2. 配置ttk：

    ```
    ttk.spec('html',{
      lint: true,
      lintConfigFile: `./htmllint_rules.json`
    });
    ```
3. 根据[htmllint rules](https://github.com/yaniswang/HTMLHint/wiki/Rules)修改`htmllint_rules.json`内容。

完成以上步骤后执行`ttk build`便会根据自定义的规范细节进行规范测试。
