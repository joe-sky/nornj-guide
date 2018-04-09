{% raw %}
# 结合Webpack在单文件中编写模板

`NornJ`模板可以用纯字符串构建在单独的模板文件中，并可支持分模块构建。这种方式可在以下几个场景使用：

* 在`Webpack`环境下使用`nornj-loader`引入单模板文件，[loader如何配置请见这里](https://github.com/joe-sky/nornj-loader/blob/master/README.md)。
* 在Node.js环境中(如`Express`服务器或`React`服务器端渲染)使用`NornJ`模板。

> 单模板文件的扩展名一般为这几种：
`*.nj.html`、`*.nj.htm`、`*.t.html`、`*.t.htm`、`*.nornj`、`*.nj`

## 模板语法高亮插件

针对上述几种扩展名的文件，我们提供了`NornJ`语法的高亮和智能提示插件：

* [nornj-highlight(vscode)](https://github.com/joe-sky/nornj-highlight)
* [language-nornj(atom)](https://github.com/zyj1022/language-nornj)

## 在单模板文件中定义单个模板

template.nj.html：
```html
<div>
  this the test demo{no}.
</div>
```

component.js：

```js
import template from './template.nj.html';

@registerTmpl('TestComponent')
class TestComponent extends Component {
  render() {
    return template({ no: 1 });
  }
}

/*渲染组件后输出html:
<div>
  this the test demo1.
</div>
*/
```

## 在同一个文件中定义多个模板

`NornJ`模板也可支持在一个单模板文件中定义多个模板，每个模板使用`template`标签定义，然后使用`include`标签引入：

template.nj.html：
```html
<template name="tmplModule" local>  <!-- 可设置local属性 -->
  <div>
    this the tmplModule demo{no}.
  </div>
</template>

<template name="tmplModule2">
  <input type="button" />
</template>

<template name="main">  <!-- 每个template标签都有name属性(1) -->
  <div>
    <#include name="tmplModule" />  <!-- include块只设置name属性，则为引入当前html文件中的其他模板(2) -->
    <#include src="./template2.nj.html" />  <!-- include块只设置src属性，则为引入其他文件中的主模板(3) -->
    <#include name="tmplModule2" src="./template2.nj.html" />  <!-- include块设置src和name属性，则为引入其他文件中的某个模板(4) -->
    this the main demo{no}.
  </div>
</template>
```

template2.nj.html：
```html
<template name="tmplModule2">
  <div>
    this the tmplModule2 demo{no}.
  </div>
</template>

<template>  <!-- template标签的name属性也可以不设置，这时会自动设置name属性为"main"(5) -->
  <div>
    this the main2 demo{no}.
  </div>
</template>
```

component.js：

```js
import tmpls from './template.nj.html';
console.log(tmpls);  //{ main:..., tmplModule2:... }，设置local属性的模板无法获取(6)

@registerTmpl('TestComponent')
class TestComponent extends Component {
  render() {
    return tmpls.main({ no: 1 });  //tmpls为对象结构，key值为html文件中template标签的name属性
  }
}

/*渲染组件后输出html:
<div>
  <div>
    this the tmplModule demo1.
  </div>
  <div>
    this the main2 demo1.
  </div>
  <div>
    this the tmplModule2 demo1.
  </div>
  this the test demo1.
</div>
*/
```

1. 单模板文件可由多个`template`标签构成，每个`template`里放置独立的模板。
2. 使用`include`块可以引入其他模板，分为几种情况如例中(2)、(3)、(4)处所示。
3. 每个单模板文件中只能有一个`name="main"`的`template`标签定义为主模板，如例中(1)处。
4. 没有设置`name`属性的`template`标签会自动生成`name="main"`属性，如例中(3)处所示。
5. `template`标签还可以设置`local`属性，这样它就只能在当前单模板文件内被其他模板使用，如例中(6)处。

## 在单模板文件中引入图片

目前`nornj-loader`配合webpack时还暂时不支持直接在单模板文件中用相对路径引入图片，如需引入相对路径图片可以这样做：

testImg.nj.html：

```html
<template name="importImg">
  <img src={imgSrc} />
</template>
```

testImg.js：

```js
import tmpls from './testImg.nj.html';

@registerTmpl('TestImg')
class TestImg extends Component {
  render() {
    return tmpls.importImg({ imgSrc: require('../../images/test.png') });  //在js文件中使用require方法引入相对路径图片，再传到模板中
  }
}
```
{% endraw %}