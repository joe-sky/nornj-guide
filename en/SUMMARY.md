# Summary

* [Introduction](README.md)
    * [Why NornJ](gettingStarted/whyNornj.md)
* Getting Started
    * [Use it in JSX](gettingStarted/enhanceReact.md)
    * Use tagged templates
* [Template syntax(JSX)](templateSyntax/index.md)
    * [Tags](templateSyntax/extensionTag.md)
        * If
        * Each
        * Switch
        * For
        * With
        * Fn
        * [Create new tag](templateSyntax/built-inExtensionTag.md)
    * [Directives](templateSyntax/inlineExtensionTag.md)
        * n-show
        * n-style
        * n-class
        * n-debounce
        * n-mobx-bind
        * n-mst-bind
        * [Create new directive](templateSyntax/built-inExtensionTag.md)
    * [Expressions](templateSyntax/filter.md)
        * Filters
            * capitalize
            * bool
            * currency
            * [Create new filter](templateSyntax/built-inFilter.md)
        * Operators
            * a.b\[c\] (Safe accessors)
            * a .. b (Range operator)
            * a ** b (Power)
            * a %% b (Rounding down)
            * [Create new operator](templateSyntax/built-inFilter.md)
* [Template syntax(tagged templates)](templateSyntax/index.md)
    * Difference from JSX
    * [Interpolations](templateSyntax/variable.md)
    * [Accessor](templateSyntax/accessor.md)
    * [Template comment](templateSyntax/comment.md)
* API Reference
    * [nj.compileH](api/renderString.md)
    * [nj.renderH](api/renderReact.md)
    * [nj.config](api/config.md)
* Tools adaptation
    * [With Webpack loader](api/webpack.md)
    * [Html-Webpack-Plugin](api/htmlWebpackPlugin.md)
    * [React-Native](api/reactNative.md)
    * [Redux](api/redux.md)
    * [React-Router](api/reactRouter.md)
    * [Mobx](api/mobx.md)
    * [React UI libraries](api/reactUI.md)
* [Works like classic template engine](gettingStarted/normalTemplateEngine.md)