# 0. 预留问题

- yarn和npm的差异和好处
- [webpack 4: import() and CommonJs](https://medium.com/webpack/webpack-4-import-and-commonjs-d619d626b655)
- 异步加载时的一个例子
- 作用域问题
```js
var x = 0;
function bbb(){
	console.log(x);
	(var) x = 0;
(function(){
var x = 10;
console.log(x);
})();
	console.log(x);
}
bbb();
console.log(x);

bbb(aaa){
	console.log(aaa);
	let aaa = 10;
	console.log(aaa);
}
```
- @babel/plugin-transform-runtime @babel/runtime @babel/polyfill的关系（https://zhuanlan.zhihu.com/p/29058936 https://juejin.im/entry/59ba1a3c5188255e723b8cae）
- @babel/runtime和@babel/runtime-corejs2的区别

[TOC]


# 1. HTML

- DOM：文档对象模型（document object model），用于描述HTML内容，通过DOM可以修改HTML的内容
- BOM：浏览器对象模型（browser object model），用于控制浏览器行为
- W3C标准：一系列网页标准的集合，包括HTML、XHTML、XML、CSS、DOM、ES

## 浏览器加载js和css时的逻辑

浏览器自上而下解析HTML文件，在<head>中遇到加载js和css的地方，会堵塞渲染引擎直到加载完成（除非使用async），但遇到写在<script>中的代码块，会直接执行。进入body后根据样式（css文件、内联样式、行内样式）渲染页面，页面渲染完成后开始执行加载的js文件中的代码

### 浏览器内核
- Chrome之前是Webkit，现在是Blink
- Safari是Webkit
- Firefox是Gecko
- IE是Trident
- Opera最早是Presto，后转为Webkit，现跟Chrome一起转为Blink


## addEventListener

addEventListener将事件监听器注册到EventTarget（事件目标），当EventTarget触发事件时指定的回调就会执行。原理是将监听器添加到EventTarget上特定事件类型的监听器列表中

EventTarget是由一个可以接收事件并记录监听器的对象所实现的DOM接口，如window、document、element

# 2. CSS

## transition

定义元素在不同状态之间的过渡效果，默认为**all 0 ease 0**。有四个属性transition-property, transition-duration, transition-timing-function, 和 transition-delay，分别是需要过渡效果的属性，过渡效果持续时间，过渡效果速度曲线，过渡效果延迟时间

# 3. JS

## 3.1 基础

### 
http://www.ruanyifeng.com/blog/2015/11/ecmascript-specification.html
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Indexed_collections

### JS实现私有变量的方式

### 立即执行函数

### 两种属性类型

- 数据属性
    - configurable：是否可delete、是否修改其他特性（除writable）（在数据属性和访问器属性之间切换），采用字面量方式时默认为true，采用Object.defineProperty时默认为false
    - enumerable：是否可枚举（for...in/Object.keys()），采用字面量方式时默认为true，采用Object.defineProperty时默认为false
    - writable：是否可改变（通过[赋值运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment_Operators)），采用字面量方式时默认为true，采用Object.defineProperty时默认为false
    - value：值，默认为undefined

- 访问器属性
    - configurable：是否可delete、是否修改其他特性（除writable）（在数据属性和访问器属性之间切换），采用字面量方式时默认为true，采用Object.defineProperty时默认为false
    - enumerable：是否可枚举（for...in/Object.keys()），采用字面量方式时默认为true，采用Object.defineProperty时默认为false
    - get：给属性提供getter方法，默认为undefined
    - set：给属性提供setter方法，默认为undefined

### JS中的+

### JS中的非数字下标

Ref: 
1. [javascript数组中数字和非数字下标的区别](https://www.cnblogs.com/ZJAJS/archive/2013/01/19/2867847.html)

### JavaScript中的相等性判断

#### ===和Object.is(ES6)

**===** 不会进行类型转换，只有值和值类型都相等时才返回true，极为具体的详情可参考[规范7.2.13](https://www.ecma-international.org/ecma-262/6.0/#sec-strict-equality-comparison)

**Object.is** 跟 **===** 类似，但对+0/-0/NaN做了特殊处理

```js
var num = 0;
var obj = new String("0");
var str = "0";
var b = false;
var nan = NaN;

num === obj //false
num === str //false
obj === str //false
nan === nan //false NaN不与任何值相等（包括它自己）但Object.is做了特殊处理

Object.is(num, num) //true
+0 === -0 //true
Object.is(+0, -0) //false Object.is中+0不等于-0 在某些数学计算时有用
NaN === NaN //false
Object.is(NaN, NaN) //true Object.is中NaN等于其自身

```

#### ==

**==** 会进行隐式转换，极为具体的详情可参考[规范7.2.12](https://www.ecma-international.org/ecma-262/6.0/#sec-abstract-equality-comparison)

如下是A==B的运算结果

| 比较      | Undefined | Null  | Number            | String                    | Boolean        | Object                      | Symbol            |
| --------- | --------- | ----- | ----------------- | ------------------------- | -------------- | --------------------------- | ----------------- |
| Undefined | A===B     | true  | false             | false                     | false          | IsFalsy(B)                  | false             |
| Null      | true      | A===B | false             | false                     | false          | IsFalsy(B)                  | false             |
| Number    | false     | false | A===B             | A===ToNumber(B)           | A==ToNumber(B) | A===ToPrimitive(B)          | false             |
| String    | false     | false | ToNumber(A)===B   | A===B                     | A==ToNumber(B) | A==ToPrimitive(B)           | false             |
| Boolean   | false     | false | ToNumber(A)===B   | ToNumber(A)===ToNumber(B) | A===B          | ToNumber(A)==ToPrimitive(B) | ToNumber(A)==B    |
| Object    | false     | false | ToPrimitive(A)==B | ToPrimitive(A)==B         | A==ToNumber(B) | A===B                       | ToPrimitive(A)==B |
| Symbol    | false     | false | false             | false                     | A==ToNumber(B) | A==ToPrimitive(B)           | A===B             |

这么记，同类型用===判断，undefined和null比较为true，Number和String比较用ToNumber，Boolean和其他比较用ToNumber，String、Number、Symbol和Object比较用ToPrimitive。

#### ToPrimitive

ToPrimitive可将数据转换成原始值，极为具体的详情可参考[规范7.1.1](https://www.ecma-international.org/ecma-262/6.0/#sec-toprimitive)

简略算法如下
```
ToPrimitive(A [, PreferredType])
// PreferredType默认为number，则先执行valueOf再执行toString，如果是string，则先执行toString再执行valueOf。一般来说都是number，只有Date比较特殊是string。而只有Symbol中的ToPrimitive覆盖了以下默认逻辑，但很少用，先按下不表


if(A不是Object)
    return A
else
    if(A是Date)
        if(A.toString()的值是原始值)
            return A.toString()
        else if(A.valueOf()的值是原始值)
            return A.valueOf()
        else
            throw new Error
    else
        if(A.valueOf()的值是原始值)
            return A.valueOf()
        else if(A.toString()的值是原始值)
            return A.toString()
        else
            throw new Error
    
```

Ref: 
1. [MDN|JavaScript 中的相等性判断](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)
2. [规范7.2.13](https://www.ecma-international.org/ecma-262/6.0/#sec-strict-equality-comparison)
3. [规范7.2.12](https://www.ecma-international.org/ecma-262/6.0/#sec-abstract-equality-comparison)
4. [规范7.1.1](https://www.ecma-international.org/ecma-262/6.0/#sec-toprimitive)


### 原型链/继承

https://javascript.info/class-inheritance

#### 几种继承

- 基于原型链

```js
function Super(){
    this.superArg1 = 'superArg1';
    this.superArray = [1, 2, 3];
    this.superFunc1 = function(){
        console.log('this is superFunc1');
    }
}
function Sub(){
    this.subArg1 = 'subArg1';
    this.subArray = [1, 2, 3];
    this.subFunc1 = function(){
        console.log('this is subFunc1');
    }
}

Sub.prototype = new Super();
Sub.prototype.constructor = Sub
var sub1 = new Sub();
var sub2 = new Sub();

sub1.superArray.push(4);
console.log(sub2.superArray) // [1,2,3,4]
```

两个问题：

1. 父类中的引用类型属性会被所有子类实例共享
2. 创建子类实例时无法向父类构造函数传递参数

- 借用构造函数

```js
function Super(name){
    this.name = name;
    this.friends = ['a', 'b', 'c'];
}
function Sub(){
    Super.call(this, 'abc');
    // Super.apply(this, ['abc']);
    this.age = 12;
}
var sub = new Sub();
```

两个问题：

1. 函数无法复用（函数都作为实例属性而不是原型属性而存在）
2. 父类原型对象中定义的属性对子类也不可见

- 组合继承（结合原型链与借用构造函数）

通过原型链实现原型属性和方法的继承，通过借用构造实现实例属性的继承
```js
function Super(name){
    this.name = name;
    this.friends = ['a', 'b', 'c'];
}
Super.prototype.say = function(){
    console.log('say---', this);
}
function Sub(){
    Super.call(this, 'abc');
    // Super.apply(this, ['abc']);
    this.age = 12;
}
Sub.prototype = new Super();
Sub.prototype.constructor = Sub;
<!--Object.defineProperty(Sub.prototype, 'constructor', {
    configurable: true,
    enumerable: false,
    writable: true,
    value: Sub,
})-->
```

- 原型式继承

借助原型基于已有的对象创建新的对象（没有严格意义上的构造函数），ES5中添加了Object.create来规范原型式继承

```js
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
```

- 寄生式继承

封装一个函数，在其内部生成一个增强的新对象并返回的模式

```js
function parasitic(o){
    const clone = object(o);
    // const clone = Object.create(o);
    clone.say = function(){
        console.log('asdf');
    }
    return clone;
}
```

- 寄生组合继承

结合寄生模式与组合模式

```js
function inherit(sub, super){
    const subPrototype = object(super.prototype)
    // const subPrototype = Object.create(super.prototype)
    sub.prototype = subPrototype;
    sub.prototype.constructor = sub;
}

function Super(name){
    this.name = name;
    this.friends = ['a', 'b', 'c'];
}
Super.prototype.say = function(){
    console.log('say---', this);
}
function Sub(){
    Super.call(this, 'abc');
    // Super.apply(this, ['abc']);
    this.age = 12;
}
inherit(Sub, Super);

var s = new Sub();
```

### 作用域
### this
### 定时器/事件队列/浏览器线程模型
### &&/||

JS中的&&和||，本质上进行布尔值的且和或的运算。当运算到某一个变量就得出最终结果之后，就返回那个变量。

一个复杂的例子

```js
var a=new Object(),b=0,c=Number.NaN,d=1,e="Hello"; 
alert(a || b && c || d && e); //js中与操作符（&&）的优先级高于或操作符（||）
```

Ref:
1. [彻底理解js中的&&和||](https://www.cnblogs.com/sgzs/p/7977208.html)

## 3.2 一些工具函数

### Object相关/原型相关

#### Object.create(proto, [propertiesObject])

**ES5**，使用现有对象作为原型对象生成一个新的对象，是对原型式继承的一个规范

```js
var a = {}
var b = Object.create(Object.prototype) //等同于直接使用字面量定义

function A(){}
var c = new A();
var d = Object.create(A.prototype) //唯一的区别是构造函数中的初始化流程无法执行
```

Ref:
1. [MDN|Object.create()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

#### Object.defineProperty()

Ref:
1. [MDN|Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

#### instance.hasOwnProperty

#### instance.isPrototypeOf

#### Object.assign

#### Object.keys

### Array相关

### 数组中一些基本函数

- push：插入一个值，返回数组新长度
- sort：排序，默认按unicode顺序排序（将值转成string），可自定义排序函数，返回原数组
- reverse：倒置，返回原数组
- concat：合并数组中的值，返回新数组
- shift：删除数组第一位并返回该值
- unshift：向数组添加一位或多位，返回新长度

#### Array.prototype.some

判断数组中是否有满足条件的值
```js
var fruits = ['apple', 'banana', 'mango', 'guava'];

function checkAvailability(arr, val) {
    return arr.some(function(arrVal) {
        return val === arrVal;
    });
}

checkAvailability(fruits, 'kela');   // false
checkAvailability(fruits, 'banana'); // true
```

#### Array.prototype.filter

筛选数组中满足条件的值
```js
function isBigEnough(element) {
  return element >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```

map/forEach/

## 3.3 ESNext

### generator

#### generator语法

##### 状态机

Generator可以理解为是一个内部封装了多种状态的状态机，执行时返回一个遍历器依次遍历内部状态
```js
function* Gen(){
    yield 'hello';
    yield 'world';
    return 'end';
}
var g = new Gen();

g.next() //{value: "hello", done: false}
g.next() //{value: "world", done: false}
g.next() //{value: "end", done: true}
g.next() //{value: undefined, done: true}
```

关于yield语法：

- yield只能用在Generator函数中
- yield表达式如果用在另一个表达式之中，必须放在圆括号里面。
- yield表达式用作函数参数或放在赋值表达式的右边，可以不加括号。

##### 和Symbol.iterator的关系？？？？？？？？？？？？

##### 关于next函数：

- next调用时返回{value: "hello", done: false}，done表示后面是否还有yield语法，value表示yield后面的值
- next传入参数，该参数作为上一个yield表达式的值


##### 和for...of的关系？？？？？？？？

##### Generator.prototype.throw()

Generator.prototype.throw()：可以在函数体外抛出错误，然后在Generator函数体内捕获。
```js
function* Gen(){
    try{
        yield 1;
    }catch(e){
        console.error('error in gen', e);
    }
}

var i = Gen();
i.next();

try {
    i.throw('a');
    i.throw('b');
} catch (e) {
    console.error('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```

#### generator的异步应用

generator可以暂停/恢复执行，并且函数体内外数据交换和throw()提供的错误处理机制，因此满足了作为异步解决方案的条件

##### 简单的封装
```js
const ask = (url) => {
    const rsp = yield fetch(url);
    console.log(rsp);
}
const gAsk = ask('http://baidu.com');
gAsk.value.then(rsp => rsp.json()).then(rsp => {
    rsp.next(rsp);
});
```

##### Thunk

Thunk是一种临时函数，将一个有回调的异步操作拆分成两部分
```js
//原样
fs.readFile('filename', (err, file) => {//dosomething})
//改写为thunk函数
const ReadFileThunk = (...args) => (callback) => fs.readFile(...args, callback)
//更加通用的方式
const ThunkGen = (fn) => (...args) => (callback) => fn(...args, callback)
```

采用Thunk自动控制generator的执行流程，**接收和交还程序的控制权**
```js
const ReadFileThunk = ThunkGen(fs.readFile);
const Gen = function* {
    const file = yield ReadFileThunk('filename');
    console.log('got file ', file);
}
const g = Gen();
const readYield = g.next();
readYield.value((err, file) => {
    if (err) throw new Error();
    g.next(file);
})
```

##### co模块

### async/await

#### 好处
- 不再像generator那样执行器来执行控制权的接收和返回
- async返回Promise实例，相比Generator函数返回Iterator更加方便
- 本质上来讲，async函数就是相当于把多个异步操作封装成了Promise对象，await就是内部then的语法糖

#### 用法
- await只能在async函数中
- async表示函数内有异步操作，async函数返回一个Promise对象
- 遇到await就先返回，等其后面的异步操作完成后再往下执行
- await表达式获得的值是Promise实例resolve之后的值
```js
//基本语法
async function asyncFunc(){
    await doSomeAsync();
    console.log()
}
async function asyncFunc(){
    const result = await doSomeAsync();
    console.log(result)
    //直接拿到doSomeAsync返回的Promise实例resolved之后的值
    //相当于doSomeAsync().then(result => console.log(result))
}
```
- await后面的表达式结果可为Promise对象（thenable对象）或基本类型的值
```js
async function asyncFunc(){
    await 1234; //此时为同步
    return 345; //return的值会作为Promise实例回调的入参
}
asyncFunc().then(res => console.log(res)) //345

//await后面是thenable对象 当做Promise实例处理
async function asyncFunc(){
    await {
        then(resolved, rejected){
            setTimeout(resolved, 1000);
        }
    }
}
asyncFunc().then(() => console.log('log me after 1000ms'));
```
- await后面表达式所产生的Promise实例rejected，则rejected时候的值会被传入async函数所产生的Promise实例的异常处理回调（catch(err)/then(null, err)）
- 任何一个await语句后面的Promise对象变为reject状态，那么整个async函数都会中断执行
- 也可以用try..catch、catch()直接处理异常
```js
async function doAsync(){
    await Promise.reject('this is a wrong');
    await Promise.reject('it will not work'); //不会执行
}
doAsync().then(()=>console.log('this will be skip')).catch(err => console.error(err)) //this is a wrong 

//也可以用try..catch、catch()直接处理异常
async function doAsync(){
    try{
        await Promise.reject('this is a wrong');
    }catch(err){
        console.log('hh error has been catched');
    }
    
    await Promise.reject().catch(err => console.log('error also could been catched in this way'));
}
```
- async中没有依赖关系的多个异步可以同时触发以加快时间
```js
async function(){
    const [async1, async2] = await Promise.all([doAsync1(), doAsync2()]);
}

// 另一个方式
const async1 = doAsync1();
const async2 = doAsync2();
await async1;
await async2;
```

### Promise

#### 对Promise的一些理解

- Promise实例代表一个异步操作，其本质上是一个绑定了回调的对象，有pending、fulfilled、rejected三种状态
- Promise实例绑定的回调函数也是被异步调用的（回调被放入一个微任务队列，JS事件队列的所有运行时结束了之后才会被调用）
- 采用.then和.catch进行链式调用时，每次都会返回一个新的Promise实例，这个实例代表上一个回调函数（异步操作），而且上一个回调函数的执行结果（return）会作为下一个回调（如果有的话）的入参，类似于then=(callback, errcallback)=>new Promise((resolve, reject)=>try{resolve(callback())}catch(){reject(errcallback())})
- Promise的错误处理需要调用.catch，否则回调函数中出现的错误将无法得到处理。这在形式上和同步代码的try/catch保持了高度的统一（catch都只需要调用一次，而不像以前的回调地狱中需要多次调用）
- 对于旧式的异步API可以通过Promise包裹的形式来将其改造成Promise形式

## 3.4 模块化

# 4. React

## 4.1 Virtual DOM

https://www.zcfy.cc/article/the-inner-workings-of-virtual-dom-rajaraodv-medium-3248.html

## 4.2 CSS in React

### 当前解决方案

- 不再使用CSS，以JS的方式去写CSS。为CSS提供了强大的模块化能力，但无法使用当前成熟的CSS预处理其（less/sass）
- 依然使用CSS，但使用JS管理CSS的依赖关系。能够较好的结合当前成熟的CSS生态和模块能力

## 4.3 [HOC（High Order Component）](https://reactjs.org/docs/higher-order-components.html)

高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。

高阶组件主要是用来封装公共的抽象逻辑，**高阶组件既不会修改输入组件**，也不会使用继承拷贝它的行为。而是，**高阶组件组合（composes）原始组件，通过用一个容器组件包裹着（wrapping）原始组件**。高阶组件就是一个没有副作用的纯函数。

**高阶组件并不关心数据使用的方式和原因，而被包裹的组件也不关心数据来自何处。**

一些要求：

1. 不在render方法中使用高阶组件

    每次在render中使用高阶组件时都会产生一个新的组件，react的差分算法会认为跟之前的那个不同进而重新渲染整个子树，在导致性能问题的同时会使原有子组件的状态丢失

2. 必须将静态方法做拷贝

    高阶组件所生成的新组件并不会包含原来组件的静态方法，可以使用[hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics)来自动拷贝所有非React的静态方法

    ```js
    function enhance(WrappedComponent) {
        class Enhance extends React.Component {/*...*/}
        // 必须得知道要拷贝的方法 :(
        Enhance.staticMethod = WrappedComponent.staticMethod;
        return Enhance;
    }
    ```

3. Refs属性不能贯穿传递

    refs是一个伪属性，React对其做了特殊处理，向一个由高阶组件生成的新组建传递ref时，其指向的是最外层容器组件实例而不是被包裹的组件。在16.3中添加了React.forwardRef来解决这个问题

## 4.4 React生态

### redux
### react-loadable

### styled-components

#### 如何基于模板字符串实现

# 5. Webpack

## [代码分割](https://webpack.js.org/guides/code-splitting/)

### 多入口

问题：

1. 会导致模块重复问题
2. 不够灵活，不能根据业务逻辑动态分割代码

### 使用SplitChunksPlugin

可以将公共依赖提取到一个已经存在的入口代码块（an existing entry chunk）或一个全新的代码块中

### Dynamic Imports

webpack中有两种方式实现动态导入。一种是ES规范中规定的import()语法，另一个种是webpack中自带的require.ensure语法（随着import()语法的成熟，require.ensure语法可能会越来越少的使用）

> import()语法返回promise，因此使用时需要promise支持

基本使用语法如下：

```js
function getComponent() {
    return import(/* webpackChunkName: "lodash" */ 'lodash').then(({ default: _ }) => {
        var element = document.createElement('div');
        element.innerHTML = _.join(['Hello', 'webpack'], ' ');
        return element;
    }).catch(error => 'An error occurred while loading the component');
}
getComponent().then(component => {
    document.body.appendChild(component);
})
```

其中的import()语法会被webpack转换成：

```js
return __webpack_require__.e(/*! import() | lodash */ \"vendors~lodash\").then(...)

// __webpack_require__.e如下
__webpack_require__.e = function requireEnsure(chunkId) {...}

```

也可以由于import()返回promise，也可以优化成async/await的形式：

> async/await语法还未普及，需要使用babel对语法进行转义，由于babel默认不对import()语法做解析，因此babel还需要额外配合@babel/plugin-syntax-dynamic-import插件对import()语法做解析

```js
async function getComponent() {
    var element = document.createElement('div');
    const { default: _ } = await import(/* webpackChunkName: "lodash" */ 'lodash');
    element.innerHTML = _.join(['Hello', 'webpack'], ' ')
    return element;
}
getComponent().then(component => {
    document.body.appendChild(component);
})
```

# 6. [Babel](https://babeljs.io/docs/en/)

Babel将ES2015+的代码转换成旧版浏览器兼容的代码，主要是以下几件事

1. Transform syntax，对语法进行转换
2. Polyfill features that are missing in your target environment (through [@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill))，垫片新特性
3. Source code transformations (codemods)，转换源代码

但Babel默认只对语法做编译和转换，如箭头函数、解构、class等，但对新增的引用类型Map/Set、Promise功能、prototype function（array.reduce,string.trim）、static function（Array.form，Object.assgin）、regenerator（generator，async）等都不默认支持，需要额外的polyfill

## 6.1 Babel的一些插件

1. @babel/preset-env

   asdf

2. 


# 7. 网络

## 7.1 ajax/fetch

### fetch中的一些问题

https://www.cnblogs.com/huilixieqi/p/6494380.html

[isomorphic-fetch可以用来同构](https://github.com/matthew-andrews/isomorphic-fetch)

## 7.2 HTTP

### cookie

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies

### code

- 400

bad request，表示请求没有进入后台服务中，通常是参数类型不匹配造成的

- 405

方法类型错误

### cors

### Cache

Ref:
1. [MDN|缓存](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)
2. [AlloyTeam|Web缓存机制系列](http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/)

# 8. 安全

# 9. git

- [运行git push时出错，提示Permission denied (publickey)](https://blog.csdn.net/u013894429/article/details/78960813)

### git stash



git branch --set-upstream-to=origin/<branch> master

## 浏览器缓存

# 10. 小程序

## 运行环境和执行机制

## 分包加载

## 优化
.....

- 封装请求