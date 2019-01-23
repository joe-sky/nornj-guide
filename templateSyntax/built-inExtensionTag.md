{% raw %}
# 内置标签

## 目录

* [if](#if)
* [else](#else)
* [elseif](#elseif)
* [switch](#switch)
* [unless](#unless)
* [each](#each)
* [for](#for)
* [with](#with)
* [props与prop](#props与prop)
* [strProp](#strProp)
* [obj](#obj)
* [list](#list)
* [fn](#fn)
* [tmpl](#tmpl)

### if

例：

```html
<div>
  this is the if block demo.
  <#if {{type}}>
    test if block
    <span>test1</span>
  </#if>
</div>
```
在执行模板函数时，如if标签的参数计算结果为true，则会执行if标签内的模板；如为false则不会执行if标签内的模板。

### else

例：

```html
<div>
  this is the if block demo
  <#if {{type}}>
    test if block
    <span>test1</span>
    <#else>  <!--else标签(1)-->
      <span>test2</span>  <!--type参数计算结果为false时执行此处的模板(2)-->
    </#else>
  </#if>
</div>
```

1. else标签须定义在if标签内，格式如例中(1)处所示。
2. 在执行模板函数时，如if标签的参数计算结果为false，则会输出if标签内的else标签子节点，如例中(2)处所示。

### elseif

elseif标签可以实现模板内的多分支流程：

```js
const html = nj`
<#if {{num > 100}}>
  100
  <#elseif {{num > 50}}>
    50
  </#elseif>
  <#elseif {{num > 20}}>
    20
  </#elseif>
  <#else>
    0
  </#else>
</#if>
`({ num: 30 });

console.log(html);  //输出20
```

### switch

switch标签也可以实现多分支流程：

```js
const html = nj`
<#switch {{num}}>
  <#case {{50}}>
    50
  </#case>
  <#case {{30}}>
    30
  </#case>
  <#default>
    0
  </#default>
</#switch>
`({ num: 30 });

console.log(html);  //输出30
```

### unless

例：

```html
<#unless {{type}}>
  test unless block
  <span>test1</span>
  <#else>
    <span>test2</span>
  </#else>
</#unless>
```

unless标签意义即为"除非"，它和if标签取相反的值。例中如type参数为`false`，则会输出`<span>test1</span>`。

### each

例：

```js
const tmplFn = nj`
<div>
  this is the each block demo{{no}}.

  <#each {{items}}>  <!--each标签开始标签(1)-->
    test each tag{{no}}  <!--items数组每项的no属性(2)-->
    <span>test{{../no}}</span>  <!--与items数组同一层的no属性(3)-->
    <#empty>
      <span>no data {{no}}</span>  <!--无数据时展示的模板(4)-->
    </#empty>
  </#each>

  <#each {{numbers}}>
    num:{{@item}},  <!--@item表示使用数组项渲染(5)-->
    no:{{@index}}{{' '}}  <!--@index表示使用数组项索引值渲染(6)-->
  </#each>

  <#each {{numbers}} moreValues>  <!--添加moreValues参数后可使用更多内置参数(7)-->
    <#if {{@first}}>show first, length:{{@length}}<br></#if>
    <#if {{@last}}>show last</#if>
  </#each>
</div>`;

let html = tmplFn({
  no: 100,
  items: [
    { no: 200 },
    { no: 300 }
  ],
  numbers: [10, 20, 30]
});

console.log(html);
/*输出html:
<div>
  this is the each block demo100.
  test each tag200
  <span>test100</span>
  test each tag300
  <span>test100</span>
  num:10,no:0 num:20,no:1 num:30,no:2
  show first, length:3<br>show last
</div>
*/

let html2 = tmplFn({
  no: 100,
  items: null,
  numbers: null
});

console.log(html2);
/*输出html:
<div>
  this is the if block demo100.
  <span>test else100</span>
</div>
*/
```

* each标签接受一个js数组格式的参数，如例中(1)处的`{{items}}`参数。
* each标签会遍历参数数组中的数据，将数组中的每一项数据都执行渲染。在遍历每个数组项时，会使用每项的数据作为当前节点的数据，相当于生成了一个上下文。如例中(2)处所示，`{{no}}`参数为items数组内各项的no值。
* 在遍历每个数组项时也可以使用父级上下文的数据，如例中(3)处所示，`{{../no}}`表示获取与items参数同一级的no值。和一般的目录描述方法类似，"../"可以写多次，每次代表向上退一级上下文。
* 在each标签中可以使用empty标签，如`{{items}}`参数为null、false或空数组时，则会执行empty标签内的模板。如例中(4)处所示，empty标签内的模板并不会产生上下文，`{{no}}`参数为items参数同一级的no值。
* each标签内可以使用`{{@item}}`(`v0.4.3新增`)插值变量，表示使用当前正在循环的项值，如例中(5)处所示。另外，使用`{{this}}`也能获取当前正在循环的项值。
* each标签内还可以使用`{{@index}}`插值变量，表示使用当前数组项索引值，如例中(6)处所示。
* each标签可以添加`moreValues`参数，如(7)处所示，这样就可以使用更多内置参数：
  * @first：是否为第一项
  * @last：是否为最后一项
  * @length：正在遍历的数据或对象的项数

### for

for标签与each标签比较类似，它传入开始和结束两个整数值，然后在这个区间中循环。`@index`为每次循环的索引值，从0开始。示例如下：

```js
const html = nj`
<#for {{0}} {{list.length}}>
  <div>{{info + list[@index]}}</div>
</#for>
`({ list: [1, 2, 3, 4, 5], info: 'test' });

console.log(html);
/*输出：
<div>test1</div>
<div>test2</div>
<div>test3</div>
<div>test4</div>
<div>test5</div>
*/
```

如上例，for标签默认是不会循环到结束值的，即为`start < end`。在添加`loopLast`参数后，就会改为`start <= end`：

```js
const html = nj`
<#for {{start}} {{end}} loopLast>
  <div>{{info}}</div>
</#for>
`({ start: 1, end: 3, info: 'test' });

console.log(html);
/*输出：
<div>test</div>
<div>test</div>
<div>test</div>  不加loopLast参数时此行不会渲染
*/
```

### with

`with`标签和js的with语句作用类似，可用于缩短链式取值的长度：

```js
const html = nj`
<#with {{props[0].prop}}>
  <span>{{a}}</span>
  <span>{{b}}</span>
</#with>
`({
  props: [
    {
      prop: {
        a: 1,
        b: 2
      }
    }
  ]
});

console.log(html);  //输出：<span>1</span><span>2</span>
```

还可以使用`with`标签在其子节点中创建新的变量：

```js
const html = nj`
<#with propA={{props[0]}} propB={{props[1]}}>
  <span>{{propA.value}}</span>
  <span>{{propB.value}}</span>
</#with>
`({
  props: [
    {
      value: 1
    }, {
      value: 2
    }
  ]
});

console.log(html);  //输出：<span>1</span><span>2</span>
```

### props与prop

props标签可以提供另外一种定义元素节点参数的方式，模板标签的每个参数用props标签内的prop标签包裹。用props标签定义的节点参数可以内嵌或包裹其他标签，能够提供更丰富的动态生成逻辑。举例如下：

```js
const tmpl = nj`
<div name=foo>
  <#props>  <!--定义props标签-->
    <#prop {{'name'}}>bar</#prop>  <!--节点参数键为字符串，值也为字符串(1)-->
    <#prop {{'id'}}>{{test}}</#prop>  <!--节点参数键为字符串，值为替换参数(2)-->
    <#prop {{'id2'}}>  <!--节点参数值为表达式标签的执行结果(3)-->
      <#each {{list}}>
        {no}
      </#each>
    </#prop>
    <#each {{list}}>  <!--根据表达式标签的执行结果动态构建节点参数(4)-->
      <#prop {{no}}>{{'test'}}{{no}}</#prop>
    </#each>
    <@name2>bar2</@name2>  <!--这种写法是#prop的语法糖形式,会自动转换为<#prop {{'name2'}}>bar2</#prop>(5)-->
    <@disabled />  <!--自动转换为<#prop {{'disabled'}} />(6)-->
  </#props>
  this is a props block demo.
</div>`;

const tmplFn = nj.compile(tmpl, 'tmpl1');
let html = tmplFn({
  list: [
    { no: 1 },
    { no: 2 },
    { no: 3 }
  ]
});

console.log(html);
/*输出html:
<div name="foo" name2="bar2" disabled id="test" id2="123" 1="test1" 2="test2" 3="test3">
this is a props block demo.
</div>
*/
```

* 每个元素节点可以有任意多个props标签子节点。props标签内部可以定义任意个prop标签，每个prop标签为该元素节点定义一个节点参数。
* prop标签开始标签内的参数为节点参数名称。使用字符串或替换参数都可以，也可以加过滤器。如例中(1)、(2)处所示。
* prop标签的子节点为节点参数值。如果有多个子节点，则节点参数值为这些子节点值连成的一个完整字符串。如例中(3)处所示。
* prop标签也可以用each标签等其他标签包裹，这样就可以动态生成多个节点参数。如例中(4)处所示。
* 使用prop标签定义节点参数的优先级会低于直接定义在节点的开始标签内，如例中(1)处的name属性就被节点标签中定义的name属性覆盖了。
* 可以使用`<@propName>propValue</@propName>`的语法糖形式代替prop标签，这样写会更简单一些，如例中(5)、(6)处所示。
* 如果prop标签没有被包裹其他标签时，props也可以省略不写：

```html
<div>
  <@name>foo</@name>
  this is a props block demo.
</div>
```

* prop标签内如果有多个子节点时，默认会按js数组格式输出；如果要按字符串格式输出prop的多个子节点，须要加`useString`参数，如下例：

```js
const html = nj`
<div>
  <@id useString>
    {{1}}
    {{2}}
    {{3}}
  </@id>
  <@name>  <!--# 此处name属性的数据会生成"[1, 2, 3]"，因此最终输出的字符串为"1,2,3" #-->
    {{1}}
    {{2}}
    {{3}}
  </@name>
</div>`();

console.log(html);  //输出：<div id="123" name="1,2,3"></div>
```

### strProp

`strProp`即为`prop`自动添加了`useString`参数的版本，还可以使用`<@@propName>propValue</@@propName>`的语法糖形式代替`strProp`标签：

```js
const html = nj`
<div>
  <#strProp {{'id'}}>
    {{1}}
    {{2}}
    {{3}}
  </#strProp>
  <@@name>
    {{1}}
    {{2}}
    {{3}}
  </@@name>
</div>`();

console.log(html);  //输出：<div id="123" name="123"></div>
```

### obj

使用`obj`标签可以在模板中生成js对象，例如：

```html
<!--test.nj.html-->
<#testExpr>
  <#obj id="testId" name={{testName}} />
</#testExpr>
```

```js
import tmpl from 'test.nj.html';

const html = tmpl({
  testName: 'testName',
  testExpr: options => {
    const obj = options.result();
    return obj.id + '_' + obj.name;
  }
});

console.log(html);  //输出：testId_testName
```

### list

使用`list`标签可以在模板中生成js数组：

```js
const html = nj`
<div>
  <@@id>
    <#list {{1}} {{2}} {{3}} {{4}} {{5}} />
  </@@id>
</div>`();

console.log(html);  //输出：<div id="12345"></div>
```

### fn

使用`fn`标签可以在模板中表示js函数：

```js
import React from 'react';
import Media from 'react-media';
import nj from 'nornj';

nj.registerComponent('Media', Media);

class App extends React.Component {
  render() {
    return nj`
      <div>
        <Media query="(max-width: 599px)">
          <#fn matches>
            <#if {matches}>
              <p>The document is less than 600px wide.</p>
              <#else>
                <p>The document is at least 600px wide.</p>
              </#else>
            </#if>
          </#fn>
        </Media>
      </div>
    `();
  }
}
```

在上例中，`fn`标签的`matches`属性即为函数的参数，可以定义多个；`fn`标签的的子节点即为函数的返回值。如上例改为`JSX`方式即为这样：

```js
...

class App extends React.Component {
  render() {
    return (
      <div>
        <Media query="(max-width: 599px)">
          {matches => matches ? (
            <p>The document is less than 600px wide.</p>
          ) : (
            <p>The document is at least 600px wide.</p>
          )}
        </Media>
      </div>
    )
  }
}
```

### tmpl

`tmpl`标签即为`React`组件或标签的子模板函数，可指定在适当的时机去渲染它们，可以实现类似于`Vue`中的`slot`分发机制。在`React`开发中使用方式如下：

```js
class TestComp extends React.Component {
  render() {
    return nj`
      <div>
        {#tmpl}
        {#tmpl2}
      </div>
    `({
      tmpl: this.props.tmpls[0],
      tmpl2: this.props.tmpls.tmpl2,
      text: 'test'
    });
  }
}

let html = renderToStaticMarkup(nj`
<TestComp>
  <#tmpl>
    <i class=icon-refresh>{text}</i>
  </#tmpl>
  <#tmpl name="tmpl2">
    <i class=icon-home>{text}</i>
  </#tmpl>
</TestComp>
`());

console.log(html);
/*输出：
<div>
  <i class="icon-refresh">test</i>
  <i class="icon-home">test</i>
</div>
*/
```

* 使用`NornJ`模板渲染每个React组件时都可以设置任意多个tmpl标签作为子节点；在组件内部可以用`this.props.tmpls[x]`的方式获取到它们，如下例中所示。
* 可以将`this.props.tmpls[x]`作为计算属性参数(即{#param}的形式)传入到`NornJ`模板中使用，这样就可以渲染tmpl标签内的模板了。
* 还可以直接执行`this.props.tmpls[x]`方法来渲染tmpl标签内的模板，如下例：

```js
class TestComp extends React.Component {
  render() {
    return nj`
      <div>
        {tmpl}
        {tmpl2}
      </div>
    `({
     tmpl: this.props.tmpls[0]({ text: 'test' }),  //用数组下标方式获取
     tmpl2: this.props.tmpls.tmpl2({ text: 'test' })  //用tmpl标签的name属性获取
    });
  }
}
```
{% endraw %}