{% raw %}
# 结合React-Router

可以直接在`NornJ`模板中使用`React-Router`提供的组件：

```js
import {
  BrowserRouter,
  HashRouter,
  Link,
  MemoryRouter,
  NavLink,
  Prompt,
  Redirect,
  Route,
  Router,
  StaticRouter,
  Switch
} from 'react-router-dom';

nj`
  <${Switch}>
    <${Route} exact path='/' component=${Page1}/>
    <${Route} exact path='/Page1' component=${Page1} />
    <${Route} exact path='/Page2' component=${Page2} />
    <${Route} exact path='/FormExample' component=${FormExample} />
    <${Redirect} from='*' to='/'/>
  </${Switch}>
`()
```

## 使用nornj-react包中的react-router组件

在`nornj-react`包中提供一些默认封装的组件，可更方便地使用`React-Router`。

这样引入即可：

```js
import 'nornj-react/router';
```

组件列表：

| nornj-react中的组件名称   | 对应的React-Router组件 |
|:-------------------------|:----------------------|
| BrowserRouter            | BrowserRouter              |
| HashRouter            | HashRouter              |
| router-Link            | Link              |
| MemoryRouter            | MemoryRouter              |
| router-NavLink            | NavLink              |
| router-Prompt            | Prompt              |
| Redirect            | Redirect              |
| Route            | Route              |
| Router            | Router              |
| StaticRouter            | StaticRouter              |
| router-Switch            | Switch              |

使用方法如下，和`React-Router`原生组件完全相同：

```js
nj`
  <router-Switch>
    <Route exact path='/' component=${Page1}/>
    <Route exact path='/Page1' component=${Page1} />
    <Route exact path='/Page2' component=${Page2} />
    <Route exact path='/FormExample' component=${FormExample} />
    <Redirect from='*' to='/'/>
  </router-Switch>
`()
```

## NornJ + React-Router的实例项目

* [todomvc[react + redux + react-router + nornj + webpack4]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc)
* [todomvc(无需webpack打包)[react + redux + react-router + nornj]](https://github.com/joe-sky/nornj/blob/master/examples/react-redux-nornj-todomvc-es5)
{% endraw %}