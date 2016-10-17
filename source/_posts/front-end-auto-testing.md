---
title: 前端自动化测试
date: 2016-10-10 14:22:11
categories: front-end
tags: test
---

前端自动化测试一般包括两个方面，单元测试和E2E测试。
单元测试为代码可用性校验，适用于通用模块、接口的测试。
E2E测试为模拟用户访问时的场景测试，

> 1. Unit testing 单元测试
    - 为什么需要单元测试？
    - 如何进行单元测试？
    - 单元测试工具（Qunit/**Jasmine**/**Mocha**/Jest）
    - 断言库(Should.js/**Chai**/expect.js/可不用)
    - 代码覆盖率工具 (**Istanbul**/blanketjs)
> 2. end-to-end testing 端到端测试，场景测试
    - protractor
    - webdriverio
    - CodeceptJS
    - nightwatch
> 3. 其它相关
    - 运行器 (Karma)
    - headless broswer (PhantomJS/Selenium)

<!-- more -->

---


### 1. Unit testing 单元测试

#### 为什么需要单元测试？

- 可用性
高效、稳定的确保模块、接口等代码的健壮可用。
- 自动化
开发及修改后补充测试用例，一次编写多次运行，同时也能确保回归测试的正确性。
- 文档化
测试用例即是对模块、接口使用方法的文档说明。
- 辅助设计
代码能够被测试的前提是代码本身具有可测试性，要确保这一点，就需要在开发中注意设计，这是 TDD 将测试前移的重要作用。

#### 如何进行单元测试？

单元测试从字面理解就是对代码单元的测试，它是一个校验，所以只应该有 pass 和 fail 两种情况；校验的单元应该是某个模块或接口，所以应该是调用它得到一个结果；校验这个结果是单元测试的基本动作。

举个例子，除法运算，如果结果是 3，达到目标且返回的为有效的数字类型才可以进行比较：
```
/**
 * 获取 a 除以 b 的结果
 * @param  {[Number]} a [数字]
 * @param  {[Number]} b [数字]
 * @return {[Number]}   [结果数字]
 */
function division(a, b) {
    return a / b;
}

// 测试代码
function test() {
    var result = division(6, 2);
    
    if (result === 3) {
        console.log('pass');
    } else {
        console.log('fail');
    }
}
```
乍一看上面的代码没什么问题，可以满足产品的需求。但是问题来了，如果 b 为 0，这个模块就出现了 BUG，同时如果下次需要达到其他的值就算通过，那就得去修改测试代码，这样的测试代码本身也太不健全。于是乎有了下面的方式：
```
/**
 * 获取 a 除以 b 的结果
 * @param  {[Number]} a [数字]
 * @param  {[Number]} b [数字]
 * @return {[Number]}   [结果数字]
 */
function division(a, b) {

    if (b === 0) {
        return 0;
    } else {
        return a / b;
    }
}

function test(name, result, expect) {

    if (result === expect) {
        console.log(name + '-> pass');
    } else {
        console.log(name + '-> fail');
    }
}
test('normal number', division(6, 2), 3);
test('zero', division(6, 0), 0);
```

如果需要期望值为 10 就通过，那可以这样：
```
test('normal number is 10', division(20, 2), 10);
```

#### 单元测试工具

