{% raw %}
# 结合Html-Webpack-Plugin

[Html-Webpack-Plugin](https://github.com/jantimon/html-webpack-plugin)是`Webpack`常用的用于生成静态html页面的插件，它内部使用的默认模板引擎为`Lodash.template`。但它也提供了使用第三方模板引擎的方式，使用相应的`webpack-loader`即可，[具体文档在这里](https://github.com/jantimon/html-webpack-plugin#writing-your-own-templates)。

`NornJ`也提供了[nornj-loader](https://github.com/joe-sky/nornj-loader)用于结合`Webpack`，故可直接支持`Html-Webpack-Plugin`。

## 配置nornj-loader

Webpack配置项：

```js
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.nj.html(\?[\s\S]+)*$/,
        use: [{
          loader: 'nornj-loader'
        }]
      },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      filename: '/index.html',
      template: './index.nj.html',
      inject: 'true',
      chunks: ['vendor', 'app'],
      path: `/dist/resources/`
    })
  ]
}
```

## 在html文件中使用NornJ语法

下面的html实例中使用了`NornJ`的`with标签`，作用是为了减少获取path参数的属性层级数：

```html
<#with {{ htmlWebpackPlugin.options }}>
<!DOCTYPE html>
<html>
<head>
  <title>react-mst-boilerplate</title>
  <meta http-equiv="content-type" content="text/html; charset=utf-8">
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black" />
  <meta name="format-detection" content="telephone=no" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <!--<meta name="viewport" content="width=device-width, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no,initial-scale=1.0" />-->
  <script src="{{ path }}js/es6-promise.auto.min.js"></script>
</head>
<body>
  <div id="app" style="position:relative"></div>
  <script src="{{ path }}js/jquery-3.1.1.min.js"></script>
  <script src="{{ path }}js/babelHelpers.min.js"></script>
</body>
</html>
</#with>
```

html模板文件经过`nornj-loader`解析后，会填充各变量并生成生产代码存放在`dist/resources/index.html`：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>
      react-mst-boilerplate
    </title>
    <meta http-equiv="content-type" content="text/html; charset=utf-8" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <meta name="format-detection" content="telephone=no" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <!--
    <meta name="viewport" content="width=device-width, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no,initial-scale=1.0" />
    -->
    <script src="web/js/es6-promise.auto.min.js"></script>
    <link href="web/css/20171218/app.css" rel="stylesheet" />
  </head>
  <body>
    <div id="app" style="position:relative"></div>
    <script src="web/js/jquery-3.1.1.min.js"></script>
    <script src="web/js/babelHelpers.min.js"></script>
    <script type="text/javascript" src="web/20171218/vendors.min.js"></script>
    <script type="text/javascript" src="web/20171218/app.js"></script>
  </body>
</html>
```
{% endraw %}