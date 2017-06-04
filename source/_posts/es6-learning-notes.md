---
title: ES6 学习笔记 (ing)
date: 2016-10-14 19:07:06
categories: front-end
tags: ES6
toc: true
---

> 我只是一个简单的学习笔记。

learn from this book:  [《ECMAScript 6 入门》](http://es6.ruanyifeng.com/) by 阮一峰

<!-- more -->

<!-- toc -->

## 0. 发展历史

- 1996.11，ECMAScript 1.0发布
- 1998.6，ECMAScript 2.0发布
- 1999.12，ECMAScript 3.0发布
- 2000~2007.10，ECMAScript 4.0草案发布，最终因分歧较大未发布正式版本
- 2009.12，ECMAScript 5.0
- 2011.6，ECMAScript 5.1发布
- 2015.6，ES2015（《ECMAScript 2015标准》）发布
- 2016.6，ES2016（《ECMAScript 2016标准》）发布

> ES6既是一个历史名词，也是一个泛指，含义是5.1版以后的JavaScript的下一代标准，涵盖了ES2015、ES2016、ES2017等等，而ES2015则是正式名称，特指该年发布的正式版本的语言标准。

> 本书中提到“ES6”的地方，一般是指ES2015标准，但有时也是泛指“下一代JavaScript语言”。

ECMAScript 与 Javascript 的区别是：前者是后者的规格，后者是前者的一种实现。

## 1. 环境

### node (推荐使用nvm管理node版本)
```
    ## 安装nvm，<version number>需要用版本号替换，目前是v0.32.0
    $ curl -o- https://raw.githubusercontent.com/creationix/nvm/<version number>/install.sh | bash

    ## 激活nvm
    $ source ~/.nvm/nvm.sh

    ## 列出远程node版本
    $ nvm ls-remote

    ## 安装指定版本的node
    $ nvm install v4.1.2

    ## 切换到指定版本
    $ nvm use v4.1.2

    ## 查看当前已安装的node版本
    $ nvm ls

    ## 设置指定版本为默认的node版本
    $ nvm alias default stable

    ## 查看当前node版本
    $ node -v

    ## 查看node安装目录
    $ which node

    ## npm安装的全局目录
    $ npm config get prefix
    $ npm config set prefix /usr/local/
```

### Babel转码工具

由于浏览器暂不支持 es6 的解析，所以需要使用Babel，第一步是添加配置
有以下两种方式:

1. 在项目根目录添加配置文件 `.babelrc`。
```
    {
        // 设定转码规则
        "presets": ["es2015"]
    }
```
2. 在 `package.json` 添加如下配置
```
    "babel": {
     　　"presets": ["es2015"]
    }
```

> [Babel官网](http://babeljs.io/docs/setup/#installation)推荐将 babel-cli 安装在项目本地，原因有二：
- Different projects on the same machine can depend on different versions of Babel allowing you to update one at a time.
- 一台机器上的不同项目可能会基于不同版本的 Babel ，这样允许你一次升级一个项目，不会互相影响
- It means you do not have an implicit dependency on the environment you are working in. Making your project far more portable and easier to setup.
- 这意味着在你工作的环境下没有含义不明的依赖。使你的项目更轻便、更容易安装。

[[这里还有其它的使用方式]](http://babeljs.io/docs/setup/#installation)

## 2. 语法

#### 2.1 变量声明
> var/function/ let/const/import/class，新增后四种。

- let：声明变量，类似var。与var区别如下：
    - **块级作用域**。只在let所在代码块内有效，for语句的计数器很适合用let声明；
    - **先声明后使用**。不存在变量提升；暂时性死区，在一个作用域内所有变量都需要先声明后使用，否则会导致ReferenceError；
    - **不允许重复声明**；

- const：声明常量。
    - 除 **只读** 外，其它特性与let类似。
    - const定义引用类型时，表示的是地址可读，如对象或数组，其值仍然可修改，如果要冻结，可使用`Object.freeze()`;

    ```
    const foo = Object.freeze({});

    // 常规模式时，下面一行不起作用；
    // 严格模式时，该行会报错
    foo.prop = 123;
    ```

- import

- class

> ES5中，var或function定义的全局变量与顶层对象(window/global)的属性等价。
> ES6中，let、const、class定义的全局变量不再跟顶层对象有关联。

```
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```

#### 2.2 解构赋值

- 数组
只要某种数据结构具有Iterator接口，都可以采用数组形式的解构赋值。如果解构失败（严格相等于undefined），变量的值就等于undefined。

    ```
    var [x1, y1, z1] = [1, 2, 3]; // x1 = 1; y1 = 2; z1 = 3;
    var [x2, y2] = new Set(['a']); // x2 = 'a'; y2 = undefined;

    // 默认值
    // 注意，ES6内部使用严格相等运算符（===），判断一个位置是否有值。所以，如果一个数组成员不严格等于undefined，默认值是不会生效的。
    var [x3 = 1] = []; // x3 = 1
    var [x4 = 1] = [null]; // x4 = null
    ```

- 对象
等号左边如有冒号，冒号之前是模式，最终会将**等号右边对应模式的值**赋值给**冒号之后的变量**。

    ```
    // 按照对象的key进行赋值
    var {foo, bar} = {foo: 'aaa', bar: 'bbb'}; // foo = 'aaa'; bar = 'bbb'
    // 等价于：
    var { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb'};

    // 模式
    var {foo: baz} = {foo: 'aaa'}; // baz = 'aaa'; foo = undefined

    // 指定默认值
    var {x: y = 3} = {}; // y = 3;
    var {x: y = 3} = {x: 5}; // y = 5

    // 将一个已声明的变量用于解构赋值时，注意写法，第二行的小括号不可省略，否则JS引擎会将{x}作为代码块处理从而报错。
    var x;
    ({x} = {x: 1});
    ```

- 字符串

    ```
    var [a, b, c] = 'hello'; // a = 'h', b = 'e', c = 'l'
    var {length: len} = 'hello'; // len = 5
    ```

- 数值和布尔值
解构赋值的规则是，只要等号右边的值不是对象，就先将其转为对象。

    ```
    let {toString: s} = 123;
    s === Number.prototype.toString // true

    let {toString: s} = true;
    s === Boolean.prototype.toString // true

    let { prop: x } = undefined; // TypeError
    let { prop: y } = null; // TypeError
    ```

- 函数参数
与变量的解构赋值一致，只是这个变量是作为函数的参数而已。

    ```
    function move({x = 0, y = 0} = {}) {
      return [x, y];
    }

    move({x: 3, y: 8}); // [3, 8]
    move({x: 3}); // [3, 0]
    move({}); // [0, 0]
    move(); // [0, 0]
    ```

    > 小括号的使用：
    > 只有赋值语句的非模式部分，可以使用圆括号。

    错误：
    ```
    var [(a)] = [1];
    function f([(z)]) {return z;}
    ({p: a}) = {p: 42};
    [({p: a}), {x: c}] = [{}, {}];
    正确：
    [(b)] = [3];
    ({p: (d)} = {});
    ```

- 用途总结
    - 交换变量的值

        ```
        [x, y] = [y, x];
        ```
    - 从函数返回多个值

        ```
        // 返回一个数组
        function example() {
          return [1, 2, 3];
        }
        var [a, b, c] = example();
        // 返回一个对象
        function example() {
          return {
            foo: 1,
            bar: 2
          };
        }
        var {foo, bar} = example();
        ```
    - 函数参数的定义

        ```
        // 参数是一组有次序的值
        function f([x, y, z]) { ... }
        f([1, 2, 3]);

        // 参数是一组无次序的值
        function f({x, y, z}) { ... }
        f({z: 3, y: 2, x: 1});
        ```
    - 提取JSON数据

        ```
        var jsonData = {
            id: 42,
            status: "OK",
            data: [867, 5309]
        };
        let {id, status, data: number} = jsonData;
        console.log(id, status, number); // 42, "OK", [867, 5309]
        ```
    - 函数参数的默认值

        ```
        jQuery.ajax = function (url, {
            async = true,
            beforeSend = function () {},
            cache = true,
            complete = function () {},
            crossDomain = false,
            global = true,
            // ... more config
        }) {
          // ... do stuff
        };
        // 避免了 var foo = config.foo || 'default foo' 这样的语句
        ```
    - 遍历Map

        ```
        var map = new Map();
        map.set('first', 'hello');
        map.set('second', 'world');
        // [key] 或 [, value] 可以只获取key或只获取value
        for (let [key, value] of map) {
          console.log(key + " is " + value);
        }
        // first is hello
        // second is world
        ```
    - 输入模块的指定方法

        ```
        const {SourceMapConsumer, SourceNode} = require("source-map");
        ```
