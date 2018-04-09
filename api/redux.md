{% raw %}
# 结合Redux

`NornJ`可直接支持`Redux`。在`nornj-react`包中提供一些默认封装的组件，可更方便地使用`Redux`。

这样引入即可：

```js
import 'nornj-react/redux';
```

组件列表：

| nornj-react中的组件名称   | 对应的Redux组件 |
|:-------------------------|:---------------|
| Provider                 | Provider       |

使用方法如下，和Redux原生组件完全相同：

```js
nj`
  <Provider store=${store}>
    <${App} />
  </Provider>
`()
```

## NornJ + Redux的实例项目

* [todomvc[react + redux + react-router + nornj + webpack4]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc)
* [todomvc(无需webpack打包)[react + redux + react-router + nornj]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc-es5)
* [计数器示例[react-native + styled-components + nornj]](https://github.com/joe-sky/nornj-react-native-counter)
{% endraw %}