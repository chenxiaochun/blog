TS 的类型系统是结构化的。结构化类型系统背后的思想是如果它们的成员类型是兼容的，则它们就是兼容的

例如，下面的`Foo`和`Bar`类型都具有相同类型的属性`x`。虽然两个类型的名称不一样，但因为它们具有相同的结构化类型。因此，使用两个类型声明的变量`a`和`b`的类型依然是互相兼容的：

```ts
type Foo = {
    x: string
}

type Bar = {
    x: string
}

let a: Foo = {
    x: '1'
}

let b: Bar = {
    x: '2'
}

a = b
```

但是在 C# 或者 Java 中，使用的是名义化类型，就是两个类型的**名称**必须完全相同，才是互相兼容的类型。因此，上面的情况在这些语言中是不被允许的

```ts
interface Pet {
  name: string;
}
class Dog {
  name: string;
}
let pet: Pet;
// OK, because of structural typing
pet = new Dog();
```

如果在 java 或者 c# 等语言中，在上面将`Dog`实例赋值给`pet`就会抛出异常。因为`Dog`并没有明确定义它实现了`Pet`类型

那 TS 的结构类型，是基于 js 最类型的写法实现的。因为在 js 中我们会非常广泛的使用结构化的匿名对象来表示各种关系

假如有两个类型：`X`和`Y`，它们互相兼容的基本规则就是`Y`要拥有至少一个与`X`相同的成员

例如下面的`Pet`类型拥有一个`name`属性。那么`dog`就可以赋值给`greet`方法。因为`dog`中也存在一个相同的`name`属性。而且这种处理还是递归式的，ts 会检查类型中的每一个子类型

```ts
interface Pet {
  name: string;
}
let dog = { name: "Lassie", owner: "Rudd Weatherwax" };
function greet(pet: Pet) {
  console.log("Hello, " + pet.name);
}
greet(dog); // OK
```

### 两个函数类型对比

1. 以参数进行对比

从上面可以看出，ts 原始类型之间的类型对比是比较简单的。接下来，看一下函数之间的类型对比就会比较复杂了

下面有两个函数`x`和`y`。函数`x`只有一个 number 类型的参数。函数`y`拥有两个参数，一个为 number 类型，另一个为 string 类型

```ts
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;
y = x; // OK
x = y; // Error
```

在此示例中，函数`x`的参数在函数`y`中都有与之相匹配的参数及类型。因此，将`x`赋给`y`就是成立的

但是，反过来。因为函数`y`的第二个参数是必传的，而在`x`中并没有与之相匹配的参数。因此，将`y`赋给`x`就是不成立的

这个示例，可能让你疑惑的点在于：为什么`x`即使少一个参数类型，也还依然能够赋值给`y`。那是因为在 js 中，多余参数被忽略是一个很常见的情景。例如，数组上的`forEach`方法的回调函数最多需要三个参数，但实际使用中，即使只传入第一个参数，也是没问题的

```ts
let items = [1, 2, 3];
// Don't force these extra parameters
items.forEach((item, index, array) => console.log(item));
// Should be OK!
items.forEach((item) => console.log(item));
```

2. 以返回值进行对比

如果两个函数没有参数，但返回值是不同的，又该如何对比呢？

下面两个函数中，`x`的返回值只有一个属性，而`y`的返回值有两个属性。可以看到，`y`可以赋值给`x`，但`x`不能赋值给`y`。能否赋值的逻辑颠倒过来了

这是因为 ts 的类型系统规定，被赋值的函数`x`的返回值必须是`y`返回值的子类型

```ts
let x = () => ({ name: "Alice" });
let y = () => ({ name: "Alice", location: "Seattle" });
x = y; // OK
y = x; // Error, because x() lacks a location property
```

## 函数参数双变

当对函数参数进行对比时，能否能赋值成功，取决于源函数的参数是否可以赋值给目标函数的参数，反过来也是同理

这其实是不健壮的，因为调用者可能会在最后给函数多传一个额外参数。但在函数定义的位置，却缺少这个特定类型的参数。实际情况中，虽然这种错误很少出现。但是，允许这种形式可以使代码开启更多的编写模式

