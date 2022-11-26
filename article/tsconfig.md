## `target`选项

用来指定生成的 ECMAScript  的目标版本，它支持以下几种选项：

```
"ES3" (default)
"ES5"
"ES6"/"ES2015"
"ES2016"
"ES2017"
"ES2018"
"ES2019"
"ES2020"
"ESNext"
```

在编写 ts 代码时，我们可以随意使用 ECMAScript 支持的各种新语法。但是，我们需要将这些浏览器可能不认识新新语法进行编译。那么到底要编译成 ECMAScript 的什么版本，可以完全由开发者自己来决定，来看一个简单的例子：

```ts
function foo() {
  const a = 1
  console.log(a)
}
```

在上面的代码中使用了 ES6 的语法`const`声明了变量，在后面编译的时候，就可以使用`target`选项来决定怎么编译它。

* 假如把`target`指定为`ES6`以及以上版本，那编译成 js 文件之后，它就还是`const`
* 假如把`target`指定为`ES6`以下的版本，那编译成 js 文件之后，它就变成了`var`

```js
"use strict";
function foo() {
  var a = 1;
  console.log(a);
}
```

## `module`选项

用来指定按照哪种模块规范去生成目标文件，它支持以下几种选项，其中默认值为`CommonJS`：

```
"None"
"CommonJS"
"AMD"
"System"
"UMD"
"ES6"
"ES2015"
"ESNext"
```

此外，`module`还依赖于`target`选项值：

* 当`target`被设置为`ES3`或者`ES5`的时候，`module`会被默认指定为`commonJs`
* 当`target`为其它值时，`module`会被默认指定为`ES6`

看几个小示例来解释一下上面的说明是什么意思。我们并不配置`module`的选项值，只通过修改`target`选项值来看最终的编译结果：

```js
import { doThing, Options  } from "./foo";

function doThingBetter(options: Options) {
  doThing(options);
  doThing(options);
}
```

第一种情况，将`target`设置为`ES5`。可以看到文件被按照`commonjs`的规范生成了代码：

```js
"use strict";
Object.defineProperty(exports, "__esModule", { value: true  });
var foo_1 = require("./foo");
function doThingBetter(options) {
  foo_1.doThing(options);
  foo_1.doThing(options);
}
```

第二种情况，将`target`设置为`ES6`。可以看到文件是按照`ES6`的模块规范生成的：

```js
import { doThing  } from "./foo";
function doThingBetter(options) {
 doThing(options);
 doThing(options);
}
```

这里要注意，模块加载机制是独立于语言实现的。ES6 使用的是`import /export`这种模块加载机制，只是这种模块加载机制是被浏览器默认实现了，不需要我们去引用第三方的模块加载器而已。

`target`指的是代码语法，而`module`指的是模块加载机制。因此，你也可以使用 ES5 的代码语法，而模块加载机制使用 ES6 的模块加载机制。例如：

```json
{
  "compilerOptions": {
    "target": "ES5",
    "module": "ESNEXT"
  }
}
```

源码示例：

```ts
// test.ts
import { doThing, Options } from "./foo";

function doThingBetter(options: Options) {
  const a = 1;
  doThing(options);
  doThing(options);
}
```

代码编译之后，可以看到仅是代码语法被编译成了 ES5，而其模块加载机制依然是 ES6 的语法：

```ts
// test.js
import { doThing } from "./foo";
function doThingBetter(options) {
    var a = 1;
    doThing(options);
    doThing(options);
}
```

编译之后的 umd 版本，也是同上面一样的道理：

```ts
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        var v = factory(require, exports);
        if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./foo"], factory);
    }
})(function (require, exports) {
    "use strict";
    Object.defineProperty(exports, "__esModule", { value: true });
    var foo_1 = require("./foo");
    function doThingBetter(options) {
        var a = 1;
        foo_1.doThing(options);
        foo_1.doThing(options);
    }
});
```

## `allowJs`选项

TypeScript 默认并不会编译项目中的 js 文件，如果想开启的话，就把此选项置为`true`。

## `skipLibCheck`选项

忽略检查所有的`.d.ts`文件

## `strict`选项

