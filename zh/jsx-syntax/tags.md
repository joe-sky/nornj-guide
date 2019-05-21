{% raw %}
# 标签 {#top}

在React开发中，`NornJ`提供了一种可扩展的特殊组件语法，称为`标签`。这种语法最常见的使用场景就是流程控制语句：

```js
const Test = props => (
  <If condition={props.isTest}>
    <i>success</i>
    <Else>
      <i>fail</i>
    </Else>
  </If>
);
```

上例中的`<If>`、`<Else>`等都是标签语法。

# 与React组件的区别 {#different-from-react}

简单地说，`NornJ`标签可以实现以下几种普通`React`组件无法实现的功能：

* [延迟渲染子节点](#lazy-render-children)
* [生成子节点可用的新变量](#generate-new-variable)

下面我们用实例分别解释下这些特性。

## 延迟渲染子节点 {#lazy-render-children}

从上面`<if>`的例子我们不难想到，其实用React的组件语法不是也是可以实现么？确实可以实现，比如[react-if](https://github.com/romac/react-if)：

```js
import { If, Then, Else } from 'react-if';

const Foo = ({ data }) => (
  <div>
    <If condition={false}>
      <Then>{() =>
        renderData(data)
      }</Then>
      <Else>
        Nothing to see here
      </Else>
    </If>
  </div>
);
```

但是可以看出，`react-if`需要一个额外的`<Then>`标签；而且文档中也注明了，如果不在`<Then>`或`<Else>`中写一个返回子节点的函数是会存在性能消耗的。因为在并不确定`condition`的值之前，所有的分支节点都没必要进行提前渲染。

然而`NornJ`的`<If>`标签则不存在上述问题，因为它的本质并不是React组件而是一个`模板函数`，由配套的babel插件进行了转换：

```js
const test = props => (
  nj.renderH(`
    <if condition={_njParam0}>
      {#_njParam1}
      <else>
        {#_njParam2}
      </else>
    </if>
  `, {
    _njParam0: props.isTest,
    _njParam1: () => <i>success</i>,
    _njParam2: () => <i>fail</i>
  });
);
```

从上面可以看出，`<i>success</i>`等其实是包在函数内并没立即执行的。

> 另外，这并不是`NornJ`标签最终的运行时代码，`NornJ`配套的babel插件还会做进一步的模板预编译以提高性能。

## 生成子节点可用的新变量 {#generate-new-variable}

例如`<Each>`循环：

```js
const Test = props => (
  <div>
    <Each of={[1, 2, 3]}>
      <i>{item}</i>
    </Each>
  </div>
);
```

上例中使用`NornJ`的`each`标签实现了循环数组`[1, 2, 3]`，然后在子节点中使用新生成的`item`变量渲染循环中每一项的值。而使用常规`JSX`写法的组件则必须使用函数才能实现，比如[react-loops](https://github.com/leebyron/react-loops)：

```js
import { For } from 'react-loops';

const Test = props => (
  <div>
    <For of={[1, 2, 3]}>
      {item =>
        <li>{item}</li>
      }
    </For>
  </div>
);
```

上述虽然是`JSX`的常规写法，但是标签子节点中插入的`额外花括号`、`函数体`等，可能或多或少还是增加了少量代码，以及影响了一点点标签嵌套的可读性。

***

下面是`NornJ`已有内置的标签：

## If

示例：

```js
const Test = props => (
  <div>
    This is a if tag demo.
    <If condition={props.type}>
      test if tag
      <span>test1</span>
    </If>
  </div>
);
```

如上，如果`if`标签的`condition`参数计算结果为true，则会渲染`if`标签内的子节点；如为false则不会渲染`if`标签内的任何东西。

* if标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| condition           | Boolean       | if标签子节点的渲染条件 |

`if`标签还包含`else`、`elseif`等子标签。

## Else

示例：

```js
const Test = props => (
  <div>
    This is a if tag demo.
    <If condition={props.type}>
      test if tag
      <span>test1</span>
      <Else>
        <span>test2</span>
      </Else>
    </If>
  </div>
);
```

上例中如果`if`标签的`condition`参数值为false，则会渲染`else`标签内的子节点；否则会渲染`if`标签内除了`else`标签外的其他内容：

```js
ReactDOM.render(<Test type={false} />, document.body);

/* 以上渲染内容为：
<div>
  This is a if tag demo.
  <span>test2</span>
</div>
*/
```

## Elseif

`elseif`标签可以实现多分支流程：

```js
const Test = props => (
  <div>
    <If condition={props.num > 100}>
      100
      <Elseif condition={props.num > 50}>
        50
      </Elseif>
      <Elseif condition={props.num > 20}>
        20
      </Elseif>
      <Else>
        0
      </Else>
    </If>
  <div>
);

ReactDOM.render(<Test num={30} />, document.body);

/* 以上渲染内容为：
<div>20</div>
*/
```

* elseif标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| condition           | Boolean       | elseif标签子节点的渲染条件 |

## Each

`each`标签可以实现循环：

```js
<Each of={numbers}>   //要循环的数组
  <i>num: {item}</i>  //item表示使用数组项
  <i>no: {index}</i>  //index表示使用数组项索引值
</Each>
```

在循环中内嵌`if`标签：

```js
<Each of={numbers}>
  <If condition={first}>show first<br/></If>  //first表示数组第一项
  <If condition={last}>show last</If>         //last表示数组最后一项
</Each>
```

如要循环的数组为空，则可以渲染`empty`标签的内容：

```js
<Each of={numbers}>
  <span>test {item.no}</span>
  <empty>
    <span>no data</span>
  </empty>
</Each>
```

* `each`标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| of           | 数组       | 要循环的数组 |
| item           | String       | 循环中生成的每项变量名，可以改变 |
| index           | String       | 循环中生成的每项索引值变量名，可以改变 |
| first           | String       | 循环中生成的第一项变量名，可以改变 |
| last           | String       | 循环中生成的最后一项变量名，可以改变 |

## For

`for`标签是`each`标签的别名，用法是完全一样的：

```js
<For of={numbers}>
  <span>test {item.no}</span>
  <empty>
    <span>no data</span>
  </empty>
</For>
```

## Switch

`switch`标签也可以实现多分支流程：

```js
const Test = props => (
  <div>
    <Switch value={props.num}>
      <Case value={50}>
        50
      </Case>
      <Case value={30}>
        30
      </Case>
      <Default>
        0
      </Default>
    </Switch>
  <div>
);

ReactDOM.render(<Test num={30} />, document.body);

/* 以上渲染内容为：
<div>30</div>
*/
```

* `switch`和`case`标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| value           | Any       | 在switch标签的value参数传入要判断值；<br>然后其会和case标签中的value值进行`===`判断；<br>所有case都不匹配时则渲染default标签的子节点 |

## With

`with`标签主要用于在`JSX`中创建新的变量：

```js
<Each of={[1, 2, 3]}>
  <With num={item} i={index}>
    <span>test-{num}-{i}</span>
  </With>
</Each>
```

## MobxObserver

# 开发新的标签 {#create-new-tag}

`NornJ`的标签都是支持可扩展的，也就是说与React组件一样可以自行封装各种新功能。例如实现一个`customIf`标签：

```js
<CustomIf condition={foo}>
  test if
  <Else>
    test else
  </Else>
</CustomIf>
```

每个标签都是一个函数，使用`nj.registerExtension`方法注册：

```js
nj.registerExtension('customIf', options => {
  const { props, children } = options;
  if (props.condition) {
    return children();  //输出标签的子节点，即"test if"
  }
  else {
    return props['else'];  //输出else标签的子节点，即"test else"
  }
});
```

* 可以一次定义多个全局标签：

```js
nj.registerExtension({
  customIf: options => {...},
  customSwitch: options => {...}
});
```

### 标签扩展函数的options参数 {#tag-options}

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| children            | Function        | 返回需要渲染的标签子节点 |
| props             | Object          | 当前标签的各行内属性值(即`<tag a=1 b=2>`中的`a`和`b`) |

## 开发一个最简单的标签 {#a-simple-tag}

## 更复杂的标签 {#more-complex-tag}

## 附属标签 {#subsidiary-tag}
{% endraw %}