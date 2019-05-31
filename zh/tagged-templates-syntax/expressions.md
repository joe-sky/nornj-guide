{% raw %}
# 表达式 {#top}

`NornJ`提供了一种在`tagged-templates`语法中写表达式的功能，主要用作实现`过滤器`与`自定义运算符`两种特有的语法。使用方式很简单，即标签名为`n`的`tagged-templates`：

```js
ReactDOM.render(
  <>
    <input value={n`'Jack' | lowerFirst`} />
    <input value={n`(1 .. 5).join('-')`} />
  </>
)
/*
  渲染结果：<input value="jack" /><input value="1-2-3-4-5" />
*/
```

## 表达式能做什么 {#what-can-expressions-do}

简单地说，`NornJ`表达式中包含一些常规JS语法不支持的特性，主要为`过滤器`与`自定义运算符`。

### 过滤器的作用

> 过滤器是什么？是一种在模板引擎(或含有模板引擎的框架，如Vue)中常见的功能，主要用于过滤展示数据。可参考Nunjucks的文档：https://mozilla.github.io/nunjucks/templating.html#filters。

`NornJ`过滤器的基本用法：

```js
<i>{n`1234.567 | currency(2.05 | int) | isString`}</i>
/*
  例中先执行currency过滤器返回"$1234.57"，然后再执行isString过滤器判断是否字符串。
  渲染结果：<i>true</i>
*/
```

如上，过滤器的语法的常规模板引擎一致使用`|`符号最为分隔符，并且还支持`为过滤器传参数`和`嵌套过滤器`。更重要的是，还可以扩展出新的过滤器。

### 自定义运算符的作用

`NornJ`表达式中还可以支持一些在常规JS语法中不支持的运算符，比如范围运算符`..`：

```js
<i>{n`(1 .. 5).join('-')`}</i>
/*
  例中"1 .. 5"的运算结果是一个含1-5的数组(即"[1, 2, 3, 4, 5]")，然后再执行数组的join方法用"-"连接每项。
  渲染结果：<i>1-2-3-4-5</i>
*/
```

如上，自定义运算符是可以在`NornJ`表达式中同常规的JS语法一起运作。同过滤器一样，自定义运算符也可以支持扩展出新的。