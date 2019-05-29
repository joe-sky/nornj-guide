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
* [封装包装组件](#encapsulate-wrapped-component)

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

## 封装包装组件 {#encapsulate-wrapped-component}

设置(或修改)`JSX`标签的属性值是`NornJ`的指令最基本的功能。指令还能实现更高级的功能，可以在当前指令所在组件的外层再套自定义逻辑的包装组件。下面我们看一个简单的应用例子(使用[ant-design的Tooltip组件](https://ant.design/components/tooltip/))。

`ant-design的Tooltip组件`常规的写法：

```js
import { Tooltip, Button } from 'antd';

ReactDOM.render(
  <div className="demo">
    <Tooltip placement="topLeft" title={text}>
      <Button>TL</Button>
    </Tooltip>
  </div>
)
```

然而可以使用`NornJ指令`的扩展开发方式将上面的Tooltip组件封装在一个包装组件之中，这样就可以像下面这种方式使用：

```js
ReactDOM.render(
  <div className="demo">
    <Button n-tooltip-topLeft={text}>TL</Button>
  </div>
)
```

如上，使用了指令后组件树结构减少了一层，看起来会更加简洁清晰。上述`n-tooltip`指令的扩展实现方式，我们将在本章节最后详细阐述。

***

下面是`NornJ`已有内置的指令：

## n-show

使用`n-show`可以在JSX中很方便地切换标签的`style.display`值是否为none，当值为`false`时不显示：

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

* `n-show指令`与`<If>标签`的区别

| 语法               | 特点            | 建议使用场景       |
|:------------------|:----------------|:----------------|
| `n-show`          | 初始渲染开销大；切换时开销小       | 在条件频繁切换时使用，性能会更好 |
| `<If>`            | 初始渲染开销小；切换时开销大       | 在条件很少改变时使用，性能会更好 |

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

使用`n-mobxBind`指令可以配合`Mobx`的可观察变量在`<input>`及`<textarea>`等表单元素上创建`双向数据绑定`，它会根据控件类型自动选取正确的方法来更新值。

* 基本使用方法

```js
import { Component } from 'react';
import { observable } from 'mobx';

class TestComponent extends Component {
  @observable inputValue = 'test';

  render() {
    return <input n-mobxBind={this.inputValue} />;
  }
}
```

如上所示，无需编写`<input>`标签的`onChange`事件，`inputValue`变量已自动和`<input>`标签建立了`双向数据绑定`的关系。

* 实质上，`n-mobxBind`的实现原理其实就是下面的语法糖形式：

```js
class TestComponent extends Component {
  @observable inputValue = 'test';

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
  @observable inputValue = 'test';

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

可以使用`debounce`参数为`n-mobxBind`提供防抖效果：

```js
import { Component } from 'react';
import { observable } from 'mobx';

class TestComponent extends Component {
  @observable inputValue = '';

  render() {
    return (
      <>
        <input n-mobxBind-debounce={this.inputValue} />
        <input n-mobxBind-debounce$200={this.inputValue} />
      </>
    );
  }
}
```

上例中的`debounce`参数默认值为`100毫秒`。也支持自定义设置，如例中为`debounce`加修饰符即可。

* 使用action更新变量

在`Mobx`开发中如果启动严格模式或者使用`mobx-state-tree`时，则须要使用action来更新变量。可按下面方式配置使用action：

```js
import { observable, action, configure } from 'mobx';

// don't allow state modifications outside actions
configure({ enforceActions: true });

class TestComponent extends Component {
  @observable inputValue = 'test';

  @action.bound
  setInputValue(value, args) {
    this.inputValue = value;  //value是用户输入的新值
    console.log(args);        //args为控件onChange事件的全部参数，类型为数组
  }

  render() {
    return <input n-mobxBind-action={this.inputValue} />;
  }
}
```

当有`action`参数时，`n-mobxBind`会默认执行camel命名法(`set + 变量名`)定义的`action`，上例中为`setInputValue`。

接下来我们来按控件分类列举下`n-mobxBind`指令可支持的场景：

### 绑定原生表单控件 {#n-mobxbind-formitem}

原生表单控件包含`文本框`、`复选框`、`单选按钮`、`选择框`等，以上都可以直接使用`n-mobxBind`指令，会自动监听相应控件的`onChange`事件并正确地更新值。

#### 文本框 {#n-mobxbind-input}

单行文本框：

```js
class TestComponent extends Component {
  @observable inputValue = 'test';

  render() {
    return (
      <>
        <input n-mobxBind={this.inputValue} />
        <p>Message is: {this.inputValue}</p>
      </>
    );
  }
}
```

多行文本框：

```js
class TestComponent extends Component {
  @observable inputValue = 'test';

  render() {
    return (
      <>
        <textarea n-mobxBind={this.inputValue}></textarea>
        <p>Message is: {this.inputValue}</p>
      </>
    );
  }
}
```

#### 复选框 {#n-mobxbind-checkbox}

单个复选框，绑定到布尔值：

```js
class TestComponent extends Component {
  @observable checked = false;

  render() {
    return (
      <>
        <input type="checkbox" id="checkbox" n-mobxBind={this.checked} />
        <label for="checkbox">{this.checked}</label>
      </>
    );
  }
}
```

多个复选框，绑定到同一个数组：

```js
class TestComponent extends Component {
  @observable checkedNames = ['Jack', 'Mike'];

  render() {
    return (
      <>
        <input type="checkbox" id="jack" value="Jack" n-mobxBind={this.checkedNames} />
        <label for="jack">Jack</label>
        <input type="checkbox" id="john" value="John" n-mobxBind={this.checkedNames} />
        <label for="john">John</label>
        <input type="checkbox" id="mike" value="Mike" n-mobxBind={this.checkedNames} />
        <label for="mike">Mike</label>
        <br />
        <span>Checked names: {this.checkedNames}</span>
      </>
    );
  }
}
```

#### 单选按钮 {#n-mobxbind-radio}

```js
class TestComponent extends Component {
  @observable picked = '';

  render() {
    return (
      <>
        <input type="radio" id="one" value="One" n-mobxBind={this.picked}>
        <label for="one">One</label>
        <br />
        <input type="radio" id="two" value="Two" n-mobxBind={this.picked}>
        <label for="two">Two</label>
        <br />
        <span>Picked: {this.picked}</span>
      </>
    );
  }
}
```

#### 选择框 {#n-mobxbind-select}

单选时：

```js
class TestComponent extends Component {
  @observable selected = '';

  render() {
    return (
      <>
        <select n-mobxBind={this.selected}>
          <option disabled value="">请选择</option>
          <option>A</option>
          <option>B</option>
          <option>C</option>
        </select>
        <span>Selected: {this.selected}</span>
      </>
    );
  }
}
```

多选时，绑定到一个数组：

```js
class TestComponent extends Component {
  @observable selected = [];

  render() {
    return (
      <>
        <select n-mobxBind={this.selected} multiple n-style="width: 50px;">
          <option>A</option>
          <option>B</option>
          <option>C</option>
        </select>
        <br />
        <span>Selected: {this.selected}</span>
      </>
    );
  }
}
```

用`<Each>`渲染的动态选项：

```js
class TestComponent extends Component {
  @observable selected = 'A';
  options = [
    { text: 'One', value: 'A' },
    { text: 'Two', value: 'B' },
    { text: 'Three', value: 'C' }
  ];

  render() {
    return (
      <>
        <select n-mobxBind={this.selected}>
          <Each of={this.options}>
            <option value={item.value}>{item.text}</option>
          <Each/>
        </select>
        <span>Selected: {this.selected}</span>
      </>
    );
  }
}
```

### 绑定组件 {#n-mobxbind-component}

除了上述的原生表单控件外，`n-mobxBind`指令也可以绑定到任意React组件上。当然，前提是该组件可能需要使用`nj.registerComponent`进行注册，并且设置一些必要的参数。

例如我们注册一个使用[ant-design的Input组件](https://ant.design/components/input/)的例子，首先是注册组件：

```js
import nj from 'nornj';
import { Input } from 'antd';

nj.registerComponent(
  'ant-Input',            //组件名(全局唯一)，类型为字符串
  Input,                  //组件对象
  {                       //组件配置参数对象
    hasEventObject: true    //为true时使用e.target.value获取值
  }
);
```

上述代码在全局统一注册一次就可以了。然后便可以正常地使用`n-mobxBind`指令进行绑定：

```js
import { Component } from 'react';
import { observable } from 'mobx';
import { Input } from 'antd';

class TestComponent extends Component {
  @observable inputValue = 'test';

  render() {
    return <Input n-mobxBind={this.inputValue} />;
  }
}
```

#### 注册组件 {#n-mobxbind-register-component}

在注册很多组件时按各参数的默认值就可以了，也就是说其实可以不写`nj.registerComponent`的第三个参数的。但是也有组件需要配置一些参数，例如：

```js
import nj from 'nornj';
import { Cascader } from 'antd';

nj.registerComponent(
  'ant-Cascader',     //组件名(全局唯一)，类型为字符串
  Cascader,           //组件对象
  {                   //组件配置参数对象，如果下表中的默认配置都满足要求也可以省略
    needToJS: true      //值被更新到该组件前，需要执行一次Mobx.toJS
  }
);
```

所有组件参数列表：

| 参数名             | 类型            | 默认值            | 作用       |
|:------------------|:----------------|:----------------|:----------------|
| hasEventObject    | Boolean         | false           | 为true时，更新事件中使用`<input onChange={e => e.target.value} />`取值。 <br> 为false时，更新事件中使用`<input onChange={value => value} />`取值。 |
| targetPropName    | String          | 'value'         | 如果hasEventObject参数为true，则更新事件中使用`<input onChange={e => e.target[targetPropName]} />`取值。 <br> 不填时默认值是`value`，也就是使用`e.target.value`取值。                |
| valuePropName    | String           | 'value'         | 被绑定控件的值属性名，即`<input value={...} />`中的value属性名称。比如可以依不同组件特性修改为`textValue`、`checked`等等。 |
| changeEventName   | String           | 'onChange'     | 被绑定控件的更新事件属性名，即`<input onChange={...} />`中的onChange属性名称。比如可以依不同组件特性修改为`onInput`、`onTextChange`等等。 |
| needToJS          | Boolean          | false          | 输入的新值在被更新到组件时，是否需要执行一次`Mobx.toJS`。例如一些需要绑定到数组值的组件可能需要设置needToJS为`true`，否则无法正确地更新值到相应的组件中，比如[ant-design的Cascader组件](https://ant.design/components/cascader/)。 <br> 需要进行这一步操作，是由[Mobx可观察变量的特性](https://mobx.js.org/refguide/tojson.html)与该组件的内部实现是否有冲突来决定的，这个有时候也无法避免。 |

#### 已预置注册的组件 {#n-mobxbind-component-preset}

目前[ant-design组件库](https://ant.design/docs/react/introduce)已在[nornj-react](https://github.com/joe-sky/nornj-react/tree/master/antd)包中预置注册了全部组件。也就是说对于`ant-design组件库`无需再手工注册了，按下面方式直接引入就可以使用`n-mobxBind`指令。

首先需要安装[babel-plugin-import插件](https://github.com/ant-design/babel-plugin-import)，并在`.babelrc`增加以下配置：

```js
"plugins": [
   ...
   [
     "import",
     {
       "libraryName": "nornj-react/antd",
       "style": true
     }
   ],
   ...
]
```

然后这样引入使用各`ant-design组件`即可：

```js
import {
  Table,
  Input,
  Button,
  Pagination,
  Tabs,
  Tree,
  Select,
  Checkbox,
  Modal,
  message,
  Row,
  Col,
  Form,
  DatePicker,
  Icon,
  Steps,
  Divider
} from 'nornj-react/antd';  //注意，此处由"antd"改为"nornj-react/antd"

...
class TestComponent extends Component {
  @observable inputValue = 'test';

  render() {
    return <Input n-mobxBind={this.inputValue} />;
  }
}
```

## n-mstBind

`n-mstBind`即为`n-mobxBind`的默认使用action来更新值的版本，用来配合`mobx-state-tree`的变量使用：

store：

```js
import { types } from "mobx-state-tree";

const TestStore = types.model("TestStore",
  {
    inputValue: 'test'
  })
  .actions(self => ({
    setInputValue(value, args) {
      self.inputValue = value;  //value是用户输入的新值
      console.log(args);        //args为控件onChange事件的全部参数，类型为数组
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

`NornJ`的指令都是支持可扩展的，也就是说可以自行封装各种新功能。

## 开发一个最简单的指令 {#a-simple-directive}

例如实现一个`n-class`指令，功能即为与[classnames](https://github.com/JedWatson/classnames)库相同：

```js
<div id="test" n-class={{ foo: true, bar: true }}>Test</div>
/* 以上渲染内容为：
<div id="test" class="foo bar">Test</div>
*/
```

上面的`n-class`指令实际上是一个扩展函数，使用`nj.registerExtension`方法注册：

```js
import nj from 'nornj';
import classNames from 'classnames';

nj.registerExtension(
  'class',     //注意：指令名称需要使用小写开头的camel命名方式
  options => {
    const {
      tagProps,  //指令所在组件的props对象，本例中为{ id: 'test' }
      value      //指令值函数，注意它是个函数需要执行才能取到结果
    } = options;

    //在组件渲染前，使用classNames库来设置className属性的值
    tagProps.className = classNames(
      value()  //此处返回例中的{ foo: true, bar: true }
    );
  }
);
```

配置`.babelrc`(该例中此步骤也可以省略)：

```js
{
  ...
  "plugins": [
    [
      "nornj-in-jsx",
      {
        "extensionConfig": {
          "class": {
            "isDirective": true
          }
        }
      }
    ]
  ]
}
```

这样我们就成功开发了一个`n-class`指令，该实例演示了`NornJ`指令的[操作将传入组件的props值](#set-component-props)功能。

## 更复杂的指令 {#more-complex-directive}

接下来我们来实现一个内部封装了包装组件的指令`n-tooltip`，它的作用和[ant-design的Tooltip组件](https://ant.design/components/tooltip/)是一样的：

```js
<div>
  <Button n-tooltip-topLeft={text}>TL</Button>
</div>
```

首先，我们组要实现一个包装组件`WrappedTooltip.jsx`：

```js
import React from 'react';
import { Tooltip } from 'antd';

const WrappedTooltip = ({
    TooltipDirectiveTag,      //指令所在组件的组件对象；如果是原生html标签的话就是标签名字符串，如div
    tooltipDirectiveOptions,  //指令扩展函数的options对象
    ...tagProps               //指令所在组件的props对象
  }) => {
  const {
    props,
    value
  } = tooltipDirectiveOptions;

  //获取指令参数
  const args = props && props.arguments;

  return (
    <Tooltip
      placement={
        (args && args[0].name) || 'rightTop'  //指令的第一个参数传递到Tooltip组件的placement属性，即显示位置
      }
      title={value()}  //指令的值传到Tooltip组件的title属性，即显示文本
    >
      <TooltipDirectiveTag  //此处渲染指令所在组件
        {...tagProps}       //传递指令所在组件的props对象
      />
    </Tooltip>
  );
};

export default WrappedTooltip;
```

然后使用`nj.registerExtension`方法注册扩展函数：

```js
import nj from 'nornj';
import WrappedTooltip from './WrappedTooltip.jsx';

nj.registerExtension(
  'tooltip',     //注意：指令名称需要使用小写开头的camel命名方式
  options => {
    const {
      tagName,    //指令所在组件对象
      tagProps,   //指令所在组件的props对象
      setTagName  //运行此函数，可以修改当前即将渲染的组件对象
    } = options;

    setTagName(WrappedTooltip);  //将当前渲染的组件修改为包装组件
    tagProps.TooltipDirectiveTag = tagName;      //传递指令所在组件对象到包装组件中
    tagProps.tooltipDirectiveOptions = options;  //传递指令的options到包装组件中
  }
);
```

> 上例有个需要注意的地方，就是`TooltipDirectiveTag`和`tooltipDirectiveOptions`参数的命名应当特例化而避免和其他指令的重复。因为这样才能适应同时存在多个含有包装组件的指令的场景，比如`<div n-directive1 n-directive2>`。

这样`n-tooltip`指令就开发完成了，还可以变更参数控制显示方向：

```js
<div>
  <Button n-tooltip-topLeft="test1">TL</Button>
  <Button n-tooltip-topRight="test2">TL</Button>
</div>
```

## 数据绑定指令 {#data-binding-directive}

{% endraw %}