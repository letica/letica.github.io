---
title: 前端面试分享
date: 2017-03-29 17:30:00
categories: front-end
tags: 面试
---

> Q: 如何知道自己的技术水平在行业内所处的位置？
>
> A: 去面试~! 尤其是所处行业的top公司 & 团队。

<!-- more -->

-----

#### ali

1. 盒子模型，box-sizing
2. 实现布局：左栏定宽，右栏自适应
3. position的值都有哪些，作用分别是什么
4. 实现数组去重
5. 闭包 & 事件委托：一个页面有很多li标签，点击标签弹出对应的index
6. 正则：匹配url；replace替换字符串首尾空格
7. 如何实现继承
8. 解释一下原型机制
9. 下面一段代码的输出是什么
```javascript
var a = {
    toString: function () {
        return: 2;
    }
};
var b = {};
b[a] = 1;
console.log(b);
```
10. requireJS 的模块加载机制，是如何避免重复加载的？
11. fis 的实时刷新是如何实现的？
12. vue 与 angularJS的区别
13. Array 的 find() 与 some()

-----

### Didi

1. 以下代码输出是什么
  ```javascript
  var a = 1;

  function fun1() {
      var a = 2;
      console.log(this.a + a);
  }

  function fun2() {
      var a = 3;
      fun1();
  }

  fun2();

  function fun3 () {
      this.a = 4;
  }

  fun3.prototype = {
      a: 5
  };

  var f3 = new fun3();
  fun1.call(f3);
  ```
2. 点击li标签弹出索引
3. 深度拷贝
4. 原生js实现jquery offset函数
5. 左边定宽，右边自适应布局
6. 实现继承
7. 模板替换：实现template函数
  ```javascript
  template('<div class="{%className%}">{%name%}</div>', {
      className: 'col-md-4',
      name: 'didi'
  })

  function template() {
  }
  ```
8. AngularJS 与 vue 有什么区别，双向绑定是如何实现的
9. fis 的工作原理，fis 插件的工作机制，字体文件的精简优化应该在哪一步添加插件来执行；
10. http 状态码，200 与 304 的区别，如何实现缓存文件
11. 浏览器从输入 url 到敲回车显示页面发生了什么？

----

#### 其它

1. AngularJS 的 isolate scope 与普通的 scope 有什么区别？

-----

### 总结

1. 面试题整体偏基础，需要深度理解基础知识的原理，从而融会贯通、举一反三。
2. 对框架的认识也不能仅仅停留在使用上，而是在熟练使用的前提下再去了解原理、查看分析源码，这样既了解整体，又深入局部。不仅要会使用轮子，也要培养自己造轮子的能力。
3. 平时的工作中要学会总结和分享，这是我目前的弱点。其实我是从今年才开始使用思维导图来整理自己的技术知识体系，短短的时间内受益良多，同时也感觉到这种方式不太够用，局限于『初学』整体框架，要深入分析的话可能还是要通过写总结文、分享给别人这种方式，这是我下一步要走的路。

+U！
