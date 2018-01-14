{% raw %}
# 扩展标签

`NornJ`模板中可使用内置扩展标签来表示if、else、each等流程控制语句以及更多的功能，也可以支持自定义扩展标签。

* 扩展标签语法

在模板中扩展标签使用封闭的xml节点元素形式定义，节点名称为`#`+`扩展标签名称`，也可定义元素节点参数，语法与普通元素节点类似：

```html
<#ext {{prop1}} {{prop2}} prop3="test{{no}}" ...>
  {{children}}
</#ext>
```

* 扩展标签内每个插值变量也都可以添加过滤器，这样就可以实现更复杂的逻辑，例如：

```html
<#ext {{type | filter1}} {{type2 | filter2}}>
  test if block
</#ext>
```

* 自定义扩展标签

扩展标签可支持自定义，这样就可以自行为模板实现各种各样的逻辑及功能。例如实现一个`customIf`扩展标签：

```html
<#customIf {{type | filter1}} useUnless>
  test if
  <#else>
    test else
  </#else>
</#customIf>
```

```js
//每个扩展标签都是一个函数，使用nj.registerExtension方法注册
nj.registerExtension('customIf', (value, options) => {  //options参数会自动放置在参数列表最后一个，保存一些模板内部成员
  let valueR, ret;
  if (!options.props.useUnless) {  //#customIf标签的useUnless参数，使用options.props.paramName的方式获取
    valueR = !!value;  //"value"即为插值变量"{{type | filter1}}"的执行结果
  } else {
    valueR = !!!value;
  }

  if (valueR) {
    ret = options.result();  //输出扩展标签的子节点，即"test if"
  }
  else {
    ret = options.props['else'];  //输出<#else>的子节点，即"test else"
  }

  return ret;
});
```
{% endraw %}