```ts
enum EventType {
  Mouse,
  Keyboard,
}
interface Event {
  timestamp: number;
}
interface MyMouseEvent extends Event {
  x: number;
  y: number;
}
interface MyKeyEvent extends Event {
  keyCode: number;
}
function listenEvent(eventType: EventType, handler: (n: Event) => void) {
  /* ... */
}
// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MyMouseEvent) => console.log(e.x + "," + e.y));
// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) =>
  console.log((e as MyMouseEvent).x + "," + (e as MyMouseEvent).y)
);
listenEvent(EventType.Mouse, ((e: MyMouseEvent) =>
  console.log(e.x + "," + e.y)) as (e: Event) => void);
// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));
```

## 可选参数和 rest 参数

当函数间进行类型对比时，可选参数和必选参数是可以互换的。只要其中一方的函数参数都是可选参数，那么另外一方即使没有定义相应的参数，它们之间的类型也是互相兼容的

例如，下面的 foo 函数带有两个可选参数 a 和 b。那么 bar 函数即使没有定义这两个参数，它和 foo 的类型也是互相兼容的。反之，如果一方有一个必选参数，那么另外一方也必须定义与之类型相匹配的参数

```ts
let foo = (a?: string, b?: number) => {}
let bar = () => {}

foo = bar
bar = foo
```

一个函数的 rest 参数，会被当成一个具有无限数列的可选参数来进行处理

从类型的角度来看，这可能不太合理。但因为实际上，可选参数的用法并没有那么深入人心。在代码运行时，将可选参数换成 undefined 传入也是等价的

下面的示例中需要给调用的回调函数传入数量不等的参数：

```ts
function invokeLater(args: any[], callback: (...args: any[]) => void) {
  /* ... Invoke callback with 'args' ... */
}
// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ", " + y));
// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ", " + y));
```

## 函数重载

当函数有类型重载定义时，两个函数如果想类型兼容，就必须双方都具有相同的类型定义才可以

## 枚举

枚举和数值类型都是互相兼容的。但是不同枚举间的值是不兼容的

例如，下面示例中想将`Color.Green`赋值给`Status.Ready`，就是报类型错误

```ts
enum Status {
  Ready,
  Waiting,
}
enum Color {
  Red,
  Blue,
  Green,
}
let ready = Status.Ready;
ready = Color.Green; // Error
```

```
Type 'Color.Green' is not assignable to type 'Status'.
```

## Class 类型兼容性

Class 之间的类型兼容对比方式，与纯对象和接口的对比方式是类似的。但 class 类型与这两者存在不同的是，它同时拥有实例类型和静态类型两种场景。但是 class 之间做类型对比时，只会对它们的实例成员类型进行对比。Class 的静态成员类型和构造函数类型不会影响 class 类型之间的兼容性

在下面示例中，两个 class 都拥有一个相同类型的属性`feet: number`。但是它们构造函数的参数个数和类型明显都是不一样的，Animal 拥有两个参数，Size 只拥有一个参数

但并不影响两个 class 实例之间的类型兼容性

```ts
class Animal {
  feet: number;
  constructor(name: string, numFeet: number) {}
}
class Size {
  feet: number;
  constructor(numFeet: number) {}
}
let a: Animal;
let s: Size;
a = s; // OK
s = a; // OK
```

#### Class 的 private 成员和 protected 成员

Class 的 private 成员和 protected 成员会影响它们之间的类型兼容性

也就是说，class 之间进行类型对比时，双方的 private 和 protected 成员必须都必须要继承于同一个父类才是互相兼容的

例如下面实例中，Parent 中定义了一个 `private name = 'cxc'`，Animal 和 Size 都继承了它。这样并不会影响两个 class 的实例类型的兼容性：

```ts
class Parent {
  private name = 'cxc'
}

class Animal extends Parent {
  feet: number = 1;
  constructor(name: string, numFeet: number) {
    super()
  }
}

class Size extends Parent {
  feet: number = 2;
  constructor(numFeet: number) {
    super()
  }
}

let a: Animal = new Animal('cxc', 1);
let s: Size = new Size(2);

a = s; // OK
s = a; // OK
```

