{% raw %}
# NornJ 语法(JSX)

`NornJ`可为JS/JSX增加的语法有以下这几类：

* [标签](jsx-syntax/tags.md)

主要用于为JSX扩充流程控制语句，例如循环：

```js
<each of={[1, 2, 3]}>
  <i>{item}</i>
</each>
```

* [指令](jsx-syntax/directives.md)

主要用于为JSX扩充一种新的修改组件`props`的封装形式，例如修改组件style属性的`display`值：

```js
<img n-show={false} />
```

* [过滤器](jsx-syntax/expressions.md#filters)

为JS/JSX扩充类似模板引擎的`过滤器/管道`语法，例如可将各类通用函数统一封装为过滤器使用：

```js
<button>
  {n`${foo} | capitalize`}
</button>
```

* [运算符](jsx-syntax/expressions.md#operators)

可为JS/JSX扩充新的运算符，比如范围运算符：

```js
<input value={n`(1 .. 100).join('-')`} />
```
{% endraw %}