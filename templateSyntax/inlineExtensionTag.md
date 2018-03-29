{% raw %}
# 行内扩展标签

`行内扩展标签`即为定义在html元素开标签内的`NornJ`扩展标签，语法为`<element #exTag="value">`。它实际上是扩展标签的语法糖写法：

```html
<div #show="{false}"></div>
```

完全等价于：

```html
<div>
  <#props>
    <#show>
      {false}
    </#show>
  </#props>
</div>
```

## 内置行内扩展标签

### show

类似于`Vue`的`v-show`指令，`#show`用于切换元素是否显示，原理为修改style：

```js
const html = nj`
<div #show="{{isShow}}">
  test inline extension tag
</div>
`({ isShow: false });

console.log(html);  //<div style="display:none">test inline extension tag</div>
```

## 外部行内扩展标签

### mobx-model

类似于`Vue`的`v-model`指令，可以使用`#mobx-model`配合`Mobx`在`<input>`及`<textarea>`等表单元素上创建`双向数据绑定`，它会根据控件类型自动选取正确的方法来更新元素。在线示例：

[source code](https://jsfiddle.net/joe_sky/wwrLuns2/)

* 基本使用方法

```js
import { Component } from 'react';
import { observable } from 'mobx';
import nj from 'nornj';

class TestComponent extends Component {
  @observable inputValue = '';

  render() {
    return nj`
      <input #mobx-model={inputValue} />
    `(this);
  }
}
```

如上所示，无需编写`<input>`标签的`onChange`事件，`@observable`的`inputValue变量`已自动和`<input>`标签建立了`双向数据绑定`的关系。

* 实质上，`#mobx-model`的实现原理和`v-model`很类似，上述示例其实就是下面的语法糖形式：

```js
class TestComponent extends Component {
  @observable inputValue = '';
  @autobind
  onChange(e) {
    this.inputValue = e.target.value;
  }

  render() {
    return nj`
      <input value={inputValue} />
    `(this);
  }
}
```

* `beforeChange`和`afterChange`事件

由于`#mobx-model`默认自动设置了组件的`onChange`事件，但有些情况下我们可能还是需要在`onChange`中做一些其他的操作。`#mobx-model`提供了`beforeChange`和`afterChange`事件解决上述问题：

```js
class TestComponent extends Component {
  @observable inputValue = '1';
  @autobind
  beforeChange(prevValue, params) {
    console.log(prevValue);               //输出：1
    console.log(params[0].target.value);  //输出：12

    //此处如果return false，则可以阻止onChange和afterChange事件的执行
    //return false;
  }

  @autobind
  afterChange(nextValue, params) {
    console.log(nextValue);               //输出：12
    console.log(params[0].target.value);  //输出：12
  }

  render() {
    return nj`
      <input :#mobx-model="inputValue options: { beforeChange: beforeChange, afterChange: afterChange }" />
    `(this);
  }
}
```

如上所示，`beforeChange`和`afterChange`事件均有两个参数，分别为`当前@observable变量的值`和`onChange事件原生的参数列表`。如用户在`<input>`(初始值为`1`)中继续录入了一个字符`2`，则会按照上例中所示打印相应信息。

<!--* 使用`action`更新`@observable`变量-->

* 需要注意的是，`#mobx-model`标签是一个`NornJ外部扩展`，需要按如下方式引入：

```js
import 'nornj-react/mobx/extension/mobx-model';
```

另外如果使用`nornj-loader`，则需要在`Webpack`的配置中引入`相应的扩展配置文件`：

```js
module: {
  rules: [
    {
      test: /\.t.html(\?[\s\S]+)*$/,
      use: [{
        loader: 'nornj-loader',
        options: {
          outputH: true,
          delimiters: 'react',
          extensionConfig: require('nornj-react/mobx/extensionConfig')
        }
      }]
    },
    ...
  ]
}
```

{% endraw %}