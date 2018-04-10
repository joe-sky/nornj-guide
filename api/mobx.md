{% raw %}
# 结合Mobx

可以直接在`NornJ`模板中使用`Mobx`提供的组件：

```js
import { Provider } from 'mobx-react';

nj`
  <${Provider} store=${store}>
    <${App} />
  </${Provider}>
`()
```

## 使用nornj-react包中的mobx组件与扩展

在`nornj-react`包中提供一些默认封装的组件和扩展，可更方便地使用`Mobx`。

这样引入即可：

```js
import 'nornj-react/mobx';
```

### 组件

| nornj-react中的组件名称   | 对应的Mobx组件 |
|:-------------------------|:---------------|
| mobx-Provider                 | Provider       |

使用方法如下，和`Mobx`原生组件完全相同：

```js
nj`
  <mobx-Provider store=${store}>
    <${App} />
  </mobx-Provider>
`()
```

### 过滤器

| nornj-react中的过滤器名称   | 对应的Mobx方法 |
|:-------------------------|:---------------|
| toJS                 | toJS       |

使用方法如下，和`Mobx`原生方法完全相同：

```js
nj`
  <input value="{toJS(store.value)}" />
  <!-- 或<input value="{store.value | toJS}" /> -->
`()
```

### 行内扩展标签

针对`Mobx`我们提供了实现双向绑定的行内扩展标签`#mobx-model`，[具体请见这里](../templateSyntax/inlineExtensionTag.md#mobx-model)。

## NornJ + Mobx的实例项目

由于`Mobx`是我们主力使用的React状态管理库，所以我们创建了完整的前端脚手架用于快速开发项目：

* [nornj-cli](https://github.com/joe-sky/nornj-cli)

该脚手架的使用方法类似于`vue-cli`，目前可创建完整的基于`react + mobx`的项目模板，并有[快速上手文档](https://github.com/joe-sky/nornj-cli/blob/master/docs/guides/overview.md)。

* [项目脚手架源码[react + mobx + react-router + nornj]](https://github.com/joe-sky/nornj-cli/tree/master/templates/react-mst)
{% endraw %}