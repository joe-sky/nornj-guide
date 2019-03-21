# Summary

* [Introduction](README.md)
    * [Why NornJ](gettingStarted/whyNornj.md)
* Getting Started
    * [Use it in JSX](gettingStarted/enhanceReact.md)
    * Use tagged templates
* [NornJ syntax(JSX)](templateSyntax/index.md)
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
            * currency
            * [Create new filter](templateSyntax/built-inFilter.md)
        * Operators
            * a.b\[c\] (Safe accessors)
            * a .. b (Range operator)
            * a ** b (Power)
            * a %% b (Rounding down)
            * [Create new operator](templateSyntax/built-inFilter.md)
* [NornJ syntax(tagged templates)](templateSyntax/index.md)
    * [Interpolations](templateSyntax/variable.md)
    * [Accessor](templateSyntax/accessor.md)
    * [Filter and Operator](tagged-templates-syntax/filters-and-operators.md)
    * [Tag and Directive](tagged-templates-syntax/tags-and-directives.md)
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
* [As a classic template engine](gettingStarted/normalTemplateEngine.md)