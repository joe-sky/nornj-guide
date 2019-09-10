{% raw %}
# 编译模板并渲染React组件

每个`NornJ`模板都可以编译为1个组件模板函数。传入数据并执行此模板函数则可以输出React虚拟dom组件对象，这样就可以配合React作为JSX的替代模板来开发组件了。

## NornJ模板与JSX的不同点

* `NornJ`模板使用字符串构建，无需预编译也可以直接使用，也可以选择预编译的方式；`JSX`在保证性能与网络开销的前提下，通常都是预编译的方式。
* `NornJ`模板可以在React组件逻辑代码内编写，也可以用单独的文件编写；`JSX`通常只和React组件逻辑代码在一起编写。
* 在`JSX`中使用js表达式与html标签混合的语法来表达逻辑判断等；`NornJ`模板会提供**完全声明式**的标签语法，如`if`或**循环语句**等语法都为标签。
* `NornJ`模板的解析方式和html更加类似，例如不加闭合的`<img>`、`<input>`等标签，以及`style="margin-left:1px;"`等`JSX`不支持的语法在nj中都是可行的。另外NornJ模板还有一些语法如`style`等均与html更加一致，具体请见[这里](../templateSyntax/react.md)。
<!-- * NornJ模板支持直接嵌入在html中的script标签内使用(示例请见[这里](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc-es5/index.html))，可以替代ReactDOM.render方法，这样即使使用非`node.js`的服务器环境也可以做直出html渲染；JSX通常都是在js文件中渲染React组件。 -->

## 将纯字符串模板编译为模板函数

例：

```js
//定义模板
const tmpl = `
<div id=test1>
  this the test demo{no}.
  <i>test{no}</i>
</div>`;

//编译为模板函数
const tmplFn = nj.compileH(tmpl, 'tmpl1');
```

* 将纯字符串编译为模板函数须使用`nj.compileH`方法。该方法第一个参数为纯字符串模板；
* 第二个参数为模板名称，该参数是可选的。如果设置了模板名称(模板名称应为全局唯一)，则下一次编译名称相同的模板时会直接从缓存中获取，这样就会提升很多性能。通常情况下推荐编译时设置该名称参数。

## 执行模板函数并输出React组件

```js
import { compileH, registerComponent } from 'nornj';
import { Component } from 'react';
import { renderToStaticMarkup } from 'react-dom/server';

//定义模板
const tmpl = `
<div id=test1>
  this the test demo{no}.
  <i>test{no}</i>
</div>`;

//编译为模板函数
const template = compileH(tmpl, 'tmpl1');

//定义组件
class TestComponent extends Component {
  render() {
    return template({
      no: this.props.no
    });
  }
}

//注册组件到NornJ模板中
registerComponent('TestComponent', TestComponent);

//输出React组件
let comp = compileH(
 '<TestComponent no=100 />',
 'tmpl2'
)();

//使用renderToStaticMarkup方法输出html
let html = renderToStaticMarkup(comp);
```

输出html：

```js
console.log(html);
/*
<div id="test1">
  this the test demo100.
  <i>test100</i>
</div>
*/
```

* 模板函数一般只传入一个参数，值为json格式的数据。模板中和传入数据中对应的值会自动进行相应替换，最后输出结果为替换后的`React vdom`对象。
* 模板函数的参数也可以传入多个json参数，如下所示：

```js
//定义模板
const tmpl = `
<div id=test1>
  this the test demo{no}.
  <i>test{no2}</i>
</div>`;

//编译为模板函数
const tmplFn = nj.compileH(tmpl, 'tmpl1');

//渲染
let comp = tmplFn({
  no: 100
}, {
  no: 200,  //相同的值优先采用顺序靠前的参数中的(1)
  no2: 300  //如果数组第一个参数没有no2属性，就会尝试从后面的参数中获取(2)
});
let html = renderToStaticMarkup(comp);

console.log(html);
/*输出html:
<div id="test1">
  this the test demo100.
  <i>test300</i>
</div>
*/
```

传入多个参数后，NornJ模板函数在运行时会按顺序检测每个数据对象是否有和模板中对应的值。如果检测到前面的参数有对应值，那么就会停止继续检测后面的参数是否有该对应值，如例中(1)处所示；如果靠前面的参数中没有对应值，那么就按顺序寻找后面的参数中是否存在，如例中(2)处所示。

