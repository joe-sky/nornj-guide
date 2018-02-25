{% raw %}
# 过滤器与表达式

* 在插值变量中可以使用过滤器，来对数据进行一些运算、过滤操作。语法为`{插值变量 | 过滤器1 | 过滤器2...}`，如使用多个过滤器则会按顺序依次执行，如下所示：

```js
nj.registerFilter('2x', num => num * 2);  //每个过滤器都是一个函数，使用nj.registerFilter方法注册

console.log(nj`<div>{{100 | +(50) | 2x}}</div>`());  //输出<div>300</div>
```

* 可以一次定义多个全局过滤器：

```js
nj.registerFilter({
  trim: obj => obj.trim(),
  replace: obj => obj.replace(/id/g, 'test1')
});
```

* 还可以使用局部过滤器，将过滤器函数定义为插值变量即可：

```js
nj`<div>{{1 | test}}</div>`({
  test: value => value + '_test'
});
```

* 过滤器也可以添加参数，语法为`{插值变量 | 过滤器1(参数1,参数2...) | 过滤器2(参数1,参数2...)...}`。在过滤器方法中第一个参数是当前传入的数据；从第二个参数开始依次为这些模板中传入的参数，如下所示：

```js
nj.registerFilter('test', (obj, p1, p2) => {
  console.log(obj);  //输出test
  console.log(p1);   //输出1，过滤器参数的语法规则与插值语法相同
  console.log(p2);   //输出2，过滤器参数也可以传入插值变量动态求值
  return obj;
});

nj`<div>{{data | test(1, arg2)}}</div>`({
  data: 'test',
  arg2: 2
});
```

* 在过滤器的参数中也支持嵌套过滤器：

```html
<#if {{i | filter1(j | filter2(k | filter3))}}>...</#if>
```

* 有参数的过滤器可以省略`|`符号，但是过滤器名称前必须有空格(至少一个空格或换行，除了几种`特殊的过滤器`)，例如：

```html
<#if {{i >=(0) ||(i <=(-10))}}>...</#if>
```

* 过滤器只传一个参数时还支持省略扩号，这样就可以用类似`js表达式`的格式来编写，例如可将上例改写为：

```js
{{i >= 0 || (i <= -10)}}  //注意">="、"<="等符号两侧都要有至少一个空格
```

这种使用过滤器的方式即为`NornJ模板的表达式语法`，实质上是编写过滤器的一种语法糖，规则为捕获过滤器名称后面的`第一个变量(包含链式的变量)`，举例如下：

```js
{{1 + 2 * 3}}    //结果为9
{{1 + (2 * 3)}}  //结果为7
{{a + b.c}}
{{a + b.c.trim() + d.e}}
{{ { a: 1, b: 2 }.b * 100 }}  //结果为200
```

* 还可以直接以函数调用的方式来使用过滤器，称为`函数型过滤器`。例如：

```html
<#each {{list(1, 2, 3, 4, 5)}}>
  {{this}}
</#each>  <!--# 输出：12345 #-->
```

> 注：`函数型过滤器`的前面可以不加空格。

* 在过滤器方法内，可以通过this.x的方式获取一些参数，如下所示：

```js
nj.registerFilter('test', function(obj) {
  console.log(this.getData('id'));  //输出100
  console.log(this.data[0]);    //输出1
  console.log(this.parent.data[0]);  //输出{ list: [1] }
  console.log(this.index);   //输出0
  return obj;
});

nj`
<#each {{list}}>
  {{@index | test}}
</#each>
`({ list: [1] }, { id: 100 });
```
{% endraw %}