{% raw %}
# 模板语法

`NornJ`模板可以使用纯字符串或标签模板字符串(es6)构建，并可适应多种不同使用场景。模板结构与html非常相似，基本示例如下：

```html
<slider>
  this the test slider {{msg}}.
  <sliderItem id="test" onSliderEnd="{{event}}" />
</slider>
```

## 目录

* [插值变量](variable.md)
* [访问器属性](accessor.md)
* [过滤器与表达式](filter.md)
* [内置过滤器](built-inFilter.md)
* [扩展标签](extensionTag.md)
* [内置扩展标签](built-inExtensionTag.md)
* [行内扩展标签](inlineExtensionTag.md)
* [模板注释](comment.md)
* [标签模板字符串(es6)语法](templateString.md)
* [React开发中与JSX的不同点](react.md)
{% endraw %}