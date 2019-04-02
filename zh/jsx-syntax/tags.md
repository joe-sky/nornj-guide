{% raw %}
# 标签 {#top}

在React开发中，`NornJ`提供了一种可扩展的特殊组件语法，称为`标签`。这种语法最常见的使用场景就是流程控制语句：

```js
const Test = props => (
  <if condition={props.isTest}>
    <i>success</i>
    <else>
      <i>fail</i>
    </else>
  </if>
);
```

上例中的`<if>`、`<else>`等都是标签语法。

# NornJ标签与React组件的区别 {#different-from-react}

简单地说，`NornJ`标签可以实现以下几种普通`React`组件无法实现的功能：

* **延迟渲染子节点**
* **生成子节点可用的新变量**

下面我们用实例分别解释下这些特性。

## 延迟渲染子节点 {#lazy-render-children}

从上面`<if>`的例子我们不难想到，其实用React的组件语法不是也是可以实现么？确实可以实现，比如[react-if](https://github.com/romac/react-if)项目：

```js
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

然而`NornJ`的`<if>`标签则不存在上述问题，因为它的本质并不是React组件而是一个`模板函数`，由配套的babel插件进行了转换：

```js
const test = props => (
  nj`
    <#if condition=${props.isTest}>
      #${() => <i>success</i>}
      <#else>
        #${() => <i>fail</i>}
      </#else>
    </#if>
  `()
);
```

从上面可以看出，`<i>success</i>`等其实是包在函数内并没立即执行的。

> 另外，这并不是`NornJ`标签最终的运行时代码，`NornJ`配套的babel插件还会做进一步的模板预编译以提高性能。

## 生成子节点可用的新变量 {#generate-new-variable}

例如`<each>`循环：

```js
const Test = props => (
  <div>
    <each of={[1, 2, 3]}>
      <i>{item}</i>
    </each>
  </div>
);
```

上例中使用`NornJ`的`each`标签实现了循环数组`[1, 2, 3]`，然后在子节点中使用新生成的`item`变量渲染循环中每一项的值。而常规`JSX`写法则必须使用函数才能实现：

```js
const Test = props => (
  <div>
    {[1, 2, 3].map(item => <i>{item}</i>}
  </div>
);
```

上述虽然是官方常规写法，但是标签中插入的`额外花括号`、`函数体`等，可能或多或少会影响一点代码整体的可读性 :smiling_imp:。

***

下面是`NornJ`已有内置的标签：

### if

示例：

```js
const Test = props => (
  <div>
    This is a if tag demo.
    <if condition={props.type}>
      test if tag
      <span>test1</span>
    </if>
  </div>
);
```

如上，如果`if`标签的`condition`参数计算结果为true，则会渲染`if`标签内的子节点；如为false则不会渲染`if`标签内的任何东西。

* if标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| condition           | Boolean       | if标签子节点的渲染条件 |

`if`标签还包含`else`、`elseif`等子标签。

### else

示例：

```js
const Test = props => (
  <div>
    This is a if tag demo.
    <if condition={props.type}>
      test if tag
      <span>test1</span>
      <else>
        <span>test2</span>
      </else>
    </if>
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

### elseif

`elseif`标签可以实现多分支流程：

```js
const Test = props => (
  <div>
    <if condition={props.num > 100}>
      100
      <elseif condition={props.num > 50}>
        50
      </elseif>
      <elseif condition={props.num > 20}>
        20
      </elseif>
      <else>
        0
      </else>
    </if>
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

### each

`each`标签可以实现循环：

```js
<each of={numbers}>   //要循环的数组
  <i>num: {item}</i>  //item表示使用数组项
  <i>no: {index}</i>  //index表示使用数组项索引值
</each>
```

在循环中内嵌`if`标签：

```js
<each of={numbers}>
  <if condition={first}>show first<br/></if>  //first表示数组第一项
  <if condition={last}>show last</if>         //last表示数组最后一项
</each>
```

如要循环的数组为空，则可以渲染`empty`标签的内容：

```js
<each of={numbers}>
  <span>test {item.no}</span>
  <empty>
    <span>no data</span>
  </empty>
</each>
```

* `each`标签的参数列表：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| of           | 数组       | 要循环的数组 |
| item           | String       | 循环中生成的每项变量名，可以改变 |
| index           | String       | 循环中生成的每项索引值变量名，可以改变 |
| first           | String       | 循环中生成的第一项变量名，可以改变 |
| last           | String       | 循环中生成的最后一项变量名，可以改变 |

### switch

`switch`标签也可以实现多分支流程：

```js
const Test = props => (
  <div>
    <switch value={props.num}>
      <case value={50}>
        50
      </case>
      <case value={30}>
        30
      </case>
      <default>
        0
      </default>
    </switch>
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

# 开发新的标签 {#create-new-tag}

`NornJ`的标签都是支持可扩展的，也就是说与React组件一样可以自行封装各种新功能。例如实现一个`customIf`标签：

```js
<customIf condition={foo}>
  test if
  <else>
    test else
  </else>
</customIf>
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
{% endraw %}