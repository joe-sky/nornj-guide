{% raw %}
# 行内扩展标签

`行内扩展标签`即为定义在html元素开标签内的`NornJ`扩展标签，语法为`<element #exTag="value">`。内置的`行内扩展标签`如下：

### show

类似于`Vue`的`v-show`指令，`#show`用于切换元素是否显示，原理为修改style：

```js
const html = nj`
<div #show="{{isShow}}">
  test inline extension tag
</div>
`({ isShow: false });

console.log(html);  //<div style="display:none">test inline extension tag</div>
```
{% endraw %}