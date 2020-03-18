{% raw %}

# MobxFormData {#top}

基于`Mobx`的响应式`React`表单数据管理与验证解决方案

[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][npm-url]

## 特征 {#feature}

- :sparkles: 简洁的 JSX API
- :star: 基于 [async-validator](https://github.com/yiminghe/async-validator) 的轻量级封装
- :dizzy: 完整的可观察类型数据驱动表单
- :star2: 支持全部原生表单组件与 [Ant Design](https://ant.design/) 组件
- :zap: 局部渲染 `Form.Item` 组件
- :fire: 可脱离 `Form` 组件独立运作

## 对比其他表单验证方案 {#contrast}

|                        |      Ant-Design v3       |      Ant-Design v4       |  Formily（UForm）  |               MobxFormData                |
| :--------------------- | :----------------------: | :----------------------: | :----------------: | :---------------------------------------: |
| 底层验证框架           |    `async-validator`     |    `async-validator`     | @formily/validator |             `async-validator`             |
| API 上手难度           |           一般           |           容易           |        较难        |                   容易                    |
| 组件外管理表单数据     | :heavy_multiplication_x: |    :o:（官方不推荐）     |        :o:         |                    :o:                    |
| 跨组件表单数据联动     |           较难           |           容易           |        较难        |                   容易                    |
| 更新只渲染表单控件自身 | :heavy_multiplication_x: |           :o:            |        :o:         |                    :o:                    |
| 自定义表单控件逻辑     |           一般           |           一般           |        一般        |     **容易（不必创建新的表单组件）**      |
| Mobx 数据适配性        |           一般           |           一般           |        一般        | **好（直接读写可观察值、无需手工 toJS）** |
| 多重验证规则           |           :o:            |           :o:            |        :o:         |   :heavy_multiplication_x:（暂未实现）    |
| 自动滚动至验证出错处   |           :o:            |           :o:            |        :o:         |         :heavy_multiplication_x:          |
| 表单生命周期           | :heavy_multiplication_x: | :heavy_multiplication_x: |        :o:         |         :heavy_multiplication_x:          |
| json 配置化生成        | :heavy_multiplication_x: | :heavy_multiplication_x: |        :o:         |         :heavy_multiplication_x:          |
| 适配多个组件库         | :heavy_multiplication_x: | :heavy_multiplication_x: |        :o:         |                    :o:                    |

## 快速开始 {#quick-start}

### 安装与配置 babel

```sh
npm install babel-plugin-nornj-in-jsx babel-plugin-import babel-plugin-module-resolver
```

配置`.babelrc`:

```js
{
  "plugins": [
    [
      'module-resolver',
      {
        alias: {
          '^antd$': 'nornj-react/antd',
        },
        extensions: ['.js', '.jsx', '.ts', '.tsx', '.es', '.es6', '.mjs'],
      },
    ],
    [
      "import",
      {
        libraryName: "nornj-react/antd",
        style: false,
      }
    ],
    "nornj-in-jsx"
  ]
}
```

### 引入 JSX 类型定义

`MobxFormData` 标签的 JSX 类型定义需在全局引入一次，例如在 APP.jsx：

```js
import "nornj-react";
```

### 使用 JSX 标签定义表单域

创建 `MobxFormData` 和 `MobxFieldData` 标签描述表单域各参数：

```js
<MobxFormData observable>
  <MobxFieldData
    name="userName"
    value="joe_sky"
    type="string"
    trigger="onChange"
    required
  />
  <MobxFieldData name="age" value="33" type="number" />
</MobxFormData>
```

### 绑定到 Form.Item 组件

使用 `n-mobxField` 指令绑定到 `Form.Item` 组件，即完成了一个最简单的响应式表单：

```js
const TestForm = observer(props => {
  const { formData } = useLocalStore(() => (
    <MobxFormData>
      <MobxFieldData
        name="userName"
        value="joe_sky"
        type="string"
        trigger="onChange"
        required
      />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ));

  const onSubmit = async e => {
    const values = await formData.validate();
    console.log(values);
  };

  return (
    <>
      <Form.Item n-mobxField={formData.userName} label="User Name">
        <Input />
      </Form.Item>
      User Name: {formData.userName}
      <Form.Item n-mobxField={formData.age} label="Age">
        <Input />
      </Form.Item>
      Age: {formData.age}
      <Button onClick={e => formData.age++}>增加Age</Button>
      <Button onClick={e => formData.reset()}>重置</Button>
      <Button onClick={onSubmit}>提交</Button>
    </>
  );
});
```

### 表单响应式更新

当我们手工修改 `formData` 实例上的表单值；或者在文本框内更改值的时候，表单都会自动触发相应的值更新及校验，非常棒的操作体验 :relaxed: ！

## 它是如何工作的 {#how-it-wooks}

`MobxFormData` 是一个基于 `Babel Plugin` 的表单数据管理方案，即会将一些语法糖转换为实际运行的代码：

- MobxFormData & MobxFieldData

转换前：

```js
const { formData } = (
  <MobxFormData observable>
    <MobxFieldData
      name="userName"
      value="joe_sky"
      type="string"
      trigger="onChange"
      required
    />
    <MobxFieldData name="age" value="33" type="number" />
  </MobxFormData>
);
```

转换后：

```js
const { formData } = createFormData({ observable: true }, [
  {
    name: "userName",
    value: "joe_sky",
    type: "string",
    trigger: "onChange",
    required: true
  },
  {
    name: "age",
    value: "33",
    type: "number"
  }
]);
```

- n-mobxField

转换前：

```js
<>
  <Form.Item n-mobxField={formData.userName} label="User Name">
    <Input />
  </Form.Item>
  <Form.Item n-mobxField={formData.age} label="Age">
    <Input />
  </Form.Item>
</>
```

转换后：

```js
<>
  <MobxObserver>
    <Form.Item n-mobxField={formData.userName} label="User Name">
      <Input n-mobxBind={formData.userName} />
    </Form.Item>
  </MobxObserver>
  <MobxObserver>
    <Form.Item n-mobxField={formData.age} label="Age">
      <Input n-mobxBind={formData.age} />
    </Form.Item>
  </MobxObserver>
</>
```

## 使用方法 {#usage}

### MobxFormData & MobxFieldData {#mobxformdata-and-mobxfielddata}

使用 `MobxFormData` 和 `MobxFieldData` 标签（参数请参考 [MobxFormDataProps](#mobxformdataprops) 和 [MobxFieldDataProps](#mobxfielddataprops)）来描述表单数据，它的返回值是 `formData` 实例（[MobxFormDataInstance](#mobxformdatainstance)）：

```js
const { formData } = (
  <MobxFormData observable>
    <MobxFieldData name="userName" value="joe_sky" type="string" />
    <MobxFieldData name="age" value="33" type="number" />
  </MobxFormData>
);
```

我们可以使用该实例对表单执行各种操作：

```js
//重新设置值
formData.name = "joe";
formData.setValue({ userName: "joe", age: 34 });

//校验
formData.validate("userName");
formData.validate(["userName", "age"]);
formData.validate();
```

### n-mobxField {#mobxfield}

使用 `n-mobxField` 指令将 `formData` 实例绑定到 `Form.Item` 组件，将指令的值设置为 `formData#fieldName` 即可：

```js
<>
  <Form.Item n-mobxField={formData.userName} label="User Name">
    <Input />
  </Form.Item>
  <Form.Item n-mobxField={formData.age} label="Age">
    <Input />
  </Form.Item>
</>
```

> 此种方式可解决绝大多数表单数据校验场景，它实际上自动为 `Form.Item` 下层的表单元素组件设置了 [n-mobxBind](./directives.md#n-mobxbind) 指令。

如果 `n-mobxField` 有不满足需求的场景，我们还可以自行编写表单元素组件的各种参数与逻辑，增加 `noBind` 参数即可：

```js
const TestForm = observer(props => {
  const { formData } = useLocalStore(() => (
    <MobxFormData>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ));

  return (
    <>
      <Form.Item n-mobxField-noBind={formData.userName} label="User Name">
        <Input n-mobxBind={formData.userName} />
        <Input value={"Age：" + formData.age} />
      </Form.Item>
      <Form.Item n-mobxField-noBind={formData.age} label="Age">
        <Input
          value={(formData.age + "").trim()}
          onChange={e => (formData.age = e.target.value)}
        />
      </Form.Item>
    </>
  );
});
```

另外 `n-mobxField` 也支持使用 `n-mobxBind` 的参数和修饰符：

```js
<>
  <Form.Item n-mobxField-debounce$200={formData.userName} label="User Name">
    <Input onChange={e => console.log(formData.userName)} />
  </Form.Item>
</>
```

- 绑定动态数据

`n-mobxField` 也可以支持绑定动态数据，语法稍有不同：

```js
<Each of={users}>
  <Form.Item n-mobxField={`formData[item.userName]`} label={item.userName}>
    <Input />
  </Form.Item>
</Each>
```

### React hooks {#hooks}

使用 `mobx-react` 包提供的 [useLocalStore](https://mobx-react.js.org/state-local) 即可，在它的参数中传入 `MobxFormData` 标签：

```js
const TestForm = observer(props => {
  const { formData: formDataUser } = useLocalStore(() => (
    <MobxFormData>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ));

  return (
    <>
      <Form.Item n-mobxField={formDataUser.userName} label="User Name">
        <Input />
      </Form.Item>
      <Form.Item n-mobxField={formDataUser.age} label="Age">
        <Input />
      </Form.Item>
    </>
  );
};
```

- 更新表单值

例如在 hooks 场景从后台接口初次更新表单值，通常可以在 `useEffect` 中操作：

```js
const TestForm = observer(props => {
  const { formData: formDataUser } = useLocalStore(() => (
    <MobxFormData>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ));

  useEffect(() => {
    api.getUserInfo().then(values => {
      formDataUser.setValue(values);
    });
  }, []);

  ...
};
```

### React class 组件 {#class}

在 class 组件中，我们可以直接使用 `MobxFormData` 标签创建 `formData` 实例：

```js
@observer
class TestForm extends React.Component {
  formDataUser = (
    <MobxFormData observable>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ).formData;

  render() {
    return (
      <>
        <Form.Item n-mobxField={this.formDataUser.userName} label="User Name">
          <Input />
        </Form.Item>
        <Form.Item n-mobxField={this.formDataUser.age} label="Age">
          <Input />
        </Form.Item>
      </>
    );
  }
}
```

- 更新表单值

例如在 class 组件场景从后台接口初次更新表单值，通常可以在 `componentDidMount` 中操作：

```js
@observer
class TestForm extends React.Component {
  formDataUser = (
    <MobxFormData observable>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ).formData;

  componentDidMount() {
    api.getUserInfo().then(values => {
      formDataUser.setValue(values);
    });
  }

  ...
}
```

### 在 Mobx Store 中定义 {#mobx-store}

`MobxFormData` 在组件外管理表单数据是非常容易的，因为它可以支持在 `Store` 内定义：

```js
class TestStore {
  @observable userList = [];
  @observable formDataUser = (
    <MobxFormData>
      <MobxFieldData name="userName" value="joe_sky" type="string" />
      <MobxFieldData name="age" value="33" type="number" />
    </MobxFormData>
  ).formData;

  async getData(params) {
    const res = await api.getModData(params);
    this.formDataUser.userName = res.data.userName;
    this.formDataUser.age = res.data.age;
  }
}
```

## API

### 设置值 {#set-value}

### 验证 {#validate}

### 重置 {#reset}

### 清除状态 {#clear}

### 自定义验证 {#custom-validate}

### 异步自定义验证 {#async-custom-validate}

### 触发验证方式 {#trigger-validate}

### 动态创建/删除 {#add-delete}

### 查找/遍历 {#select-loop}

## Types

### RuleType

`RuleType` 包含所有的验证类型，由 [async-validator](https://github.com/yiminghe/async-validator/blob/master/src/index.d.ts#L23) 提供。

```js
type RuleType =
  | "string"
  | "number"
  | "boolean"
  | "method"
  | "regexp"
  | "integer"
  | "float"
  | "array"
  | "object"
  | "enum"
  | "date"
  | "url"
  | "hex"
  | "email"
  | "any";
```

### RuleItem

`RuleItem` 包含 `MobxFieldData` 标签上所有由 [async-validator](https://github.com/yiminghe/async-validator/blob/master/src/index.d.ts#L40) 提供的参数。

```js
interface RuleItem {
  type?: RuleType; // default type is 'string'
  required?: boolean;
  pattern?: RegExp | string;
  min?: number; // Range of type 'string' and 'array'
  max?: number; // Range of type 'string' and 'array'
  len?: number; // Length of type 'string' and 'array'
  enum?: Array<string | number | boolean | null | undefined>; // possible values of type 'enum'
  whitespace?: boolean;
  fields?: Rules; // ignore when without required
  options?: ValidateOption;
  defaultField?: { type: RuleType }; // 'object' or 'array' containing validation rules
  transform?: (value: any) => any;
  message?: string;
  asyncValidator?: (
    rule: Rules,
    value: any,
    callback: (error: string | string[] | void) => void,
    source: ValidateSource,
    options: ValidateOption
  ) => void | Promise<void>;
  validator?: (
    rule: Rules,
    value: any,
    callback: (error: string | string[] | void) => void,
    source: ValidateSource,
    options: ValidateOption
  ) => void;
}
```

### MobxFieldDataProps

```js
interface MobxFieldDataProps extends RuleItem {
  name: string;
  value?: any;
  trigger?: string;
  [key: string]: any;
}
```

### MobxFieldData

```js
interface MobxFieldData {
  (props: MobxFieldDataProps): JSX.Element;
}
```

### MobxFieldDataInstance

```js
interface MobxFieldDataInstance extends MobxFieldDataProps, IObservableObject {
  validatorSchema?: schema;
  reset?: Function;
  validateStatus?: string;
  help?: string;
}
```

### MobxFormDataProps

```js
interface MobxFormDataProps {
  observable?: boolean;
}
```

### MobxFormData

```js
interface MobxFormData {
  (props: MobxFormDataProps): JSX.Element;
}
```

### MobxFormDataInstance

```js
interface MobxFormDataInstance extends IObservableObject {
  fieldDatas: Map<string, MobxFieldDataInstance>;
  validate(name: string | string[]): Promise<any>;
  error(name: string, help: string): void;
  clear(name: string | string[]): void;
  reset(name: string | string[]): void;
  add(fieldData: MobxFieldDataProps | JSX.Element): void;
  delete(name: string): void;
  setValue(name: string | object, value?: any): void;
  [key: string]: any;
}
```

[npm-image]: http://img.shields.io/npm/v/nornj-react.svg
[downloads-image]: http://img.shields.io/npm/dm/nornj-react.svg
[npm-url]: https://www.npmjs.org/package/nornj-react

{% endraw %}