但是，如果两个 Class 继承的是不同父类，即使继承的两个父类的结构和类型是完全相同的，那么两个子类的实例类型也是不兼容的

下面示例中，Animal 和 Size 继承了不同的父类，虽然两个父类都定义了`private name = 'cxc'`，但两个子类的实例类型仍然是互不兼容的：

```ts
class Parent1 {
  private name = 'cxc'
}

class Parent2 {
  private name = 'cxc'
}

class Animal extends Parent1 {
  feet: number = 1;
  constructor(name: string, numFeet: number) {
    super()
  }
}

class Size extends Parent2 {
  feet: number = 2;
  constructor(numFeet: number) {
    super()
  }
}

let a: Animal = new Animal('cxc', 1);
let s: Size = new Size(2);

a = s;
s = a;
```

```
Type 'Size' is not assignable to type 'Animal'.
  Types have separate declarations of a private property 'name'.
Type 'Animal' is not assignable to type 'Size'.
  Types have separate declarations of a private property 'name'.
```

那如果，把这两个父类的 private 修饰符去掉，这两个 Class 的实例类型也就互相兼容了

## 泛型的类型兼容性

TS 是一套结构化的类型系统。因此，类型参数只有在被某个成员真正使用了之后，才会影响类型之间的兼容性

例如下面示例中，x 和 y 都定义为了`Empty`类型，接收一个泛型参数`T`，虽然在声明 x 和 y 的类型时，传入的泛型参数类型不同，但 x 和 y 依然是互相兼容的

因为在`Empty`类型中，仅仅只是声明了泛型参数`T`，其内部成员并没有真正去使用它。因此，这个泛型参数就不会对两个类型的兼容性产生影响

```ts
interface Empty<T> {}
let x: Empty<number>;
let y: Empty<string>;
x = y;
```

再看另一个示例，x 和 y 在声明类型时传入了不同的泛型参数，`number`和`string`是互不兼容的两个类型。而且在`NotEmpty`中的成员还确实使用了此泛型参数。因此，x 和 y 就不是互相兼容的类型：

```ts
interface NotEmpty<T> {
  data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;
x = y;
```

还有一种情况，就是虽然定义了泛型参数，但在实际使用时，并没有给其传入具体的类型，这其实相当于给此类型参数指定成了 any 类型。因此，下面的`identity`和`reverse`依然是互相兼容的类型：

```ts
let identity = function <T>(x: T): T {
  return x
};
let reverse = function <U>(y: U): U {
  return y
};
identity = reverse;
reverse = identity;
```

## 子类型的兼容性

在 TypeScript 中，有以下若干常用子类型。当 [strictNullChecks](https://www.typescriptlang.org/tsconfig#strictNullChecks) 设置为 false 时，各子类型之间的兼容性表现形式为：

|   | `any` | `unknown` | `object` | `void` | `undefined` | `null` | `never`
--- | --- | --- | --- | --- |--- | --- | --- |
`any`→     |    | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
`unknown`→ | ✅ |    | ❌ | ❌ | ❌ | ❌ | ❌ |
`object`→  | ✅ | ✅ |    | ❌ | ❌ | ❌ | ❌ |
`void`→    | ✅ | ✅ | ❌ |    | ❌ | ❌ | ❌ |
`undefined`→| ✅ | ✅ | ✅ | ✅ |    | ✅ | ❌ |
`null`→    | ✅ | ✅ | ✅ | ✅ | ✅ |    | ❌ | 
`never`→   | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |    |

从上面表格数据可以得出以下几条结论：

* 任何类型都可以赋值给自身类型
* 任何类型都可以赋值给 any 和 unknown 类型
* unknown 和 never 基本是一对相反类型。unknown 可以接受任何子类型的赋值；never 除了本身，不接受任何其它子类型的赋值。unknown 除了 any 和自身，不能赋值给任何其它子类型；never 却可以赋值给任何子类型
* void 不接受除了 any、unknown、never、undefined 和 null 以外其它子类型的赋值与被赋值

## 参考资料

* https://jkchao.github.io/typescript-book-chinese/faqs/type-system-behavior.html
* https://segmentfault.com/a/1190000021898024
