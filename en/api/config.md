{% raw %}
# 模板全局配置

`NornJ`模板可通过修改全局配置来定制一些功能，如插值变量的规则等。

* 通过`nj.config`API可设定各种全局配置，具体有：

| 名称           | 作用          |
|:------------------|:----------------|
| delimiters  | 修改模板语法规则 |
| outputH    | `nj`标签模板字符串返回的模板函数是否默认按`hyperscript`形式输出(即配合`React`的使用方式)，默认值为`false`。引入`nornj-react`包后该值默认设置为`true`。 |
| createElement | `hyperscript`形式的创建元素方法(即为`React`中的`React.createElement`)，默认值为null。引入`nornj-react`包后该值默认设置为`React.createElement`。 |
| includeParser | 模板中解析`<#include>`标签的方法，默认不添加到`nj`对象中。可以从`nornj/tools/includeParser`获取到这个函数，另外在`nornj-loader`内部会自动设置它。
| textMode | 是否使用文本模式解析，默认为`false`。

# 修改模板语法规则

目前可修改的语法规则有：

```js
import nj from 'nornj';

nj.config({
  delimiters: {
    start: '{%',      //插值变量开始字符，默认为"{{"
    end: '%}',        //插值变量结束字符，默认为"}}"
    extension: '$',   //标签前置字符，默认为"#"
    prop: '@-',       //参数标签前置字符，默认为"@"
    comment: '##'     //模板注释，默认为<!--#...#-->语法中的"#"
  }
});

const html = nj`
<div id=test1>
  <@-name>test1</@-name>
  this the test demo{%no%}.
  <$if {%hasNo%}>
    <i>test{%no%} is {{'running'}} in client side</i>
    <!--##Something that needs to be commented out##-->
  </$if>
</div>`();
```

如上例，可将模板规则替换为非默认规则的其他字符。利用这个可以实现用`NornJ`模板在`node.js`服务器端输出要在客户端运行的`NornJ`模板，因为语法规则不同，所以服务器端和客户端的模板不会发生冲突。

# 使用文本模式解析

`NornJ`在解析html字符串时默认全局使用`非文本模式`解析，解析方式和html非常类似，如下所示：

```html
<div>
  <span>   test1</span>    <span>{'test2'}    </span>
</div>
```

以上模板会解析为下面这样，即会忽略多余的空格及换行符等：

```html
<div>
  <span>test1</span>
  <span>test2</span>
</div>
```

但有时候不需要去除多余空格，这在给普通的文本做替换参数时非常有用。那么就可以这样全局开启`文本模式`：

```js
nj.config({
  textMode: true
});
```

这样上面的例子就会解析为：

```html
<div>
  <span>   test1</span>    <span>test2    </span>
</div>
```

有些标签无论是否全局开启`文本模式`，它们的子节点都会使用`文本模式`解析，有如下几个标签：

* style
* script
* textarea
* xmp
* nj-text，自定义的特殊标签，解析时只生成它的子节点，示例如下：

```html
<div>
  <nj-text>
    <span>   test1</span>    <span>{'test2'}    </span>
  </nj-text>
</div>
```

解析为：

```html
<div>
    <span>   test1</span>    <span>test2    </span>
</div>
```
{% endraw %}