{% raw %}
# 插值变量

在模板内可以定义插值变量，语法默认为`Mustache`语法如`{{参数名}}`，即双花括号。插值变量的作用是在模板编译后，输出html字符串或vdom对象时可用数据插入定义的参数。

* 自定义插值语法

可使用`nj.config`方法修改插值语法的默认规则(具体请看[模板全局配置](../api/config.md))，比如可以将双花括号改为单花括号。

> 在使用`nornj-react`包后，插值变量语法默认改为使用单花括号如`{参数名}`，这样做是为了在`React`中使模板语法更简练且与`JSX`语法更为接近。在本项目的文档中，`React`相关的示例默认使用单花括号，其他则默认使用双花括号。

* 在元素节点参数中定义插值变量

```html
<div id={{id}} name="{{id}} {{name}}">
```

如上例，元素属性节点中的值可以省略不写引号，但是如果属性中包含空格则必须写引号。

* 在文本节点中定义插值变量
```html
<div>{{content}}</div>
```

* 使用双花括号形式的插值变量，在模板输出html字符串时是会自动进行字符转义的，这样的目的是为了防止xss攻击等。但是也可以设置不进行转义，就须要用3个花括号的形式定义插值变量，如下所示：

```html
<div>{{{content}}}</div>
```

如果改变了插值变量的语法规则，如将`{{参数名}}`改成了`{%参数名%}`，则非转义语法就变为`{{%参数名%}}`，即前后额外各添加一个首尾字符。

> 在`React`开发中，`NornJ`模板内任何形式的插值语法则都不会进行转义，因为`React`会替我们进行特殊字符转义。

* 插值变量可支持点号或数组下标语法获取属性值：

```html
<div>{{content.prop1.prop2['prop3']}}</div>
```

另外，上面的链式取值方式如遇到`content`或`prop1`等值为`null`的情况时并不会报出错误。这一特点与`Nunjucks`模板较为类似，故在`NornJ`模板中可以更放心地使用链式取值。

* 插值变量内可以包含空格或换行：

```html
<div>{{
  content[0].length
}}</div>
```

* 插值变量内可放入数字、字符串等js基本类型：

```html
<div>{{'content'}} {{500.05}} {{null}} {{undefined}} {{false}}</div>
```

模板中也可以插入复杂类型(如对象或数组)和js表达式及语句，具体请见[表达式](filter.md#表达式)、[内置过滤器](built-inFilter.md)、[内置扩展标签](built-inExtensionTag.md)。

* 与`JSX`类似，可使用延展属性表示元素节点参数：

```js
const props = {
  foo: 1,
  bar: 2
};
const html = nj`<div {{...props}}></div'>`({ props });  //展开对象作为节点属性

console.log(html);  //输出：<div foo="1" bar="2"></div'>
```

* 定义元素节点参数的简易写法：

```js
let foo = 'bar';
let html = nj`<div {{foo}}></div'>`({ foo });  //此语法等同于"foo={{foo}}"，可省略写属性名

console.log(html);  //输出：<div foo="bar"></div'>
```

* 元素节点名称也可以设置为插值变量，如下所示：

```html
<{{element}}>this is content</{{element}}>
```

1. 元素节点名称作为插值变量时，插值变量内不可以有空格，如`{{ element }}`、`{{element|filter(' ')}}`都是错误的。
2. 另外，利用此功能可以实现`React`开发中的`高阶组件`，将组件类作为插值变量传入模板中即可，如下例：

```js
import { Component } from 'react';
import { render } from 'react-dom';

class TestComponent extends Component {
  render() {
    return nj`<div>{no}</div>`(this.props);
  }
}

render(nj`<{component} no={100} />`({
  component: TestComponent
}), document.body);
```

* 标签属性值放入插值变量的简易写法：

在标签的属性名前加`:`，相应的属性值会自动包裹在插值变量中：

```js
let html = nj`<div :id="'div' + (i + 1)" :name="'test'"></div'>`({ i: 1 });  //此语法等同于<div id="{{'div' + (i + 1)}}" name="{{'test'}}"></div'>

console.log(html);  //输出：<div id="div2" name="test"></div'>
```
{% endraw %}