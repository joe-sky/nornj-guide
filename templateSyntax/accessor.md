{% raw %}
# 访问器属性

与`Vue`或`Mobx`的计算属性功能有些相似，`NornJ`模板中可以使用`访问器属性`来将一些复杂逻辑封装成一个变量，语法为在普通的插值变量前加一个`#`字符，如下例：

```js
const html = nj`<div>{{#prop}}</div'>`({
  info: 'accessor property.',
  prop: () => {  //每个访问器属性都是一个函数，值即为函数的返回值
    return 'This is a ' + ctx.getData('info')
  }
});

console.log(html);  //输出：<div>This is a accessor property.</div'>
```

* `访问器属性`的函数可以有一个`ctx`(即为context)参数，该参数中可以取到模板中的一些数据：

```js
const html = nj`
<div>
  <#each {{list}}>
    {{#prop}}
  </#each>
</div>`({
  id: 100,
  list: [1],
  prop: ctx => {
    console.log(ctx.data[0]);  //输出1
    console.log(ctx.parent.data[0].list);  //输出[1]
    console.log(ctx.index);  //输出0
    return ctx.getData('id');
  }
});

console.log(html);  //输出：<div>100,200</div'>
```

* `访问器属性`也支持链式获取：

```js
const html = nj`
<div>
  {{props.#prop * 2}}
</div>`({
  id: 100,
  props: {
    prop: ctx => {
      return ctx.getData('id') + 50;
    }
  }
});

console.log(html);  //输出：<div>300</div'>
```

* `访问器属性`还可以当做嵌入其他`NornJ`模板函数的插槽，如下所示：

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

console.log(html);  //输出：<div><div>test1</div><div>test2</div></div>
```

* `访问器属性`也支持在`NornJ`的标签模板字符串语法中直接用`${}`语法嵌入使用，语法为`#${}`：

```js
const tmpl = nj`
<div>test</div>
`;

const html = nj`
<div>
  #${tmpl}
</div>
`();

console.log(html);  //输出：<div><div>test</div></div>
```

* 在React开发中，利用`访问器属性`还可将`NornJ`模板内的数据传给`JSX`：

```js
const html = nj`
<div>
  <#each {1 .. 5}>
    #${ctx => <img id={ctx.data[0]} />}
  </#each>
</div>
`();

console.log(ReactDOMServer.renderToStaticMarkup(html));  //输出：<div><img id="1" /><img id="2" /><img id="3" /><img id="4" /><img id="5" /></div>
```
{% endraw %}