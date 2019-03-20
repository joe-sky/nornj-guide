# Summary

* [概述](README.md)
    * [为什么要开发NornJ](gettingStarted/whyNornj.md)
* 快速起步
    * [在JSX中使用](gettingStarted/enhanceReact.md)
    * 使用tagged templates
* [模板语法(JSX)](templateSyntax/index.md)
    * [标签](templateSyntax/extensionTag.md)
        * If
        * Each
        * Switch
        * For
        * With
        * Fn
        * [开发新的标签](templateSyntax/built-inExtensionTag.md)
    * [指令](templateSyntax/inlineExtensionTag.md)
        * n-show
        * n-style
        * n-class
        * n-debounce
        * n-mobx-bind
        * n-mst-bind
        * [开发新的指令](templateSyntax/built-inExtensionTag.md)
    * [表达式](templateSyntax/filter.md)
        * 过滤器
            * capitalize
            * bool
            * currency
            * [开发新的过滤器](templateSyntax/built-inFilter.md)
        * 运算符
            * a.b\[c\] (安全属性访问器)
            * a .. b (范围运算符)
            * a ** b (乘方)
            * a %% b (向下取整)
            * [开发新的运算符](templateSyntax/built-inFilter.md)
* [模板语法(tagged templates)](templateSyntax/index.md)
    * 与JSX语法的不同点
    * [插值变量](templateSyntax/variable.md)
    * [访问器属性](templateSyntax/accessor.md)
    * [模板注释](templateSyntax/comment.md)
* API 参考
    * [nj.compileH](api/renderString.md)
    * [nj.renderH](api/renderReact.md)
    * [nj.config](api/config.md)
* 结合前端生态工具
    * [结合Webpack在单文件中编写模板](api/webpack.md)
    * [Html-Webpack-Plugin](api/htmlWebpackPlugin.md)
    * [React-Native](api/reactNative.md)
    * [Redux](api/redux.md)
    * [React-Router](api/reactRouter.md)
    * [Mobx](api/mobx.md)
    * [React第三方ui库](api/reactUI.md)
* [像传统模板引擎那样工作](gettingStarted/normalTemplateEngine.md)