枚举是 ts 中为数不多的，不属于 js 语言的扩展的特性之一

枚举允许开发者定义一系列的常量组合，不仅能够作为一定意义上的文档说明，也可以对各种场景做明确的定义

TS 默认提供了基于数值和字符两种类型的枚举

## 数值型枚举

使用`enum`关键字定义一个枚举。在此枚举中，没有给其属性指定任何明确的初始值。所以，`Up`的初始值默认为`0`，其它属性值依次默认为`Down = 1`, `Left = 2`, `Right = 3`

因此，就是说，定义一个枚举时如果没有指定任何初始值，那么它就是一个数值型枚举

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}
```

1. 如果明确指定了初始值

```ts
enum Direction {
  Up = 1,
  Down,
  Left,
  Right,
}
```

在上面的枚举中，`Up`的属性值为`1`，那么下面其它的属性值就会自动基于这个值进行累加，也就是`Down = 2`，`Left = 3`，`Right = 4`

也可以指定小数。如果`Up = 1.1`，那么`Down = 2.1`，`Left = 3.1`，`Right = 4.1`。也就是说：**数值型枚举只会按照整数`1`进行累加**

2. 可以引用其它枚举的属性值作为初始值

下面的`E2.A`引用了`E1.X`的属性值，那么`E2`的其它两个属性值会被自动初始化为`E2.B = 2`和`E2.C = 3`：

```ts
enum E1 {
  X = 1,
  Y,
  Z,
}
 
enum E2 {
  A = E1.X,
  B,
  C,
}
```

3. 可以使用小括号表达式来作为初始值

下面的`E1.X`和`E2.A`的初始值都是`3`：

```ts
enum E1 {
  X = (1 + 2),
  Y,
  Z,
}
 
enum E2 {
  A = E1.X,
  B,
  C,
}
```

4. 可以在初始值中使用`+`、`-`、`*`、`/`、`%`、`<<`、`>>`、`>>>`、`&`、`|`、`^`等二元操作符：

```ts
enum FileAccess {
  // constant members
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // computed member
  G = "123".length,
}
```

## 字符型枚举

字符串型枚举比较简单，它的属性没有类似于数值型枚举中属性自动累加的特性。因此，每个属性都必须有一个明确的初始值：

```ts
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}
```

## 混合型枚举

可以在一个枚举中同时定义数值和字符串两种属性类型。但是可能会让人蒙圈，因此，非必要情况下不要使用这种形式：

```ts
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}
```

## 枚举成员类型

如果一个枚举的全部属性值都是一个**纯字面量**，例如：

* 字符串：`bar`、`foo`
* 数值：`1`、`2`或者`-1`、`-2`

那么此枚举的所有成员也会**自动成为一个类型**

例如在下面示例中，`ShapeKind`枚举拥有两个属性，因为没有明确指定值，那么它们的属性值默认为`0`、`1`

然后`Circle`和`Square`两个类型的`kind`属性，分别引用了`ShapeKind.Circle`和`ShapeKind.Square`

后面声明了类型为`Circle`的变量`c`，它的`kind`属性值被指定为了`ShapeKind.Square`

```ts
enum ShapeKind {
  Circle,
  Square,
}
 
interface Circle {
  kind: ShapeKind.Circle;
  radius: number;
}
 
interface Square {
  kind: ShapeKind.Square;
  sideLength: number;
}
 
let c: Circle = {
  kind: ShapeKind.Square,
  radius: 100,
};
```

此时，会收到 ts 抛出的类型错误。因为`ShapeKind.Circle`和`ShapeKind.Square`属于两种不同的类型，彼此并不兼容

```
Type 'ShapeKind.Square' is not assignable to type 'ShapeKind.Circle'.
```

## 映射反转

枚举中的数值型成员，还拥有一个额外的特性。就是可以从枚举**属性值**映射回枚举**属性名**

在下面示例中，可以看到，访问`Enum.A`得到的是它的属性值。如果反过来，通过`Enum[a]`就可以得到它的属性名

```ts
enum Enum {
  A,
}
 
let a = Enum.A;
let nameOfA = Enum[a]; // "A"
```