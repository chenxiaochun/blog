## 关于 import 动态异步引入资源

使用 import 动态异步引入资源，如果想修改其打包之后的 chunName，中间的注释名称必须以/*开头才会起作用：
```js
import(/* webpackChunkName: "react-qr-code" */ 'react-qr-code')
```

## 关于 html strong b 标签的使用区别

根据 HTML 5 的规范，`<b>`标签应该做为最后的选择，只有在没有其他标记比较合适时才使用它。HTML 5 规范声明：标题应该用 `<h1> - <h6>` 标签表示，被强调的文本应该用 `<em>` 标签表示，重要的文本应该用 `<strong>` 标签表示，被标记的或者高亮显示的文本应该用 `<mark>` 标签表示

`b` 和 `i` 标签只对应样式，`strong` 和 `em` 还对阅读器起作用，针对无障碍阅读

## 快速动态生成使用下标填充的数组

```js
[...new Array(24).keys()]
```

输出：
```js
 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]
```

此技巧的一个使用场景，假设我们想生成一个从 00:00 到 23:00 的时间列表：

```js
[...new Array(24).keys()].map(v => Number(v) < 10 ? `0${v}:00` : `${v}:00`)
```
最终输出：
```js
['00:00', '01:00', '02:00', '03:00', '04:00', '05:00', '06:00', '07:00', '08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00', '18:00', '19:00', '20:00', '21:00', '22:00', '23:00']
```

## 执行 git push 时，自动推送到当前远程分支

当添加完以下配置，再往远程推送代码时，只需要直接执行 `git push`，而不再需要执行 `git push origin branchName`
```git
git config --global push.default current
```

## 关于 antd form rules message 的一个小技巧

当一个表单项值为必填时，我们一般会这样写：
```TypeScript
<Form>
  <Form.Item name="name" label="名称" rules={[ { required: true, message: '请输入名称' } ]}>
    <Input />
  </Form.Item>
</Form>
```

其实 rules 里的 message 可以省略。antd 进行 form 验证输出错误信息时，会自动展示成：请输入名称。也就是会自动取 Form.Item 的 label  值

## 约束 Form 实例类型

实例化 `form` 时，传入自定义的 `values` 类型定义。后续在使用 `form` 对象上的方法时，就会有 `values` 的类型约束了
```
type SubSearchFormValue = {
  name: string
}
const [form] = Form.useForm<SubSearchFormValue>()
```

例如使用 `form.setFieldsValue` 时。因为类型上没有定义 age，就会报类型错误
```TypeScript
form.setFieldsValue({
  name: 'cxc',
  age: 10, 
})
```

## css user-select

对于元素内的一段文字，如果不想让鼠标能够选中它。使用 pointer-event: none 是不起使用的。必须使用：
```css
.text{
  user-select: 'none';
}
```

## 随机生成各种尺寸用户头像
https://pravatar.cc/

## react props 传入 null 和 undefined 是有区别的

在以下示例中
传入 null 时，会被当成一个正常值进行处理，不会走组件设置的默认值。因此当 type = null 时，组件的返回是 null
传入 undefined 时，会自动取 defaultProps 上的默认值。因此当 type = undefined 时，type 会被赋值为默认值 1，正常返回后面的内容
```TypeScript
interface IProps {
  type?: PRICE_TAG_TYPE
  color?: string
  text?: string
}

export const PriceTag = ({ type, color, text }: IProps) => {
  if (!type) {
    return null
  }
  return <Tag color={color || PRICE_TAG[type].color}>{text || PRICE_TAG[type].text}</Tag>
}

PriceTag.defaultProps = {
  type: 1,
  color: '',
  text: '',
}
```

## Form Item name path 类型约束
```TypeScript
export type NestedKeyOf<ObjectType extends object> = {
  [Key in keyof ObjectType & (string | number)]: ObjectType[Key] extends object
    ? [Key, ...NestedKeyOf<ObjectType[Key]>]
    : [Key]
}[keyof ObjectType & (string | number)]
假设定义了以下类型：
type Address = {
  street: string
  city: string
}

type FormValues = {
  user: {
    name: string
    password: string
  }
  address: Address
  rememberMe: boolean
}
```

使用方式：
```TypeScript
<Form>
  <Form.Item name={['user', 'name'] as NestedKeyOf<FormValues>}>
  ...
  </Form.Item>
</Form>
```

## eslint 检查结果保存生成文本文件
```
npx eslint -f compact "src/index.tsx"> result.txt
```

## Chrome Nextwork 筛选支持正则表达式

```
/workbenc|compare-price|price-match|price-health|pricing/
```

## 让 dayjs 支持 quarter 时间范围

dayjs 默认不支持 quarter 时间范围选择，需要引入以下插件才能支持
```js
import quarterOfYear from 'dayjs/plugin/quarterOfYear'
dayjs.extend(quarterOfYear)
```

此时下面的方法才能起作用：
```js
dayjs().startOf('quarter')
```

## WebSocket 请求 404 问题

http 协议是无状态的，websocket 协议是建立在一个 tcp 协议上的长链接协议
websocket 协议的三次握手机制和 http 协议是相同的，它使用http 的 Upgrade 协议头将 http 连接升级到webSocket 连接，这个特性使得 websocket 应用程序可以很容易地复用现有的基础设施
所以，使用 webSocket 协议需要在 nginx 中添加以下配置：
```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

## 比 html2canvas 快 100 倍的截图工具

https://github.com/zumerlab/snapdom?tab=readme-ov-file
