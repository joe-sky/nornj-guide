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

## 目录

* [show](#show)
* [mobx-model](#mobx-model)
* [mst-model](#mst-model)

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

### mobx-model

类似于`Vue`的`v-model`指令，可以使用`#mobx-model`配合`Mobx`在`<input>`及`<textarea>`等表单元素上创建`双向数据绑定`，它会根据控件类型自动选取正确的方法来更新元素。

* 在线示例

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
      <!-- <input :#mobx-model="inputValue" /> 还可以这样写，效果完全一样 -->
    `(this);
  }
}
```

如上所示，无需编写`<input>`标签的`onChange`事件，`inputValue`变量已自动和`<input>`标签建立了`双向数据绑定`的关系。

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

* `#mobx-model`和`#mst-model`存放在`nornj-react`包中，需要按如下方式引入后方可使用：

```js
import 'nornj-react/mobx';
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
      <!-- 使用"options: { ...settings }"语法来设置参数 -->
      <input :#mobx-model="inputValue options: { beforeChange: beforeChange, afterChange: afterChange }" />
    `(this);
  }
}
```

如上所示，`beforeChange`和`afterChange`事件均有两个参数，分别为`当前@observable变量的值`和`onChange事件原生的参数列表`。如用户在`<input>`(初始值为`1`)中继续录入了一个字符`2`，则会按照上例中所示打印相应信息。

* 使用`action`更新`@observable`变量

在`mobx`开发中如果启动严格模式或者使用`mobx-state-tree`时，则须要使用`action`来更新`@observable`变量。可按下面方式配置使用`action`：

```js
import { observable, action, configure } from 'mobx';

// don't allow state modifications outside actions
configure({enforceActions: true});

class TestComponent extends Component {
  @observable inputValue = '1';
  @action.bound
  setInputValue(v) {
    this.inputValue = v;
  }

  render() {
    return nj`
      <input :#mobx-model="inputValue options: { action: true }" />
    `(this);
  }
}
```

`action`参数设置为`true`时，`#mobx-model`会默认执行camel命名法的`set + @observable变量名`的`action`，上例中为`setInputValue`。也可以手工指定`action`名称：

```js
class TestComponent extends Component {
  @observable inputValue = '1';
  @action.bound
  setValueToInputValue(v) {
    this.inputValue = v;
  }

  render() {
    return nj`
      <input :#mobx-model="inputValue options: { action: 'setValueToInputValue' }" />
    `(this);
  }
}
```

### mst-model

`#mst-model`即为`#mobx-model`的默认使用`action`的版本，用来配合`mobx-state-tree`的变量使用，具体请参考上一节。示例如下：

store：

```js
import { types } from "mobx-state-tree";

const TestStore = types.model("TestStore",
  {  
    inputValue: '1'
  })
  .actions(self => ({
    setInputValue(v) {
      self.inputValue = v;
    }
  }));
```

component：

```js
@inject('rootStore')
@observer
class TestComponent extends Component {
  render() {
    return nj`
      <input :#mobx-model="testStore.inputValue" />
    `({
      testStore: this.props.rootStore.testStore
    });
  }
}
```
{% endraw %}