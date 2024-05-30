## Promises/A+ 规范

 ❗️开始之前一定要熟悉以下规范，至少通读一遍

Promise 规范整个篇幅其实不长，但确实有点儿枯燥。其实大家日常使用 promise 时对它的基本运行机制已经比较熟悉了，只是规范使用标准化的语言将它抽象描述出来了而已

下面是中文两版规范，我曾经试着想翻译一下英文版，结果发现实在是翻译不下去了😳

* 英文版：https://promisesaplus.com/
* 中文版：https://promisesaplus.com.cn

手写一个 promise 实现，曾经（可能现在也是）是一道经典的面试题。如果自己能够完全实现一次，将会对 promise 的执行原理有一个更深层的理解

## 实现过程

### 1. Promise 有三种状态

三种状态分别是：PENDING、FULFILLED、REJECTED。而且只能从 PENDING 变为 FULFILLED 或 REJECTED，一旦状态变更，不可逆转【规范 2.1】

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
```

### 2. 声明一个 Promise 类

Promise 构造函数接受一个`executor`函数，`executor`接受两个函数作为回调，分别是`resolve`和`reject`，通过调用它们来改变 promise 的状态

需要在 promise 上定义一个`state`用来保存当前状态，初始状态为 PENDING。定义一个`value`用来保存成功状态值【规范1.3】。定义一个`reason`用来保存失败状态原因【规范1.5】

调用`resolve`时，状态变为 FULFILLED，保存`value`值。调用`reject`时，状态变为 REJECTED，保存`reason`值

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class Promise {
  constructor(executor) {
    this.state = PENDING
    this.value = undefined
    this.reason = undefined

    let resolve = (value) => { 
      this.state = FULFILLED
      this.value = value
    }

    let reject = (reason) => { 
      this.state = REJECTED
      this.reason = reason
    }

    try {
      executor(resolve, reject)
    }
    catch (e) {
      reject(e)
    }
  }
}
```

### 3. Promise 的`then`方法

promise 上一定有一个`then`方法，它也接受两个函数作为回调，当 promise 状态变为`fulfilled`时，第一个函数会被调用，当 promise 状态变为`rejected`时，第二个函数会被调用

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class Promise {
  constructor(executor) {
    this.state = PENDING
    this.value = undefined
    this.reason = undefined

    let resolve = (value) => {
      this.state = FULFILLED
      this.value = value
    }

    let reject = (reason) => {
      this.state = REJECTED
      this.reason = reason
    }

    try {
      executor(resolve, reject)
    }
    catch (e) {
      reject(e)
    }
  }

  then(onResolve, onReject) {
    if (this.state === FULFILLED) {
      onResolve(this.value)
    }

    if (this.state === REJECTED) {
      onReject(this.reason)
    }
  }
}
```

写一段代码测试一下：

```js
const promise = new Promise((resolve, reject) => {
  resolve('成功')
}).then(data => {
  console.log(data)
}, err => {
  console.log(err)
})
```

输出：

```
成功
```

### 4. 继续改造 promise 的执行机制

当我们将上面的测试代码改为如下逻辑，使用`setTimeout`包一下`resolve`方法，你会发现不会有任何输出：

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功')
  }, 1000)
}).then(data => {
  console.log(data)
}, err => {
  console.log(err)
})
```

这是因为`resolve`方法是异步执行的，而我们实现的 Promise 类在执行到`then`方法时，当前 promise 状态还是 PENDING，不会立即执行`resolve`方法

所以，需要我们在`then`方法中，在 PENDING 状态时将回调函数都分别保存两个数组里，等到 promise 状态变为 FULFILLED 或 REJECTED 时，再依次遍历执行这些回调

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class Promise {
  constructor(executor) {
    this.state = PENDING
    this.value = undefined
    this.reason = undefined

    this.onResolvedCallbacks = []
    this.onRejectedCallbacks = []

    let resolve = (value) => {
      this.state = FULFILLED
      this.value = value
      this.onResolvedCallbacks.forEach(fn => fn())
    }

    let reject = (reason) => {
      this.state = REJECTED
      this.reason = reason
      this.onRejectedCallbacks.forEach(fn => fn())
    }

    try {
      executor(resolve, reject)
    }
    catch (e) {
      reject(e)
    }
  }

  then(onResolve, onReject) {
    if (this.state === FULFILLED) {
      onResolve(this.value)
    }

    if (this.state === REJECTED) {
      onReject(this.reason)
    }

    if (this.state === PENDING) {
      this.onResolvedCallbacks.push(() => {
        onResolve(this.value)
      })
      this.onRejectedCallbacks.push(() => {
        onReject(this.reason)
      })
    }
  }
}
```

两次运行上面测试代码将会成功输出

---


```js
// 1. promise 有三个状态值：PENDING、FULFILLED、REJECTED【规范 2.1】
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class Promise {
  // 2. promise 接受一个`executor`函数，该函数接受两个函数作为回调参数，分别是`resolve`和`reject`，这两个函数在 promise 状态改变时会被调用
  constructor(executor) {
    // 3. this.state 用来保存当前 promise 的状态，并且初始值为 PENDING
    this.state = PENDING
    
    // 4. this.value 用来保存当前 promise 成功状态的值，这个值可以是 undefined/thenable/promise，但它的初始值是 undefined【规范1.3】
    this.value = undefined

    // 5. this.reason 用来保存当前 promise 失败状态的值，它的初始值是 undefined【规范1.5】
    this.reason = undefined

    // 6. 声明一个数组，用来保存当前

    // 6. 声明一个 resolve 方法，当 promise 变为成功状态时调用它
    let resolve = () => {

    }
  }

  // 7. promise 上必须有一个 then 方法，它接受两个函数作为回调参数，成功时执行第一个函数，失败时执行第二个函数
  then = (onFulfilled, onRejected) => {

  }
}
```

promise 只能从`pending`状态转变为`fulfilled`或`rejected`状态，状态一旦确认，就不能再改变



在开始实现之前，我们想想一个 promise 的最基本形态是什么？

1. promise 有三个状态值：PENDING、FULFILLED、REJECTED【规范 2.1】

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
```

2. promise 接受一个`executor`函数，该函数接受两个函数作为回调参数，分别是`resolve`和`reject`，这两个函数在 promise 状态改变时会被调用

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class Promise {
  constructor(executor) {
    
  }
}
```

```js
new Promise((resolve, reject) => {
  resolve('fulfilled')
}).then((value) => {
  console.log(value)
})
```

3. promise 必须有一个`then`方法，它也接受两个函数作为回调函数，当 promise 状态变为`fulfilled`时，第一个函数会被调用，当 promise 状态变为`rejected`时，第二个函数会被调用



## 测试用例

https://github.com/promises-aplus/promises-tests

```nodejs
npx promises-aplus-tests test.js
```