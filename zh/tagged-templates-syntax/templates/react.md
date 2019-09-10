{% raw %}
# React开发中与JSX的不同点

### style参数

在`JSX`中须要使用对象的方式给`style`属性赋值；在`NornJ`模板中可以直接使用与`html`完全相同的`style`语法：

```html
<div style="color:purple;font-size:1rem;">test</div>
```

### class和for参数

在`JSX`中须要使用`className`和`htmlFor`来给`class`和`for`赋值；在`NornJ`模板中可以直接使用与`html`完全相同的参数名：

```html
<input type="checkbox" name="test" class="cbx" />
<label for="test">test</label>
```
{% endraw %}