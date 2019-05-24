{% raw %}
# 指令 {#top}

`指令`是一种扩展的HTML(XML)标签属性，`NornJ`也为`JSX`提供了指令语法：

```js
<div n-show={true}>
  Test directive
</div>
```

上例中的`n-show`即为指令语法。

# 指令能做什么 {#what-can-directives-do}

`指令`通常可以用来封装一些实用功能，以实现写更少的代码去做更多的事情为目的。具体来说`NornJ`的指令主要可以实现以下几种功能：

* [操作将传入组件的props值](#set-component-props)
* [封装高阶组件](#encapsulate-hoc)

## 操作将传入组件的props值 {#set-component-props}

`NornJ`的指令最主要的功能就是用来设置(或修改)`JSX`标签的属性值。比如预置指令`n-show`，它就是用来设置JSX标签的`style.display`属性：

```js
<input n-show={false} />
/*
 实际渲染：<input style="display:none" />
*/
```

目前`JSX`原生的语法可以实现类似指令的效果吗？答案是可以的。通常可以使用`JSX延展操作符`来模拟出类似指令的效果，比如[react-hanger的useInput](https://github.com/kitze/react-hanger#useinput)：

```js
const newTodo = useInput('');

<input name="input" {...newTodo.eventBind} />
/*
 实际渲染：<input name="input" value={newTodo.value} onChange={newTodo.onChange} />
*/
```

但是，上面这种方式也存在以下这些问题：

* 封装扩展的内部无法获取JSX标签已有的其他属性值，比如上例中的`name="input"`。这在开发一些功能时会有局限。
* 写法与常规的JSX属性区别较大，可读性差一些。

然而`NornJ`的指令语法可以完美解决上述问题。

## 封装高阶组件 {#encapsulate-hoc}

设置(或修改)`JSX`标签的属性值是`NornJ`的指令最基本的功能。但指令还能实现更高级的功能，以一种更简单的语法应用高阶组件。下面我们看一个简单的应用例子(使用[ant-design的tooltip组件](https://ant.design/components/tooltip/))：



***

下面是`NornJ`已有内置的指令：

## n-show

使用`n-show`可以在JSX中很方便地切换标签的`style.display`属性，当值为`false`时不显示，效果和`Vue`的`v-show`类似：

```js
class TestComponent extends Component {
  render() {
    return <input n-show={this.props.show} />;
  }
}

ReactDOM.render(<TestComponent show={false} />);
/*
 渲染结果：<input style="display:none" />
*/
```

## n-style

使用`n-style`可以在JSX中使用与html语法一致的css写法：

```js
class TestComponent extends Component {
  render() {
    //以下与<input style={{ marginLeft: '10px', padding: 0 }} />效果相同
    return <input n-style="margin-left:10px;padding:0" />;
  }
}
```

在`n-style`中也可以动态嵌入变量：

```js
const cssProp = 'padding';

class TestComponent extends Component {
  render() {
    return <input n-style={`margin-left:${10};${cssProp}:0`} />;
  }
}
```

## n-debounce

使用`n-debounce`可以在JSX中为`input`等表单元素增加防抖效果，以减少用户输入频率而提高性能：

```js
class TestComponent extends Component {
  onChange = e => {
    //每次输入后延迟一定毫秒才触发一次
    console.log(e.target.value);
  };

  render() {
    return (
      <>
        <input n-debounce onChange={this.onChange} defaultValue="test" />
        <input n-debounce={200} onChange={this.onChange} />
      </>
    );
  }
}
```

如上，`n-debounce`的触发事件默认为`onChange`。如果不写`n-debounce`的值，默认为`100毫秒`。

* 指定任意事件

`n-debounce`也可以支持`onChange`以外的其他事件。比如`onInput`，则需要在`n-debounce`后面添加`onInput`参数：

```js
class TestComponent extends Component {
  onInput = e => {
    console.log(e.target.value);
  };

  render() {
    return <input n-debounce-onInput={200} onInput={this.onInput} />;
  }
}
```

## n-mobxBind

类似于`Vue`的`v-model`指令，可以使用`n-mobxBind`配合`Mobx`在`<input>`及`<textarea>`等表单元素上创建`双向数据绑定`，它会根据控件类型自动选取正确的方法来更新元素。

* 基本使用方法

```js
import { Component } from 'react';
import { observable } from 'mobx';

class TestComponent extends Component {
  @observable inputValue = '';

  render() {
    return <input n-mobxBind={this.inputValue} />;
  }
}
```

如上所示，无需编写`<input>`标签的`onChange`事件，`inputValue`变量已自动和`<input>`标签建立了`双向数据绑定`的关系。

* 实质上，`n-mobxBind`的实现原理和`v-model`很类似，上述示例其实就是下面的语法糖形式：

```js
class TestComponent extends Component {
  @observable inputValue = '';

  onChange = e => {
    this.inputValue = e.target.value;
  };

  render() {
    return <input value={this.inputValue} onChange={this.onChange} />;
  }
}
```

* `onChange`事件

由于`n-mobxBind`默认自动设置了组件的`onChange`事件，但有些情况下我们可能还是需要在`onChange`中做一些其他的操作：

```js
class TestComponent extends Component {
  @observable inputValue = '1';

  onChange = e => {
    console.log(e.target.value);
  };

  render() {
    return <input n-mobxBind={this.inputValue} onChange={this.onChange} />;
  }
}
```

如上所示，`onChange`事件的行为和标签原生的`onChange`完全相同，它会在文本框的值变化后执行。

* 增加防抖效果

可以使用`debounce`修饰符为`n-mobxBind`提供防抖效果：

```js
import { Component } from 'react';
import { observable } from 'mobx';

class TestComponent extends Component {
  @observable inputValue = '';

  render() {
    return <input n-mobxBind-debounce$200={this.inputValue} />;
  }
}
```

* 使用`action`更新变量

在`mobx`开发中如果启动严格模式或者使用`mobx-state-tree`时，则须要使用`action`来更新变量。可按下面方式配置使用`action`：

```js
import { observable, action, configure } from 'mobx';

// don't allow state modifications outside actions
configure({ enforceActions: true });

class TestComponent extends Component {
  @observable inputValue = '1';

  @action.bound
  setInputValue(v) {
    this.inputValue = v;
  }

  render() {
    return <input n-mobxBind-action={this.inputValue} />;
  }
}
```

当有`action`参数时，`n-mobxBind`会默认执行camel命名法(`set + 变量名`)定义的`action`，上例中为`setInputValue`。

### 文本框 {#n-mobxbind-input}

### 复选框 {#n-mobxbind-checkbox}

### 单选按钮 {#n-mobxbind-radio}

### 选择框 {#n-mobxbind-select}

### 在组件上使用 {#n-mobxbind-component}

## n-mstBind

`n-mstBind`即为`n-mobxBind`的默认使用`action`来更新值的版本，用来配合`mobx-state-tree`的变量使用：

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
    return <input n-mstBind={this.props.rootStore.testStore} />;
  }
}
```

如上，`n-mstBind`会默认执行camel命名法(`set + 变量名`)定义的`action`来更新值，上例中为`setInputValue`。除此外`n-mstBind`的其他特性与上述的`n-mobxBind`完全相同。

# 开发新的指令 {#create-new-directive}

## 开发一个最简单的指令 {#a-simple-directive}

## 更复杂的指令 {#more-complex-directive}

## 数据绑定指令 {#data-binding-directive}

{% endraw %}