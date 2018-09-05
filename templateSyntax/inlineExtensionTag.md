{% raw %}
# 扩展属性

`扩展属性`即为在html元素开标签内编写的自定义属性，类似于`Vue`及`Angular`中的`指令`。语法为`<element #exTag="value">`，用它可以封装一些实用功能，目的是写更少的代码去做更多的事情。`扩展属性`实际上是扩展标签的语法糖写法：

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

## 内置扩展属性

### 目录

* [show](#show)
* [mobx-bind](#mobx-bind)
* [mst-bind](#mst-bind)

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

### mobx-bind

类似于`Vue`的`v-model`指令，可以使用`#mobx-bind`配合`Mobx`在`<input>`及`<textarea>`等表单元素上创建`双向数据绑定`，它会根据控件类型自动选取正确的方法来更新元素。

* 在线示例(jsfiddle)

[source code](https://jsfiddle.net/joe_sky/wwrLuns2/)

* [在线示例(codepen)](https://codepen.io/joe_sky/pen/mxKdrj)

* 基本使用方法

```js
import { Component } from 'react';
import { observable } from 'mobx';
import nj from 'nornj';

class TestComponent extends Component {
  @observable inputValue = '';

  render() {
    return nj`
      <input :#mobx-bind="inputValue">
    `(this);
  }
}
```

如上所示，无需编写`<input>`标签的`onChange`事件，`inputValue`变量已自动和`<input>`标签建立了`双向数据绑定`的关系。[点这里](https://github.com/joe-sky/nornj-cli/blob/master/docs/guides/antDesign.md#%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95)是一个`#mobx-bind`结合表单组件的示例页面。

* 实质上，`#mobx-bind`的实现原理和`v-model`很类似，上述示例其实就是下面的语法糖形式：

```js
class TestComponent extends Component {
  @observable inputValue = '';
  @autobind
  onChange(e) {
    this.inputValue = e.target.value;
  }

  render() {
    return nj`
      <input value={inputValue} {onChange}>
    `(this);
  }
}
```

* `#mobx-bind`和`#mst-bind`存放在`nornj-react`包中，需要按如下方式引入后方可使用：

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

* `onChange`事件

由于`#mobx-bind`默认自动设置了组件的`onChange`事件，但有些情况下我们可能还是需要在`onChange`中做一些其他的操作：

```js
class TestComponent extends Component {
  @observable inputValue = '1';

  @autobind
  onChange(e) {
    console.log(e.target.value);
  }

  render() {
    return nj`
      <input :#mobx-bind="inputValue" {onChange}>
    `(this);
  }
}
```

如上所示，`onChange`事件的行为和标签原生的`onChange`完全相同，它会在文本框的值变化后执行。

* 使用`action`更新变量

在`mobx`开发中如果启动严格模式或者使用`mobx-state-tree`时，则须要使用`action`来更新变量。可按下面方式配置使用`action`：

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
      <input :#mobx-bind.action="inputValue">
    `(this);
  }
}
```

当有`action`修饰符时，`#mobx-bind`会默认执行camel命名法(`set + 变量名`)定义的`action`，上例中为`setInputValue`。

### mst-bind

`#mst-bind`即为`#mobx-bind`的默认使用`action`来更新值的版本，用来配合`mobx-state-tree`的变量使用：

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
      <input :#mst-bind="testStore.inputValue">
    `({
      testStore: this.props.rootStore.testStore
    });
  }
}
```

如上，`#mst-bind`会默认执行camel命名法(`set + 变量名`)定义的`action`来更新值，上例中为`setInputValue`。除此外`#mst-bind`的其他特性与上述的`#mobx-bind`完全相同。

### 目前mobx-bind和mst-bind默认支持的控件列表

| 控件名称        | 控件类型                  | 备注                     |
|:---------------|:-------------------------|:-------------------------|
| `<input>`      | 原生                      | `<input type="checkbox">`和`<input type="radio">`暂时不支持 |
| `<textarea>`   | 原生                      |                         |
| `<select>`     | 原生                      | `<select multiple>`暂时不支持 |
| `<ant-input>`  | Ant Design                |                         |
| `<ant-input.textarea>`<br>`<ant-textarea>`  | Ant Design                |                         |
| `<ant-checkbox>`  | Ant Design                |                         |
| `<ant-checkbox.group>`<br>`<ant-checkboxgroup>`  | Ant Design                |                         |
| `<ant-radio.group>`  | Ant Design                |                         |
| `<ant-switch>`  | Ant Design                |                         |
| `<ant-select>`  | Ant Design                | `<ant-select mode="multiple">`暂时不支持                        |
| `<ant-cascader>`  | Ant Design                |                         |
| `<ant-datepicker>`<br>`<ant-datepicker.monthpicker>`<br>`<ant-datepicker.weekpicker>`<br>`<ant-datepicker.rangepicker>`<br>`<ant-monthpicker>`<br>`<ant-weekpicker>`<br>`<ant-rangepicker>`  | Ant Design                |                         |
| `<el-input>`   | Element-React                |                         |
| `<el-select>`   | Element-React                | `<el-select multiple>`暂时不支持 |
| `<el-datepicker>`   | Element-React                |                         |
| `<el-daterangepicker>`   | Element-React                |                         |
| `<el-timeselect>`   | Element-React                |                         |
| `<el-timepicker>`   | Element-React                |                         |
| `<el-timerangepicker>`   | Element-React                |                         |
| `<el-switch>`   | Element-React                |                         |
| `<el-checkbox>`   | Element-React                |                         |
| `<el-checkbox.group>`   | Element-React                |                         |
| `<el-radio.group>`   | Element-React                |                         |
| `<el-cascader>`   | Element-React                | &nbsp;                        |
{% endraw %}