* 另外还可以用`nj.renderH`方法来执行渲染，它与`nj.compileH`相比则是自动包含了编译模板函数的步骤：

```js
let comp = nj.renderH(`
<div id=test1>
  this the test demo{no}.
  <i>test{no}</i>
</div>`, {
  no: 100
});

let html = renderToStaticMarkup(comp);

console.log(html);
/*输出html:
<div id="test1">
  this the test demo100.
  <i>test300</i>
</div>
*/
```

如上，`nj.renderH`方法的第一个参数为纯字符串模板，从第二个参数开始和`nj.compileH`编译出来的模板函数一样可以传入多个json参数。

## 使用标签模板字符串的方式定义模板

以`nj`为前置标签的模板字符串可以直接像模板函数一样执行，就和执行使用`compileH`方法编译出来的模板函数的效果相同，如下所示：

```js
import { Component } from 'react';
import nj from 'nornj';

class TestComponent extends Component {
  render() {
    return nj`
      <div id=test1>
        this the test demo{no}.
        <i>test{no}</i>
      </div>`({ no: 1 });
  }
}
```

## 渲染Component.subComponent形式的组件

类似于`JSX`，`NornJ`模板中组件的标签名也可以使用`<Component.subComponent />`的形式：

```html
<ant-Form>
  <ant-Form.Item label="id">
    ...
  </ant-Form.Item>
  <ant-Form.Item label="name">
    ...
  </ant-Form.Item>
<ant-Form />
```

## registerTmpl

`registerTmpl`采用es7装饰器的形式，可以使NornJ用更简洁的语法配合React使用，[更多详情请见这里](https://github.com/joe-sky/nornj-react/blob/master/README.md)。

```js
import { Component } from 'react';
import { registerTmpl } from 'nornj-react';

@registerTmpl({
  name: 'TestComponent',  //可传入组件名，相当于调用了nj.registerComponent注册组件，可选参数
  template: `             <!--可传入模板，纯字符串和以nj为前置标签的模板字符串都可以，可选参数-->
    <div id=test1>
      this the test demo{no}.
      <i>test{no}</i>
    </div>
  `
})
class TestComponent extends Component {
  render() {
    return this.template({ no: 1 });  //使用this.template方法渲染，该方法和nj.compileH编译的模板函数是一样的
  }
}
```

## 直接在模板函数中传入React组件

除了可以使用`registerComponent`或`registerTmpl`全局注册组件之外，还可以用以下几种方式直接在模板函数中局部使用`React`组件：

(1) 在nj标签的模板字符中的标签名中，用`${}`的方式嵌入组件：

```js
import Icon from 'react-native-vector-icons/FontAwesome';
import Button from 'antd-mobile/lib/button';

nj`
<${Button} type="primary" size="small" inline>
  <${Icon} name="rocket" size={30} color="#900" />small
</${Button}>`();
```

(2) 在模板函数的参数中，直接传入组件对象：

```js
import Icon from 'react-native-vector-icons/FontAwesome';
import Button from 'antd-mobile/lib/button';

nj`
<{Button} type="primary" size="small" inline>
  <{Icon} name="rocket" size={30} color="#900" />small
</{Button}>`({  //标签名格式为"{tagName}"
  Button，
  Icon
});
```

(3) 使用模板函数中的`components`参数传入组件，`components`参数只能写在模板函数的第一个参数中，需传入一个以组件名为key的对象：

```js
import Icon from 'react-native-vector-icons/FontAwesome';
import Button from 'antd-mobile/lib/button';

nj`
<Button type="primary" size="small" inline>
  <Icon name="rocket" size={30} color="#900" />small
</Button>`({  //标签名格式和一般标签相同
  components: {
    Button,
    Icon
  }
});
```

`components`参数也可以传入数组，如下：

```js
...

nj`
<Button type="primary" size="small" inline>
  <Icon name="rocket" size={30} color="#900" />small
</Button>`({
  components: [{
    Button
  }, {
    Icon
  }]
});
```
{% endraw %}