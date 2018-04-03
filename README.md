{% raw %}
# NornJ

```html
  ____        __
/\  __ \     /\ \
\ \ \/\ \   _\_\ \
 \ \_\ \_\ /\_____\
  \/_/\/_/ \/_____/
 `<div>
    Hello World!
  </div>`();
```

`NornJ`是一个**渲染高效**，**可读性好**，**扩展容易**，**适用性广**的javascript模板引擎。

[![NPM Version][npm-image]][npm-url]
<a href="https://travis-ci.org/joe-sky/nornj">
<img src="https://travis-ci.org/joe-sky/nornj.svg?branch=master" alt="Travis CI Status"/>
</a>
<a href="https://codecov.io/gh/joe-sky/nornj">
  <img src="https://codecov.io/gh/joe-sky/nornj/branch/master/graph/badge.svg" alt="Codecov" />
</a>
[![NPM Downloads][downloads-image]][npm-url]

## 概述

`NornJ`是一款同时支持渲染`纯字符串(html)`和`HyperScript(React vdom)`的模板引擎。

> 什么是`HyperScript`?

[`HyperScript`](https://github.com/hyperhype/hyperscript)可以说是一种创建用户界面元素的语法规范，即：`h (tag, attrs, [text?, Elements?,...])`语法。各大前端框架中对于它的应用，最著名的当属`React`。`React`的`createElement`方法即为`HyperScript`的一种实现，`React`使用它来创建`virtual dom`对象。

而`NornJ`可将同样语法规范的模板，转换为多种方式渲染：

```
                  +-----------------+
                  ¦ template string ¦
                  +-----------------+
                           |
                           |
           +-------------------------------+
           |           render to           |
           |                               |
 +-------------------+     +--------------------------------+
 ¦ pure string(html) ¦     ¦ HyperScript(React virtual dom) ¦
 +-------------------+     +--------------------------------+
```

因此，`NornJ`不但可以作为`Express`及`Koa`服务器的界面模板引擎，还可以作为`React`开发中`JSX`的替代品，解决`JSX`在表现流程控制语句等一些方面的不足。它的语法和`JSX`并不互相排斥，可共存一起运行。

## 模板基本示例

* 在独立的模板文件中编写

```html
<template name="partial">
  <#if {{i > 0 || (i <= -10)}}>
    <input id=test onclick={{click}}>
  </#if>
</template>

<template>
  <div>
    <#each {{1 .. 20}}>                               <!-- ".."是范围运算符 -->
      this the test demo Hello {{@index ** 2 | int}}  <!-- "**"是乘方运算符 -->
    </#each>
    <#include name="partial" />                       <!-- 导入其他模板 -->
  </div>
</template>
```

可见，`NornJ`的语法结构在尽量与html保持一致的同时，更有丰富的扩展语法去实现各种逻辑；且拥有`..`、`**`等js不支持的运算符，而且它们还是可以自由扩展的^_^!

* 在js文件中像JSX那样编写

```js
const partial = nj`
  <#if ${i > 0 || (i <= -10)}>                        <!-- 可直接嵌入js表达式 -->
    <input id=test onclick={{click}}>
  </#if>
`;

const template = nj`
  <div>
    <#each {{1 .. 20}}>
      this the test demo Hello {{@index ** 2 | int}}
    </#each>
    #${partial}                                       <!-- 导入其他模板 -->
  </div>
`;
```

`NornJ`也同时支持像`JSX`那样在js文件中自由地编写，它使用`ES2015+`提供的`tagged template string`语法；并且几乎所有JSX支持的特性，它也都是支持的^_^!

## 在线演示地址

* [在线Playground(jsfiddle)](https://jsfiddle.net/joe_sky/n5n9tutj/)
* [在线Playground(codepen)](https://codepen.io/joe_sky/pen/ooPNbj)

## 特色

传统js模板引擎如`Handlebars`、`EJS`、`Nunjucks`等通常只支持渲染字符串，`NornJ`与它们相比，相同点和不同点都有：

* 支持渲染`React`的`virtual dom`对象，作为`JSX`的替代模板语言。
* 支持渲染字符串，故它也可以像传统js模板引擎一样支持`Express`或`Koa`等渲染html；也支持为各类文本文件提供模板渲染。
* 模板语法简单且丰富，在参考自`Handlebars`、`Nunjucks`、`Vue`等著名项目的模板语法基础上，也有很多自己独特的语法。
* 语法的可扩展性很强大而且容易，例如模板中的每个`运算符`及`语句`都是可以扩展的。
* 它有多种使用方式适应不同场景：
  * 可用单独的模板文件定义，并用`Webpack loader`编译；
  * 也可以用`script`标签写在html中；
  * 还可以支持用`ES6模板字符串`语法直接在js文件中编写。
* 高效渲染，几乎不逊于`Handlebars`、`JSX`等主流模板。
* 它不仅可以直接在浏览器中运行，也可以支持`模板预编译`；去除编译器的runtime版仅`4kb(gzip)`大小。

## 与React配合示例

`NornJ`可以替代JSX输出React组件，用它可以将React组件的表现与逻辑更优雅地实现解藕：

```js
import { Component } from 'react';
import { render } from 'react-dom';
import nj from 'nornj';
import { registerTmpl } from 'nornj-react';

@registerTmpl({
  name: 'TestComponent',
  template: `
    <div id=test1>
      this the test demo{no}.
      <#for {1} {no}>
        <i>test{@index}</i>
      </#for>
    </div>
  `
})
class TestComponent extends Component {
  render() {
    return this.template(this.props);
  }
}

render(nj`<TestComponent no=100 />`(), document.body);

/* output:
<div id="test1">
  this the test demo100.
  <i>test0</i>
  <i>test1</i>
  <i>test2</i>
  ...
  <i>test99</i>
</div>
*/
```

## 在ES5环境下使用

`NornJ`也可以支持在es5环境下使用普通的字符串：

```html
<script id="template" type="text/nornj">
  <div>
    this the test demo Hello {{msg}}
    <input id="test" onclick="{{click}}" />
  </div>
</script>
<script type="text/javascript">
  var tmpl = document.querySelector('#template').innerHTML;
</script>
```

然后可以这样渲染html字符串：

```js
let html = nj.render(tmpl, {
  msg: 'Hello world!',
  click: "alert('test')"
});

console.log(html);
/*输出：
<div>
  this the test demo Hello world!
  <input id='test' onclick="alert('test')" />
</div>
*/
```

## 安装

使用npm安装:

```sh
npm install nornj
```

## 相关项目及工具

* [nornj-react(React适配库)](https://github.com/joe-sky/nornj-react)
* [nornj-loader(Webpack loader)](https://github.com/joe-sky/nornj-loader)
* [babel-plugin-nornj-loader(babel插件)](https://github.com/yuhongda/babel-plugin-nornj-loader)
* [koa-nornj(koa适配库)](https://github.com/qingqinxl1/koa-nornj)
* [express适配器](https://github.com/joe-sky/nornj/blob/master/tools/expressEngine.js)
* [react-native transformer](https://github.com/joe-sky/nornj/blob/master/tools/metroTransformer.js)

## 项目脚手架

* [nornj-cli](https://github.com/joe-sky/nornj-cli)

该脚手架的使用方法类似于`vue-cli`，目前可创建完整的基于`react + mobx`的项目模板，且有[快速上手文档](https://github.com/joe-sky/nornj-cli/blob/master/docs/guides/overview.md)。

## 语法高亮插件

* [nornj-highlight(vscode)](https://github.com/joe-sky/nornj-highlight)
* [language-nornj(atom)](https://github.com/zyj1022/language-nornj)

## 示例项目

* [todomvc项目[react + redux + react-router + nornj + webpack4]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc)
* [todomvc项目(无需webpack打包)[react + redux + react-router + nornj]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc-es5)
* [todomvc项目[backbone + marionette + nornj]](https://github.com/joe-sky/nornj/blob/master/examples/backbone-marionette-nornj-todomvc)
* [计数器示例[react-native + styled-components + nornj]](https://github.com/joe-sky/nornj-react-native-counter)
* [项目脚手架[react + mobx + react-router + nornj]](https://github.com/joe-sky/nornj-cli/tree/master/templates/react-mst)

## 浏览器支持

* 可支持所有现代浏览器以及Internet Explorer 9+。

## 谁在使用

![who use](images/y-dept.jpg?raw=true)

## License

MIT

[npm-image]: https://img.shields.io/npm/v/nornj.svg
[downloads-image]: https://img.shields.io/npm/dm/nornj.svg
[npm-url]: https://www.npmjs.org/package/nornj
{% endraw %}