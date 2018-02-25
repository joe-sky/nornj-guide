{% raw %}
# 模板注释

`NornJ`模板中默认使用`<!--#...#-->`语法，相比html的注释多了一个`#`字符，示例如下：

```html
<div>
  <!--#Something that needs to be commented out#-->
</div>
```

以上模板渲染后会输出`<div></div>`，也就是注释节点内的内容不会实际渲染出来。

> 注：在使用`nornj-react`包后，模板注释的语法默认设置为`<!--...-->`，即和html注释语法默认相同。

* 另外，如需要在模板渲染后输出注释内容，可以直接使用`<!--...-->`语法：

```html
<div>
  <!--Something that needs to be commented out-->
</div>
```

以上模板渲染后会输出`<div><!--Something that needs to be commented out--></div>`。
{% endraw %}