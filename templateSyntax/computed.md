{% raw %}
# 计算属性

与`Vue`或`Mobx`的计算属性功能有些相似，`NornJ`模板中也可以使用计算属性来将一些复杂逻辑封装成一个参数。语法为在普通的插值变量前加一个`#`字符，如下例：

> 注：`NornJ`的计算属性都是只读的，并不能像`Vue`那样设置可写属性。`NornJ`模板只关注输出要渲染的内容，因此并没有实现可写属性的意义。

```js
const html = nj`<div>{{#computed}}</div'>`({
  info: 'computed property.',
  computed: ctx => {  //每个计算属性都是一个函数，返回值即为计算结果
    return 'This is a ' + ctx.getData('info')
  }
});

console.log(html);  //输出：<div>This is a computed property.</div'>
```

* 计算属性的函数可以有一个`ctx`(即为context)参数，该参数中可以取到模板中的一些数据：

```js
const html = nj`
<div>
  <#each {{list}}>
    {{#computed}}
  </#each>
</div>`({
  id: 100,
  list: [1],
  computed: ctx => {
    console.log(ctx.data[0]);  //输出1
    console.log(ctx.parent.data[0].list);  //输出[1]
    console.log(ctx.index);  //输出0
    return ctx.getData('id');
  }
});

console.log(html);  //输出：<div>100</div'>
```

* 计算属性还可以当做嵌入其他`NornJ`模板函数的插槽，如下所示：

```js
const tmpl1 = nj`
<div>test1</div>
`;

const tmpl2 = nj`
<div>{{test2}}</div>
`;

const html = nj`
<div>
  {{#tmpl1}}
  {{#tmpl2}}
</div>
`({
  tmpl1,
  tmpl2,
  test2: 'test2'
});

console.log(html);  //输出：<div><div>test1</div><div>test2</div></div'>
```
{% endraw %}