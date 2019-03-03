# React

对react的各项特性进行了解和梳理，并会对梳理过程中想到的一些问题做一个记录

## Code-Splitting（代码分割）

目前最常使用webpack打包成单页面应用，但通常js文件会过于庞大以至于页面加载时间过长，因为就需要将打包文件（bundle）进行拆分并在运行时自动加载。

### import()

推荐使用动态导入语法（input()）进行代码分割

```js
import('./module').then(module=>{
    console.log(module.dosomething())
})
```

> 但，该动态导入语法，还是个ES6的Proposal（参见Ref2），处于Stage3，但目前（2018.08.01）各浏览器也处于刚刚支持的状态（FF还不支持）

当webpack遇到该语法时，会自动进行代码分割。使用babel时，需要使用babel-plugin-syntax-dynamic-import插件（参见Ref4）来解析该语法

### Libraries

React Loadable以一种React友好的方式封装了import()
```js
import Loadable from 'react-loadable';

const LoadableOtherComponent = Loadable({
    loader: () => import('./OtherComponent'),
    loading: () => <div>Loading...</div>,
});

const MyComponent = () => (
    <LoadableOtherComponent/>
);
```


React Loadable可以创建加载状态（loading states）、错误状态（error states）、超时（timeouts）、预加载（preloading），甚至可以在服务端渲染中使用代码分割

### Route-based code splitting（基于路由的代码分割）

各个路由是页面中一个不错的代码分割点

```js
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import Loadable from 'react-loadable';

const Loading = () => <div>Loading...</div>;

const Home = Loadable({
    loader: () => import('./routes/Home'),
    loading: Loading,
});

const About = Loadable({
    loader: () => import('./routes/About'),
    loading: Loading,
});

const App = () => (
    <Router>
        <Switch>
            <Route exact path="/" component={Home}/>
            <Route path="/about" component={About}/>
        </Switch>
    </Router>
);
```

Ref:
1. [Code-Splitting](https://reactjs.org/docs/code-splitting.html)
2. [tc39/proposal-dynamic-import](https://github.com/tc39/proposal-dynamic-import)
3. [webpack/code-splitting](https://webpack.js.org/guides/code-splitting/)
4. [babel/babel-plugin-syntax-dynamic-import](https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import/)
5. [React Loadable](https://github.com/thejameskyle/react-loadable)