开启此配置项之后，TypeScript 会以严格的规则校验项目中所有的类型定义，这些规则主要包括：

* noImplicitAny
* noImplicitThis
* strictNullChecks
* strictPropertyInitialization
* strictBindCallApply
* strictFunctionTypes

#### 1、`noImplicitAny`

这条规则是不允许变量或者函数参数是隐式的`any`类型。

下面的参数`list`没有明确的类型定义，它有可能是任何值，TypeScript 也推导不出它是什么类型。虽然函数内部访问了它上面的`map`方法，但是 TypeScript 只能认为它是一个隐式的`any`类型：

```js
function extractIds (list) {
  return list.map(member => member.id)
}
```

还有一种常见示例是事件回调函数：

```js
function onClick (e) {
  //                     ^
  //                       Parameter 'e' implicitly has an 'any' type. ts(7006)
  e.preventDefault()
}
```

这里应该根据具体触发事件的元素去定义`e`的类型。比如，如果是鼠标点击事件，应该这样定义类型：

```js
function onClick(e: React.MouseEvent<HTMLButtonElement>) {
  e.preventDefault()
}
```

#### 2、`noImplicitThis`

这条规则是禁止在当前上下文有隐式的`this`定义。比如下面的示例：

```js
function uppercaseLabel () {
  return this.label.toUpperCase()
}

const config = {
  label: 'foo-config',
  uppercaseLabel
}

config.uppercaseLabel()
```

上面`config`中的`uppercaseLabel`引用了上面的同名方法。但是，在代码真正运行起来之前，`this`的在当前上下文中的类型定义其实是不确定的、模凌两可的。把它改造一下：

```js
const config = {
  label: 'foo-config',
  uppercaseLabel () {
    return this.label.toUpperCase()
  }
}
```

#### 3、`strictNullChecks`

这条规则会校验当前值如果可能返回`null`或者`undefined`，则会抛出类型错误提示。

下面的`show`方法参数的类型定义是一个字符串数组。但是，参数`data`有可能会是`null`或者`undefined`。所以，访问它上面的`map`方法就会抛出类型错误。

```js
function show(data?: string[]){
  data.map(o => console.log(o))
}
```

改造它的最保险的方式，就是添加`if`判断：

```js
function show(data?: string[]){
  if(Array.isArray(data){
    data.map(o => console.log(o))
  }
}
```

#### 4、`strictPropertyInitialization`

这条规则校验的是无论是在构造函数或者是类方法里访问类属性时，都必须有其相应的初始化定义。

```js
class Student {
  constructor (grade: number) {
    this.grade = grade
  }

  setLessons (lessons: number) {
    this.lessons = lessons
  }
}
```

上面的代码在`constructor`和`setlessons`中分别访问了类属性`grade`和`lessons`。因为在类中没有这两个属性的明确定义，所以就会抛出以下类型错误：

```
Property 'grade' does not exist on type 'Student'.ts(2339)

Property 'lessons' does not exist on type 'Student'.ts(2339)
```

改造一下修复此类型错误：

```js
class Student {
  grade = 1
  lessons = 1

  constructor(grade: number) {
    this.grade = grade
  }

  setRedoLessons(lessons: number) {
    this.lessons = lessons
  }
}
```

#### 5、`strictBindCallApply`

这条规则会校验`bind`、`call`和`apply`的使用场景。下面是`apply`的常规使用方式：

```js
function sum (num1: number, num2: number) {
  return num1 + num2
}

sum.apply(null, [1, 2])
// 3
```

可是`sum`函数只定义了两个传入参数。如果`strictBindCallApply`置为`true`，在使用`apply`调`sum`时传入了多余数量的参数，就会抛出类型错误：

```js
sum.apply(null, [1, 2, 3])
```

所以，如果`sum`函数的参数不确定的话，更优雅的实现方式应该是使用解构运算符：

```js
function sum (...args: number[]) {
  return args.reduce<number>((total, num) => total + num, 0)
}

sum.apply(null, [1, 2, 3])
// 6
```

## 参考资料

* https://dev.to/briwa/how-strict-is-typescript-s-strict-mode-311a


