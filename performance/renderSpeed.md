{% raw %}
# 模板渲染效率测试

`NornJ`的模板渲染原理，与一些著名的项目如`Handlebars`或`Vue`的底层实现方式是非常类似的，也就是使用js原生的`new Function`API来动态创建模板函数。

使用`new Function`API动态创建模板函数的方式，相当于一个小型的编译器，理论上可以有无上限的性能优化空间。例如一个简单的模板函数示例：

```js
function anonymous(p1, p2) {
  var _type0 = 'div';
  var _data0 = p2.getData('num') + '_100';
  var _props0 = {
    'id': _data0
  };

  return p1.h(_type0, _props0);
}
```

上例可以再这样进行一些编译阶段的优化，将创建变量的过程省略：

```js
function anonymous(p1, p2) {
  return p1.h('div', {
    'id': p2.getData('num') + '_100'
  });
}
```

这样模板函数的渲染效率就会得到相应提升。

## 大量数据渲染测试

针对于`NornJ`的模板渲染效率，我们做过一个简单的测试，[源码在这里](https://github.com/joe-sky/nornj/blob/master/test/misc/testSpeed.spec.js)。

### 与Handlebars对比

`hbs`样例：

```html
<{{div}} id="{{num}}_100" id2="2">
  {{#each arr}}
    <span class="test_{{@index}}">
      test_{{../num}}
      {{#each ../list2}}
        {{no}}{{no}}<div>{{no}}</div>{{no}}{{no}}
      {{/each}}
    </span>
    {{#five @index}}
      <br />
    {{else}}
      <img />
    {{/five}}
  {{/each}}
</{{div}}>
```

`nj`样例：

```html
<{{div}} id="{{num}}_100" id2="2">
  <#each {{arr}}>
    <span class="test_{{@index}}">
      test_{{../num}}
      <#each {{../list2}}>
        {{no}}{{no}}<div>{{no}}</div>{{no}}{{no}}
      </#each>
    </span>
    <#if {{@index | five}}>
      <br />
      <#else>
        <img />
      </#else>
    </#if>
  </#each>
</{{div}}>
```

如上例，外层循环为`100次`，内层循环为`1000次`，渲染测试耗时为：

```js
hbs:246
nj:171
```

### 与JSX对比

`JSX`样例：

```js
render: function() {
  return (
    <div id={this.state.num + '_100'}>
      {this.props.arr.map((o, i) => [
        o.item.name['fullName'],
        <span className={'test_' + i} style={{ color: 'blue' }} onClick={this.onClick}>
          {'test_' + this.state.num}
          {list2.map((p, j) => <div key={j}>
            <span>{'span' + p.no}</span>
            <i>{p.no}</i>
            <div><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i></div>
          </div>)}
        </span>
      ])}
    </div>
  );
}
```

`nj`样例：

```html
<div id="{num}_100">
  <#each {arr}>
    {item.name['fullName']}
    <span class=test_{@index}
          style={../styles}
          onClick={../onClick}>
      test_{../num}
      <#each {../list2}>
        <div key={@index}>
          <span>span{no}</span>
          <i>{no}</i>
          <div><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i></div>
        </div>
      </#each>
    </span>
    <#if {@index | five(1)}>
      <br />
      <#else>
        <img />
      </#else>
    </#if>
  </#each>
</div>
```

如上例，外层循环为`100次`，内层循环也为`100次`，再各渲染`5次`取平均值，渲染测试耗时为：

```js
jsx:1198
jsx:1164
jsx:1304
jsx:1184
jsx:1136

avg:1197.2

nj:1470
nj:1190
nj:1225
nj:1215
nj:1222

avg:1264.4
```

可见，`NornJ`比`JSX`速度略低一点点，但也在同一个数量级上。毕竟`NornJ`和`JSX`相比，就好比去拿`jQuery`去和`原生dom`对比效率一样，封装过的毕竟没有原生方式速度快。

但是，`NornJ`可以使用一些模板特有的优化方式进行优化。比如使用`<#once>`标签，它和`Vue`的`v-once`指令有些类似，可以把一些静态的标签放到它里面，这样只会渲染第一次，之后就会利用缓存了。如下对`nj`样例进行修改：

```html
<div id="{num}_100">
  <#each {arr}>
    {item.name['fullName']}
    <span class=test_{@index}
          style={../styles}
          onClick={../onClick}>
      test_{../num}
      <#each {../list2}>
        <div key={@index}>
          <span>span{no}</span>
          <i>{no}</i>
          <!-- 添加once扩展标签 -->
          <#once><div><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i><i><i>1000</i></i></div></#once>
        </div>
      </#each>
    </span>
    <#if {@index | five(1)}>
      <br />
      <#else>
        <img />
      </#else>
    </#if>
  </#each>
</div>
```

再次进行测试，这次`NornJ`比`JSX`稍快了一些：

```js
jsx:1129
jsx:1101
jsx:1115
jsx:1254
jsx:1070

avg:1133.8

nj:1258
nj:960
nj:942
nj:946
nj:948

avg:1010.8
```
{% endraw %}