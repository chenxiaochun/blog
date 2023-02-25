TS 的类型兼容是基于结构子类型的。结构类型是一种仅基于类型的成员来关联类型的方式。来看下面的示例：

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

如果两个函数没有参数，返回值是不同的，又该如何对比呢？

下面两个函数，`x`的返回值只有一个属性，而`y`的返回值有两个属性

可以看到，能否赋值的逻辑颠倒过来了。`y`可以赋值给`x`，但`x`不能赋值给`y`

这是因为 ts 的类型系统规定，函数`x`的返回值必须是`y`返回值的子类型才可以

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

## 关于 class 类型

Class 类型的工作方式与纯对象和接口是差不多的。但 class 有一个不同的地方在于，它还拥有实例类型和静态类型两种情况。但是在对两个 class 类型进行对比，只会对它们的实例成员进行对比。Class 的静态成员和构造函数不会影响它们之间的兼容性

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

### Class 中的 private 成员和 protected 成员

两个 class 类型进行对比时，它们的 private 成员和 protected 成员是会对两者的类型兼容有影响的

在对两个 class 实例类型进行检查时，如果一方某在某个 private 成员，那么另外一方，也要存在相同