- [Qunit](https://github.com/jquery/qunit)
08年发布，最初作为JQuery的测试框架。

- [Jasmine](http://jasmine.github.io/) (推荐，配合Karma)
10年发布，大多数场景下，它的运行需要 Karma 或 Chutzpah ，除了一些发行版本（如jasmine-node npm)有内建的runner。
优点：集成度高，自带BBD、spy、方便的异步支持(2.0)；配置方便；
缺点：相对不太灵活；由于各种功能内建，断言方式或者异步等风格相对比较固定；没有自带mockserver， 如果需要这功能的得另外配置；

- [Mocha](https://github.com/mochajs/mocha) (推荐，配合Chai)
诞生于2011年，专为nodeJS的模块构建。它的API与Jasmine非常像，多了一些语法糖去适用于更多的场景，如BDD。
优点：终端显示友好；灵活，扩展性好；
缺点：自身集成度不高（没有断言、spy、异步等），而且经常要配合Chai、Sinon 等库使用；配置相对麻烦一点点；

- [Jest](https://github.com/facebook/jest)
14年发布，Facebook 发布的基于 Jasmine 的单元测试工具。


#### 断言库
断言库提供了很多语义化的方法来对值做各种各样的判断，当然也可以不用断言库，Node.js中也可以直接使用[原生assert库](https://nodejs.org/api/assert.html)。

- [Should.js](https://github.com/shouldjs/should.js)
BDD style assertions for node.js -- test framework agnostic

- [Chai](https://github.com/chaijs/chai) (推荐)
Chai is a BDD / TDD assertion library for node and the browser that can be delightfully paired with any javascript testing framework.

- [expect.js](https://github.com/Automattic/expect.js)
Minimalistic BDD assertion toolkit based on should.js

> ##### TDD Test Driven Development 测试驱动开发
> 先写测试代码，后写功能代码，通过测试来指引实际的功能开发。
适用于开发通用组件、工具方法、类库等。
> ##### BDD Behaviour Driven Development 行为驱动开发
> BDD是第二代的、由外及内的、基于拉(pull)的、多方利益相关者的(stakeholder)、多种可扩展的、高自动化的敏捷方法。它描述了一个交互循环，可以具有带有良好定义的输出（即工作中交付的结果）：已测试过的软件。

#### 代码覆盖率工具

- [Istanbul](https://github.com/gotwarlost/istanbul) (推荐，目前最强大前端代码覆盖率工具)

- [blanketjs](http://blanketjs.org/)

### 2. end-to-end testing 端到端测试，场景测试

- [protractor](https://github.com/angular/protractor)
E2E test framework for Angular apps. 使用Jasmine测试框架来定义测试,当然你也可以选择其他测试框架来定义测试，如mocha。

- [webdriverio](https://github.com/webdriverio/webdriverio) (推荐)
这个库是nodejs的一个webdriver模块(浏览器自动化)。你可以用它写超级简单Selenium测试在你最喜欢的 BDD / TDD 测试框架中，并且可以在本地运行或在云端中， Sauce Lab,BrowserStack或TestingBot。

- [CodeceptJS](https://github.com/Codeception/CodeceptJS)
一个基于WebDriver全新的端到端测试框架。

- [nightwatch](https://github.com/nightwatchjs/nightwatch)
一个易于使用的，基于 Node.js 平台的浏览器自动化测试解决方案。它使用强大的 Selenium WebDriver API 来在 DOM 元素上执行命令和断言。 语法简单但很强大，使您可以快速编写测试。


### 3. 其它相关

#### 运行器

- Karma
karma 可以更好更快速地在多种环境下执行测试代码。运行时它会自动启动配置好的浏览器，同时启动一个 node 服务器，然后在启动好的浏览器中执行测试代码，并将执行结果传回给 node 服务器，打印出收到的执行结果。

#### headless broswer 驱动器

- [PhantomJS](http://phantomjs.org/)
- [casperjs](http://casperjs.org/)


---

供参考：
[单元测试Unit Test](http://www.tych.io/tech/2013/07/10/unit-test.html)
[聊一聊前端自动化测试 byTmallfe](https://github.com/tmallfe/tmallfe.github.io/issues/37)
[七牛资深前端工程师马逸清：前端测试探索实践](http://www.csdn.net/article/2015-07-15/2825220-qiniu-front-end-test-practice)
[The Difference Between TDD and BDD](http://joshldavis.com/2013/05/27/difference-between-tdd-and-bdd/)
[AMD module 单元测试](https://www.zybuluo.com/bornkiller/note/24759)
[测试框架 Mocha 实例教程 by阮一峰](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)
[代码覆盖率工具 Istanbul 入门教程](http://www.ruanyifeng.com/blog/2015/06/istanbul.html)
[gulp_for_qa](https://qaseven.github.io/2016/01/25/gulp_for_qa/) (代码覆盖率测试相关)
[Learn To FE AutoTest](http://blog.lvscar.info/post/tech/fe_auto_test/)
