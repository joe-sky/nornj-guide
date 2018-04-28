{% raw %}
# 标签模板字符串(es6)语法

`NornJ`模板除了能使用纯字符串来构建外，还提供一种类似`JSX`的使用方式，即使用es6支持的标签模板字符串来构建模板，如下例：

```js
import nj from 'nornj';

nj`<div>
     {{test}}
   </div>`;
```

# 构建方式

引入`nj`变量后，将它作为模板字符串的标签即可。该标签模板字符串会返回一个模板函数，参数列表和nj.compile方法编译出的模板函数完全相同，可直接运行。如下例：

```js
const tmplFn = nj`
<div>
  {{test}}
</div>`;

console.log(tmplFn({ test: 100 }));  //输出：<div>100</div>
```

## 嵌入js变量或表达式

`nj`标签模板字符串可直接嵌入任意js变量与表达式，语法为使用`${}`，如下例：

```js
console.log(nj`
<div>
  ${(1 + 2) * 3}
  ${' test '.trim()}
</div>`());  //输出：<div>9test</div>
```

在`React`开发中，`JSX`变量也可以直接嵌入到其中：

```js
nj`
<div>
  <#if ${true}>
    ${<img />}
  </#if>
</div>`;
```

`React`组件对象也支持嵌入标签中编写：

```js
import Icon from 'react-native-vector-icons/FontAwesome';
import Button from 'antd-mobile/lib/button';

nj`
<${Button} type="primary" size="small" inline>
  <${Icon} name="rocket" size={30} color="#900" />small
</${Button}>`;
```

事实上，这种嵌入变量的方式，本质是一种模板的语法糖，如下例所示：

```js
const html = nj`
<div>
  ${(1 + 2) * 3}
</div>`();

//以上完全等价于：

const html2 = nj`
<div>
  {{value}}
</div>`({ value: (1 + 2) * 3 });
```

* 可在单个插值变量中插入多个js变量或表达式，如下例所示：

```js
console.log(nj`
<div>
  {{ ${1 + 2} * 3 + ${4.5 + 5.5} | int }}  <!--#可以同时使用过滤器等其他模板语法#-->
</div>`());  //输出：<div>19</div>
```

# nj.template

`v0.4.3`新增了一种更简便的使用模板方式`nj.template`，使用方法如下：

```js
import nj, { template as t } from 'nornj';

const html1 = nj`
<div>
  {{'test_' + test2}}
</div>`({ test2: 'test2' });

const html2 = t`
<div>
  {{'test_' + ${'test2'}}}
</div>`;

//以上两种均输出：<div>test_test2</div>
```

如上，`nj.template`的优势在于不用在后面写执行模板函数的括号了，这样结构会更简便；但是这样就无法给模板传参数列表了，需要传参数列表时还是要用`nj`。
{% endraw %}