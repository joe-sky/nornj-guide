{% raw %}
# 标签

`NornJ`模板中可使用`内置标签`来表示if、else、each等流程控制语句以及更多的功能，也可以支持自定义标签。

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
{% endraw %}