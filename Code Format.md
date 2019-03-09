# Code Format

代码检查与格式自动化

# 思路

[ESLint](https://github.com/eslint/eslint)是可拔插的JS和JSX的语法检查器，[Prettier](https://github.com/prettier/prettier)是最近广泛使用代码格式化工具，这里ESLlint采用Prettier的语法格式规则对语法进行检查，并配合lint-staged等工具实现commit前的代码自动检查。基于Sublime环境。

# [安装ESlint](https://eslint.org/docs/user-guide/getting-started)

# [安装Prettier](https://prettier.io/docs/en/install.html)

# [整合ESLint和Prettier](https://prettier.io/docs/en/eslint.html)

使用[eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)和[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)配置ESLint，eslint-plugin-prettier主要是可以让ESLint使用Prettier的语法检查规则，eslint-config-prettier则关闭了可能Prettier发生冲突的ESLint自带的一些语法检查规则（eslint-config-prettier本质上关闭了一些在rules字段中配置的参数Note that this config *only* turns rules *off,* so it only makes sense using it together with some other config.）

eslint-plugin-prettier还提供一种简单的配置方式用来整合了使用Prettier语法检查规则和关闭ESLint冲突规则这两件事，即

```json
{
    "extends": ["plugin:prettier/recommended"]
}
```

上面的配置主要干了三件事：

- Enables `eslint-plugin-prettier`.即类似于在`plugins`字段中配置`prettier`
- Sets the `prettier/prettier` rule to `"error"`.
- Extends the `eslint-config-prettier` configuration.即类似于在`extends`字段中配置`prettier`

可以使用[命令行工具](https://github.com/prettier/eslint-config-prettier#cli-helper-tool)帮助检查Prettier和ESLint之间的冲突

```json
{
  "scripts": {
    "eslint-check": "eslint --print-config . | eslint-config-prettier-check"
  }
}
```

## 其他插件

### [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)

提供对react的语法支持，但由于eslint-config-prettier中支持了eslint-plugin-react插，所以采用eslint-config-prettier中推荐的写法，详见[eslint-config-prettier文档](https://github.com/prettier/eslint-config-prettier#installation)

```json
{   //常规写法
    "plugins": ["react"] //"plugins": ["eslint-plugin-react"]
}
{   //eslint-config-prettier中支持的写法
    "extends": ['plugin:prettier/recommended', 'prettier/react'],
}
```

### [eslint-plugin-import](https://github.com/benmosher/eslint-plugin-import)

提供import/export的语法支持

```json
{
    "plugins": ["import"] //"plugins": ["eslint-plugin-import"]
}
```

## 其他配置项

### babel-eslint

提供更新的实验性质的或还没有完全到规范中的语法支持，建议仅在使用babel做语法转换时使用

# 最终配置

```js
/**
 * Ref:
 * 1. [Integrating with ESLint](https://prettier.io/docs/en/eslint.html)
 * 2. [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)
 * 3. [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)
 */
module.exports = {
    // Target Environment
    env: {
        browser: true,
        es6: true,
        commonjs: true,
    },
    /**
     * Did 3 things
     * Enables eslint-plugin-prettier.
     * Sets the prettier/prettier rule to "error".
     * Extends the eslint-config-prettier configuration.(Note that this config only turns rules off)
     */
    extends: ['plugin:prettier/recommended', 'prettier/react'],
    // remember there is eslint-plugin-prettier besides eslint-plugin-import
    plugins: ['import'],
    parser: 'babel-eslint',
    parserOptions: {
        ecmaVersion: 2016, //set ES2016 for async/await
        sourceType: 'module',
    },
    rules: {
        'no-console': 0,
    },
};
```

# Sublime中的支持

## 代码检查

- [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter)Sublime中的代码检查框架
- [SublimeLinter-eslint](https://github.com/SublimeLinter/SublimeLinter-eslint)SublimeLinter框架的ESLint插件

## 代码格式化

[JSPrettier](https://github.com/jonlabelle/SublimeJsPrettier)在Sublime中使用Prettier对代码进行格式化，JSPrettier会自动读取项目中的Prettier配置并进行格式化，此外在Sublime中的配置如下

```json
{
    "auto_format_on_save": true
}
// key binding
[
	{ "keys": ["shift+alt+f"], "command": "js_prettier" }
]
```

# 自动化

# CLI

在package.json中配置

```json
{
    "lint": "eslint -c .eslintrc.js src webpack.config.js",
    "lint:fix": "eslint -c .eslintrc.js src webpack.config.js --fix"
}
```

