**An open standard for sound, interoperable JavaScript promises—by implementers, for implementers.**

Promise 代表了一个异步操作的最终结果。与`promise`进行交互的一个主要方式就是通过它的`then`方法

This specification details the behavior of the then method, providing an interoperable base which all Promises/A+ conformant promise implementations can be depended on to provide. 
本规范详细描述了`then`方法的行为，

As such, the specification should be considered very stable. 
Although the Promises/A+ organization may occasionally revise this specification with minor backward-compatible changes to address newly-discovered corner cases, we will integrate large or backward-incompatible changes only after careful consideration, discussion, and testing.

### 术语说明

* `promise`是一个符合此规范的、带有一个`then`方法的对象或者函数
* `thenable`是一个定义了`then`方法的对象或者函数
* `value`就是一个任意的 js 合法变量（包括：`undefined`、一个`thenable`对象、或者是一个`promise`对象
* `exception`就是一个使用`throw`抛出的异常变量值
* `reason`指的是一个`promise`被拒绝时的原因

#### Promise 状态

一个 promise 必须是这三种状态之一：`pending`、`fulfilled`、`rejected`

1. 当 promise 为`pending`状态时，它可能会转变为`fulfilled`或者`rejected`状态
2. 当 promise 为`fulfilled`状态时，它就不能再转变为任何其它状态了，而且必须带有一个不可变的 value
3. 当 promise 为`rejected`状态时，它也不能再转变为任何其它状态了，而且必须带有一个不可变的原因

Here, “must not change” means immutable identity (i.e. ===), but does not imply deep immutability.
