{% raw %}
# 结合React-Native

可以直接在`NornJ`模板中使用`React-Native`提供的组件：

```js
import {
  AccessibilityInfo,
  ActivityIndicator,
  ART,
  Button,
  DatePickerIOS,
  DrawerLayoutAndroid,
  FlatList,
  Image,
  ...
} from 'react-native';

nj`
  <${FlatList}>
    <${Image} />
  </${FlatList}>
`()
```

## 使用nornj-react包中的react-native组件

在`nornj-react`包中提供一些默认封装的组件，可更方便地使用`React-Native`。

这样引入即可：

```js
import 'nornj-react/native';
```

使用方法如下，和`React-Native`原生组件完全相同：

```js
nj`
  <FlatList>
    <Image />
  </FlatList>
`()
```

## 在react-native中使用单文件组件

在RN项目的根目录下创建`rn-cli.config.js`，然后这样配置

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve('./node_modules/nornj/tools/metroTransformer');
  },
  getSourceExts() {
    return ['htm', 'nj', 'nornj'];
  }
};
```

然后就可以在RN中使用`NornJ`的单文件组件了：

```html
<template name="displayPanel">
  <DisplayPanel>
    <Text style="{[styles.numberBlock, counter < 0 ? styles.lowerThanZero]}">{counter}</Text>
    <Text style={styles.unitBlock}>/ times</Text>
  </DisplayPanel>
</template>

<template>
  <Container>
    <#include name="displayPanel" />
    <ControlPanel style="{styles.inline}">
      <ButtonAddSmall onPress={increment}
                      underlayColor={colors.add.bg}>
        <Text :style="[styles.text, styles.textColorAdd]">+</Text>
      </ButtonAddSmall>
      <ButtonMinusSmall onPress={decrement}
                        underlayColor={colors.minus.bg}>
        <Text :style="[styles.text, styles.textColorMinus]">-</Text>
      </ButtonMinusSmall>
    </ControlPanel>
    <ControlPanel>
      <ButtonAdd onPress={incrementIfOdd}
                 underlayColor={colors.add.bg}>
        <Text :style="[styles.text, styles.textColorAdd]">Increment if odd</Text>
      </ButtonAdd>
      <ButtonAdd onPress={onPressAdd}
                 underlayColor={colors.add.bg}>
        <Text :style="[styles.text, styles.textColorAdd]">Increment async</Text>
      </ButtonAdd>
      <ButtonMinus onPress={onPressMinus}
                   underlayColor={colors.minus.bg}>
        <Text :style="[styles.text, styles.textColorMinus]">Decrement async</Text>
      </ButtonMinus>
      <#if {counter < 0}>
        <ButtonZero onPress={setToZero}
                    underlayColor={colors.minus.bg}>
          <Text :style="[styles.text, styles.textColorMinus]">Set to zero</Text>
        </ButtonZero>
      </#if>
    </ControlPanel>
  </Container>
</template>
```

具体请看下面的实例项目。

## NornJ + React-Native的实例项目

* [计数器示例[react-native + styled-components + nornj]](https://github.com/joe-sky/nornj-react-native-counter)
{% endraw %}