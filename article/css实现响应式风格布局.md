### 效果预览：

![](https://img13.360buyimg.com/imagetools/jfs/t1/201710/12/5463/59075/6137085dEf25ef8b6/d8f63ab2f76b4427.jpg)

```tsx
import React from 'react'
import { Row, Col, Spin, Empty } from 'antd4'
import { toJS } from 'mobx'
import { observer } from 'mobx-react'

import s from './style.m.less'

export const AttributeDistribution = observer(() => {
  const attribute = toJS(store.attribute.value)

  return (
    <Spin spinning={store.attribute.fetching}>
      <Row className={s.box}>
        {attribute.length ? (
            return (
              <Col lg={{ span: 12 }} xl={{ span: 8 }} xxl={{ span: 6 }} key={item.attrNameZh} className={s.item}>
                <div style={{ height: '200px', textAlign: 'center', fontSize: '40px', lineHeight: '200px', color: '#999' }}>
                  {index + 1}
                </div>
              </Col>
            )
          })
        ) : (
          <div style={{ display: 'flex', justifyContent: 'center', width: '100%' }}>
            <Empty />
          </div>
        )}
      </Row>
    </Spin>
  )
})

```

css 代码

```css
.item {
  border-bottom: 1px solid #efefef;
  border-right: 1px solid #efefef;
  padding: 10px;
  transition: 0.3s all;
  &:hover {
    box-shadow: 0 0 30px rgba(0, 0, 0, 0.1);
  }
}
```

实现之后的一个基本效果：

![](https://img14.360buyimg.com/imagetools/jfs/t1/46914/4/17297/66055/613707bfEe2476a6d/bfb6a479de115e27.jpg)

### 去掉每一行最后一个元素的右边框

使用`nth-child`去掉每一行最后一个元素的**右边框**：

```css
.ant4-col-xxl-6 {
  &:nth-child(4n + 4) {
    border-right: none;
  }
}
```

我们来推演一下：每一行显示 4 个元素，`n`从 0 开始计数：

```
当 n 为 0 时，4n + 4 = 4
当 n 为 1 时，4n + 4 = 8 
当 n 为 2 时，4n + 4 = 12
...
```

所以，通过此选择器。可以完美的将每一行最后一个元素的右边框去掉

### 去掉最后一行元素的下边框

比较麻烦的是如何去掉**下边框**，行数不固定，去掉的还必须是最后一行的下边框。所以，问题就变成了，如何使用 css 来选择最后一行的元素

因为我们要去掉的仅仅是最后一行元素的下边框。但是最后一行不一定会正好有 4 个元素。如果少于 4 个的话，倒数第 2 行的元素就也会被选中的

要想选择最后一行的元素，先能选择到最后一行的第一个元素：

```css
.ant4-col-xxl-6 {
  &:nth-child(4n + 1) {
    background: skyblue;
    border-bottom: none;
  }
}
```

来推演一下：

```
当 n 为 0 时，4n + 1 = 1
当 n 为 1 时，4n + 1 = 5 
当 n 为 2 时，4n + 1 = 9
...
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/153724/27/21348/74039/6136c2cfE51655a07/0efb0c7aac281d93.jpg)

再选择最后 n 个元素：

```css
.ant4-col-xxl-6 {
  &:nth-last-child(-n + 4){
    background: #eee;
    border-bottom: none;
  }
}
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/64771/13/17264/58927/6136c2d2E4a17f7ef/e4ff68d634d8474e.jpg)

然后它们两个集合取一个交集，那么得到的是谁？对，就是最后一行的**第一个元素**

```css
.ant4-col-xxl-6 {
  &:nth-child(4n + 1):nth-last-child(-n + 4) {
    background: yellow;
    border-bottom: none;
  }
}
```

![](https://img12.360buyimg.com/imagetools/jfs/t1/199830/2/7135/68876/6136c51dE23f8d307/d3e373d0ac3f551c.jpg)

找到了最后一行的第一个元素，那么它和最后一行的其它元素是什么关系？对，和它是兄弟节点关系，而且是它后面的兄弟节点。css 中可以使用`~`来选择它们

```css
.ant4-col-xxl-6 {
  &:nth-child(4n + 1):nth-last-child(-n + 4) ~ div {
    border-bottom: none;
  }
}
```

接着补充 css 代码，最后的实现为：

```css
.ant4-col-xxl-6 {
  &:nth-child(4n + 4) {
    border-right: none;
  }
  &:nth-child(4n + 1):nth-last-child(-n + 4),
  &:nth-child(4n + 1):nth-last-child(-n + 4) ~ div {
    border-bottom: none;
  }
}
```

添加响应式控制：

```tsx
import React from 'react'
import { Row, Col, Spin, Empty } from 'antd4'

export const AttributeDistribution = observer(() => {
  const attribute = toJS(store.attribute.value)

  return (
    <Spin spinning={store.attribute.fetching}>
      <Row className={s.box}>
        {attribute.length ? (
            return (
              <Col lg={{ span: 12 }} xl={{ span: 8 }} xxl={{ span: 6 }} key={item.attrNameZh} className={s.item}>
                <div style={{ height: '200px', textAlign: 'center', fontSize: '40px', lineHeight: '200px', color: '#999' }}>
                  {index + 1}
                </div>
              </Col>
            )
          })
        ) : (
          <div style={{ display: 'flex', justifyContent: 'center', width: '100%' }}>
            <Empty />
          </div>
        )}
      </Row>
    </Spin>
  )
})
```

页面布局，可以通过`Col`组件的响应式属性来进行控制：

* `xxl`，表示屏幕大于等于`1600px`时进行渲染
* `xl`，表示屏幕大于等于`1200px`时进行渲染

其中的`span`表示一行中每个元素所占的比例

在 css 中，则使用`@media`来控制样式的响应式渲染，完整的 css 代码如下：

```css
.box {
  :global {
    @media (min-width: 1600px) {
      .ant4-col-xxl-6 {
        &:nth-child(4n + 4) {
          border-right: none;
        }
        &:nth-child(4n + 1):nth-last-child(-n + 4),
        &:nth-child(4n + 1):nth-last-child(-n + 4) ~ div {
          border-bottom: none;
        }
      }
    }
    @media (min-width: 1200px) and (max-width: 1600px) {
      .ant4-col-xl-8 {
        &:nth-child(3n + 3) {
          border-right: none;
        }
        &:nth-child(3n + 1):nth-last-child(-n + 3),
        &:nth-child(3n + 1):nth-last-child(-n + 3) ~ div {
          border-bottom: none;
        }
      }
    }
  }
}
```
