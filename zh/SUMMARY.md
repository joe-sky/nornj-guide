# Summary

* [概述](README.md)
    * [为什么要开发 NornJ](getting-started/why-nornj.md)
    * [快速起步](getting-started/use-it-in-jsx.md)
* [JSX 扩展语法](jsx-syntax/index.md)
    * [标签](jsx-syntax/tags.md#top)
        * [与 React 组件的区别](jsx-syntax/tags.md#different-from-react)
            * [延迟渲染子节点](jsx-syntax/tags.md#lazy-render-children)
            * [生成子节点可用的新变量](jsx-syntax/tags.md#generate-new-variable)
        * [If](jsx-syntax/tags.md#if)
            * [Else](jsx-syntax/tags.md#else)
            * [Elseif](jsx-syntax/tags.md#elseif)
        * [Each](jsx-syntax/tags.md#each)
            * [For](jsx-syntax/tags.md#for)
        * [Switch](jsx-syntax/tags.md#switch)
        * [With](jsx-syntax/tags.md#with)
        * [MobxObserver](jsx-syntax/tags.md#mobxobserver)
        * [MobxFormData](jsx-syntax/mobxFormData.md)
        * [开发新的标签](jsx-syntax/tags.md#create-new-tag)
            * [最简单的标签](jsx-syntax/tags.md#a-simple-tag)
            * [更复杂的标签](jsx-syntax/tags.md#more-complex-tag)
            * [子标签](jsx-syntax/tags.md#subsidiary-tag)
    * [指令](jsx-syntax/directives.md#top)
        * [指令能做什么](jsx-syntax/directives.md#what-can-directives-do)
            * [操作将传入组件的 props 值](jsx-syntax/directives.md#set-component-props)
            * [封装包装组件](jsx-syntax/directives.md#encapsulate-wrapped-component)
        * [n-show](jsx-syntax/directives.md#n-show)
        * [n-style](jsx-syntax/directives.md#n-style)
        * [n-debounce](jsx-syntax/directives.md#n-debounce)
        * [n-mobxBind](jsx-syntax/directives.md#n-mobxbind)
            * [绑定原生表单控件](jsx-syntax/directives.md#n-mobxbind-formitem)
                * [文本框](jsx-syntax/directives.md#n-mobxbind-input)
                * [复选框](jsx-syntax/directives.md#n-mobxbind-checkbox)
                * [单选按钮](jsx-syntax/directives.md#n-mobxbind-radio)
                * [选择框](jsx-syntax/directives.md#n-mobxbind-select)
            * [绑定组件](jsx-syntax/directives.md#n-mobxbind-component)
                * [注册组件](jsx-syntax/directives.md#n-mobxbind-register-component)
                * [已预置注册的组件](jsx-syntax/directives.md#n-mobxbind-component-preset)
        * [开发新的指令](jsx-syntax/directives.md#create-new-directive)
            * [最简单的指令](jsx-syntax/directives.md#a-simple-directive)
            * [更复杂的指令](jsx-syntax/directives.md#more-complex-directive)
            * [数据绑定指令](jsx-syntax/directives.md#data-binding-directive)
* [表达式扩展语法](tagged-templates-syntax/expressions.md#top)
    * [表达式能做什么](tagged-templates-syntax/expressions.md#what-can-expressions-do)
    * [适配常规 JS 表达式](tagged-templates-syntax/expressions.md#js-expression-compatibility)
    * [过滤器](tagged-templates-syntax/expressions.md#filters)
        * [字符串处理](tagged-templates-syntax/expressions.md#string)
            * [foo | upperFirst](tagged-templates-syntax/expressions.md#upperfirst)
            * [foo | lowerFirst](tagged-templates-syntax/expressions.md#lowerfirst)
            * [foo | camelCase](tagged-templates-syntax/expressions.md#camelcase)
        * [类型测定](tagged-templates-syntax/expressions.md#type-determination)
            * [foo | isObject](tagged-templates-syntax/expressions.md#isobject)
            * [foo | isNumber](tagged-templates-syntax/expressions.md#isnumber)
            * [foo | isString](tagged-templates-syntax/expressions.md#isstring)
            * [foo | isArrayLike](tagged-templates-syntax/expressions.md#isarraylike)
        * [foo | currency](tagged-templates-syntax/expressions.md#currency)
        * [foo | toJS](tagged-templates-syntax/expressions.md#tojs)
        * [使用 Lodash](tagged-templates-syntax/expressions.md#lodash)
        * [开发新的过滤器](tagged-templates-syntax/expressions.md#create-new-filter)
    * [运算符](tagged-templates-syntax/expressions.md#operators)
        * [foo.bar[baz]](tagged-templates-syntax/expressions.md#optional-chaining)
        * [foo .. bar](tagged-templates-syntax/expressions.md#range)
            * [foo ..< bar](tagged-templates-syntax/expressions.md#semi-closed-interval-range)
        * [foo <=> bar](tagged-templates-syntax/expressions.md#spaceship)
        * [foo %% bar](tagged-templates-syntax/expressions.md#rounding-down)
        * [开发新的运算符](tagged-templates-syntax/expressions.md#create-new-operator)
* API 参考
    * [nj.registerExtension](api/nj-compile-h.md)
    * [nj.registerFilter](api/nj-render-h.md)
    * [nj.registerComponent](api/nj-render-h.md)
    * [nj.config](api/config1.md)
* 生态 & 工具
    * [语法高亮插件](tools-adaptation/react-native.md)
    * [单元测试](tools-adaptation/react-native.md)
    * [TypeScript 支持](tools-adaptation/react-native.md)
    * [React-Native](tools-adaptation/react-native.md)
    * [Css in Js](tools-adaptation/css-in-js.md)
    * [Mobx](tools-adaptation/mobx.md)
    * [Ant Design](tools-adaptation/react-ui.md)
<!-- * 性能 -->
<!--    * 渲染效率测试 -->
* 更多
    * 替代 JSX (文档待完成)
    * 渲染 Html 字符串(文档待完成)
    * [Css 渲染工具](tools-adaptation/css-render-tool.md)