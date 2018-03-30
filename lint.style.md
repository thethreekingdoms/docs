## Style代码规范

ttk使用[stylelint-webpack-plugin](https://github.com/vieron/stylelint-webpack-plugin)实现style代码的规范测试，测试内核为[stylelint](http://stylelint.io/)。

### 自定义规范

你可以根据自身的业务需求自定义规范细节，步骤如下：

1. 在项目中创建json类型的规范配置文件，比如在根目录下创建`stylelint_rules.json`；
2. 配置ttk：

    ```
    ttk.spec('style',{
      lint: true,
      lintConfigFile: `./stylelint_rules.json`
    });
    ```

3. 根据[stylelint-webpack-plugin配置](https://github.com/vieron/stylelint-webpack-plugin)和[stylelint rules](http://stylelint.io/user-guide/rules/)修改`stylelint_rules.json`内容。

完成以上步骤后执行`ttk build`便会根据自定义的规范细节进行规范测试。
