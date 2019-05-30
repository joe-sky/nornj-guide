{% raw %}
# 表达式

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