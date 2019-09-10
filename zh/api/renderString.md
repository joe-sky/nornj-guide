{% raw %}
# 编译模板并渲染html字符串

每个`NornJ`模板都可以编译为1个模板函数。传入数据并执行此模板函数则可以输出html(或者是xml、普通文本，以下统一称为输出html)字符串，这样就可以配合`Backbone`、`express`、`koa`等框架作为视图的模板引擎了。

## 将纯字符串模板编译为模板函数

例：

```js
//定义模板
const tmpl = `
<div id=test1>
  this the test demo{{no}}.
  <i>test{{no}}</i>
</div>`;

//编译为模板函数
const tmplFn = nj.compile(tmpl, 'tmpl1');
```

* 将纯字符串编译为模板函数须使用`nj.compile`方法，该方法第一个参数为纯字符串模板；
* 第二个参数为模板名称，该参数是可选的。如果设置了模板名称(模板名称应为全局唯一)，则下一次编译名称相同的模板时会直接从缓存中获取，这样就会提升很多性能。通常情况下推荐编译时设置该名称参数。

### 执行模板函数并输出html

例：

```js
//定义模板
const tmpl = `
<div id=test1>
  this the test demo{{no}}.
  <i>test{{no}}</i>
</div>`;

//编译为模板函数
const tmplFn = nj.compile(tmpl, 'tmpl1');

//输出html
let html = tmplFn({
  no: 100
});

console.log(html);
/*输出html:
<div id="test1">
  this the test demo100.
  <i>test100</i>
</div>
*/
```

* 模板函数一般只传入一个参数，值为json格式的数据。模板中和传入数据中对应的值会自动进行相应替换，最后输出结果为替换后的html字符串。
* 模板函数的参数也可以传入多个json参数，如下所示：

```js
//定义模板
const tmpl = `
<div id=test1>
  this the test demo{{no}}.
  <i>test{{no2}}</i>
</div>`;

//编译为模板函数
const tmplFn = nj.compile(tmpl, 'tmpl1');

//渲染
let html = tmplFn({
  no: 100
}, {
  no: 200,  //相同的值优先采用顺序靠前的参数中的(1)
  no2: 300  //如果数组第一个参数没有no2属性，就会尝试从后面的参数中获取(2)
});

console.log(html);
/*输出html:
<div id="test1">
  this the test demo100.
  <i>test300</i>
</div>
```

传入多个参数后，NornJ模板函数在运行时会按顺序检测每个数据对象是否有和模板中对应的值。如果检测到前面的参数有对应值，那么就会停止继续检测后面的参数是否有该对应值，如例中(1)处所示；如果靠前面的参数中没有对应值，那么就按顺序寻找后面的参数中是否存在，如例中(2)处所示。

* 另外还可以用`nj.render`方法来执行渲染，它与`nj.compile`相比则是自动包含了编译模板函数的步骤：

```js
let html = nj.render(`
<div id=test1>
  this the test demo{{no}}.
  <i>test{{no}}</i>
</div>`, {
  no: 100
});

console.log(html);
/*输出html:
<div id="test1">
  this the test demo100.
  <i>test100</i>
</div>
*/
```

如上，`nj.render`方法的第一个参数为纯字符串模板，从第二个参数开始和`nj.compile`编译出来的模板函数一样可以传入多个json参数。

## 使用标签模板字符串的方式定义模板

以`nj`为前置标签的模板字符串可以直接像模板函数一样执行，就和执行使用`compile`方法编译出来的模板函数的效果相同，如下所示：

```js
let html = nj`<span>test{{no}}</span>`({ no: 1 });

console.log(html);
/*输出html:
<span>test1</span>
*/
```
{% endraw %}