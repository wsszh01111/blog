# webpack

本文档主要以官网内容为主，力求对webpack做一个比较全面且相对细致的梳理，并会对梳理过程中想到的一些问题做一个记录

## Problems

1. [use **Object.prototype.hasOwnProperty.call(instance, 'someArg')** rather than **instance.hasOwnProperty('someArg')**](https://github.com/wsszh01111/blog/blob/master/2018.06.md#objectprototypehasownproperty)

## Entry

入口文件。webpack从该入口文件开始构建整个项目的依赖图谱

### Single Entry (Shorthand) Syntax

基本语法如下：

> entry: string|Array<string>

```
module.exports = {
    entry: {
        main: './path/to/my/entry/file.js'
    }
};
// shorthand
module.exports = {
    entry: './path/to/my/entry/file.js'
};

```

采用数组形式传入多个入口文件时，会创建**multi-main entry**，适合需要注入（inject）多个依赖文件到同一个chunk的情况

```
module.exports = {
    entry: ['babel-polyfill', 'whatwg-fetch', './entry/path/file.js']
}
```

### 可扩展的配置方式

> webpack可扩展的配置方式（Scalable webpack configurations）：是指可重用并可与其他配置组合使用。这是一种流行的技术，用来将程序的要点（concerns）分成环境（environment）、构建目标（build target）和运行时（runtime）

> entry: {[entryChunkName: string]: string|Array<string>}

```
module.exports = {
    entry: {
        a: './src/a.js',
        b: './src/b.js'
    }
};
```

采用这种配置，webpack将分别以a.js和b.js为入口文件分别生成两个完全独立的文件（每个文件中都有webpack引导程序（bootstrap））。

一些具有实际意义的场景（Scenarios）如下：
1. 分离业务代码和第三方依赖（Separate App and Vendor Entries）

配合**CommonsChunkPlugin**（webpack4.x有较大改动，但原理应该类似），CommonsChunkPlugin可以将a.js中对于b.js的引用提取出来（替换为__webpack_require__()），因此一般a中放业务代码，b中放第三方依赖，称为vendors.js

```
module.exports = {
    entry: {
        app: './src/app.js', //app中是业务代码
        vendors: ['react', 'jquery'] //vendors中是依赖的第三方代码
    }
};
```

2. 多页面应用（Multi Page Application）

多页应用一般有多个html，因此采用这种方式生成每个html对应的js文件。同时利用**CommonsChunkPlugin**，可以将多个js中的公共代码进行有效复用

```
module.exports = {
    entry: {
        pageOne: './src/pageOne/index.js',
        pageTwo: './src/pageTwo/index.js',
        pageThree: './src/pageThree/index.js'
    }
};
```

## Output

output参数控制如何命名打包文件和其输出位置，默认是./dist/main.js

默认用法如下：

```
module.exports = {
    output: {
        filename: 'bundle.js',
        path: '/home/proj/public/assets'
    }
};

// 多入口时 采用替换符（substitutions）保证每个打包文件有唯一的文件名
module.exports = {
    output: {
        filename: '[name].js',
        path: '/home/proj/public/assets'
    }
};
```

几个选项讲一讲：
1. chunkFilename

配置非入口的打包文件名（non-entry chunk files）。该文件名，是在需要请求chunks时，在运行的状态下产生的（即在需要时才创建文件名，请求对应的文件），因此webpack会在运行状态（runtime）中，对于替换符[name]和[chunkhash]，增加代码块ID（chunk id）到替换符的值的映射关系，会增大代码体积，同时如果替换符的值变了，将导致bundle无效（即runtime中有1=>aaa/1=>somehash，会增大代码体积，且aaa或somehash不能变）

2. publicPath

在使用按需加载或加载额外的资源（图片、文件）时此属性是一个很重要的属性，其指定在浏览器中引用输出目录时的公共URL（public URL）

```
module.exports = {
    output: {
        // One of the below
        publicPath: 'https://cdn.example.com/assets/', // CDN (always HTTPS)
        publicPath: '//cdn.example.com/assets/', // CDN (same protocol)
        publicPath: '/assets/', // server-relative
        publicPath: 'assets/', // relative to HTML page
        publicPath: '../assets/', // relative to HTML page
        publicPath: '', // relative to HTML page (same directory)
    }
};
```