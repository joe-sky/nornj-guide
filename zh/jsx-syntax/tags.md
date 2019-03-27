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

# NornJ标签与React组件的区别

简单地说，`NornJ`标签可以实现以下几种普通`React`组件无法实现的功能：

* **延迟渲染子节点**
* **生成子节点可用的新变量**

下面我们用实例分别解释下这些特性。

## 延迟渲染子节点

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

### 生成子节点可用的新变量

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

<!--
* 标签语法

在`NornJ`模板中`标签`使用闭合的xml节点元素形式定义，节点名称为`#`+`标签名称`，也可定义元素节点参数，语法与普通元素节点类似：

```html
<#ext {{prop1}} {{prop2}} checked prop3="test{{no}}">
  {{children}}
</#ext>
```

* 标签内每个插值变量也都可以使用过滤器或表达式，这样就可以实现更复杂的逻辑，例如：

```html
<#ext {{value1.trim() | filter1}} {{ value2.length * 2 }}>
  test block
</#ext>
```

## 自定义标签

* 标签可支持自定义，这样就可以自行为模板实现各种各样的逻辑及功能。例如实现一个`customIf`全局标签：

```html
<#customIf {{value.trim()}} useUnless>
  test if
  <#else>
    test else
  </#else>
</#customIf>
```

```js
//每个标签都是一个函数，使用nj.registerExtension方法全局注册
nj.registerExtension('customIf', (value, options) => {  //options参数会自动放置在参数列表最后一个，保存一些模板内部成员
  let valueR, ret;
  if (!options.props.useUnless) {  //#customIf标签的useUnless参数，使用options.props.paramName的方式获取
    valueR = !!value;  //"value"即为插值变量"{{value.trim()}}"的执行结果
  } else {
    valueR = !!!value;
  }

  if (valueR) {
    ret = options.result();  //输出标签的子节点，即"test if"
  }
  else {
    ret = options.props['else'];  //输出<#else>的子节点，即"test else"
  }

  return ret;
});
```

* 可以一次定义多个全局标签：

```js
nj.registerExtension({
  customIf: (val, option) => {...},
  customSwitch: (val, option) => {...}
});
```

### 局部标签

* 还可以使用局部标签，将标签函数定义为插值变量即可：

```js
nj`
<#customIf {{true}}>
  test if
</#customIf>`({
  customIf: (val, option) => {...}
});
```

`NornJ`可以利用局部标签的特性，来为一些实际场景提供代码逻辑与结构的分离，例如这个[Ant Design表单验证组件的例子](https://github.com/joe-sky/nornj-cli/blob/master/docs/guides/antDesign.md#%E8%A1%A8%E5%8D%95%E9%AA%8C%E8%AF%81%E7%BB%84%E4%BB%B6%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95)。

### 标签内部的options参数

* 注册标签函数的最后一个参数即为options参数，它是一个对象类型。从options中可以获取到模板内部的一些值，主要用于实现一些复杂的模板扩展功能，如下所示：

```js
nj.registerExtension('customIf', (value, options) => {
  const ctx = options.context;
  console.log(ctx.getData('id'));   //输出100
  console.log(ctx.data[0]);         //输出1
  console.log(ctx.parent.data[0]);  //输出{ list: [1] }
  console.log(ctx.index);           //输出0

  if (value) {
    return options.result();  //输出0
  }
});

nj`
<#each {{list}}>
  <#customIf {{this > 0}}>
    {{@index}}
  </#customIf>
</#each>
`({ list: [1] }, { id: 100 });
```

options参数列表(更多参数及细节待补充)：

| 参数名称           | 类型            | 作用            |
|:------------------|:----------------|:----------------|
| _njOpts           | Object          | 主要用在标签参数数量不固定时，用来判断是否为options参数 |
| context           | Object          | 模板内部的上下文数据 |
| result            | Function        | 执行此函数后，获得当前标签的子节点执行结果 |
| props             | Object          | 当前标签的各行内属性值(即`<#exTag a=1 b=2>`中的`a`和`b`) |
| name              | String          | 当前标签的名称 |
| parentName        | String          | 上一层标签的名称 |

## 内置标签

* 具体请看[内置标签](built-inExtensionTag.md)。
-->
{% endraw %}