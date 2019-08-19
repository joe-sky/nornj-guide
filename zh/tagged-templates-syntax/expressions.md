{% raw %}
# 表达式扩展语法 {#top}

`NornJ`提供了一种在`tagged templates`语法中写表达式的功能，称为`NornJ 表达式`。主要用作`增强现有的JS表达式`，并实现`过滤器`等内建的新语法。使用方式很简单，即标签名为`n`的`tagged templates`：

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

简单地说，`NornJ`表达式中内建了一些常规JS语法不支持的特性：

### 过滤器

> 过滤器是什么？是一种在模板引擎(或含有模板引擎的框架，如Vue)中常见的功能，主要用于过滤展示数据。可参考Nunjucks的文档：https://mozilla.github.io/nunjucks/templating.html#filters。

`NornJ`过滤器的基本用法：

```js
<i>{n`1234.567 | currency(2.05 | int) | isString`}</i>
/*
  例中先执行currency过滤器返回"$1234.57"，然后再执行isString过滤器判断是否字符串。
  渲染结果：<i>true</i>
*/
```

如上，过滤器的语法的常规模板引擎一致使用`|`符号作为分隔符，并且还支持`为过滤器传参数`和`嵌套过滤器`。更重要的是，还可以扩展出新的过滤器。

### 运算符

`NornJ`表达式中除了支持常规运算符外，还可以支持一些在常规JS语法中不支持的运算符，比如范围运算符`..`：

```js
<i>{n`(1 .. 5).join('-')`}</i>
/*
  例中"1 .. 5"的运算结果是一个含1-5的数组(即"[1, 2, 3, 4, 5]")，然后再执行数组的join方法用"-"连接每项。
  渲染结果：<i>1-2-3-4-5</i>
*/
```

如上，此类自定义运算符是可以在`NornJ`表达式中同常规的JS语法一起运作。同过滤器一样，运算符也可以支持扩展出新的。

## 适配常规 JS 表达式 {#js-expression-compatibility}

`NornJ`表达式对于常规的 JS 语法有非常好的适配性，例如下面列举的这些常用场景都可以支持：

```js
const Test = () => (
  <>
    <i>{n`1 + 2 * 3 - 4 / 5`}</i>
    <i>{n`1 < (2 + 1) && !(5 > 4)`}</i>
    <i>{n`'abc'.trim() + 'def'.substr(1).length`}</i>
  </>
);
```

### 使用变量

借助于`Babel 插件`的转换能力，`NornJ`表达式内可以直接使用当前作用域内可用的变量：

```js
const Test = props => {
  const str = 'abc';
  const str2 = 'def';

  return (
    <>
      <i>{n`props.a > (2 + 1) && !props.b < 4`}</i>
      <i>{n`str.trim() + str2.substr(1).length`}</i>
    </>
  );
};
```

除了上述使用方法外，变量也可以通过`tagged templates`参数方式传入，效果是一样的：

```js
const Test = props => {
  const str = 'abc';
  const str2 = 'def';

  return (
    <>
      <i>{n`${props.a} > (2 + 1) && ${props}.b < 4`}</i>
      <i>{n`${str}.trim() + ${str2}.substr(1).length`}</i>
    </>
  );
};
```

每个`NornJ`表达式计算后都会返回正确的类型，所以也可以和外部 JS 语法混合：

```js
const Test = props => (
  <>
    <i>{n`${props.a} > (2 + 1)` && n`${props}.b < 4`}</i>
  </>
);
```

### 插入过滤器等自定义语法

`NornJ`表达式内可以同时使用`常规 JS 语法`和`过滤器等自定义语法`，这正是它的特色：

```js
const Test = () => {
  const str = 'abc';

  return (
    <>
      <i>{n`str.trim() | capitalize`}</i>
      <i>{n`12345678 | currency`}</i>
      <i>{n`(1 .. 100).length * 100`}</i>
    </>
  );
};
```

### 表达式内目前不支持的语法

`NornJ`表达式虽然适配性很强，但当前还是有一些不支持的语法，如下：

```js
//注意，以下为一些目前不支持的语法
const Test = props => (
  <>
    <i>{n`!!12345`}</i>
    <i>{n`12345++`}</i>
    <i>{n`+12345`}</i>
    <i>{n`'12345'.map((item, i) => item)`}</i>
  </>
);
```

不过，日后不排除`NornJ`会支持上述语法的可能性，已支持的语法已足够支撑常规开发使用。

> 另外，`NornJ`表达式的目标定位是为现有 JS 语法做增强，故它的内部或许并不需要实现全部的 JS 表达式。 