使用语法类似于 js 中的三元运算符

下面示例中，如果`Dog`类型可以继承于`Animal`类型，则返回`number`类型，否则就会返回`string`类型

```ts
interface Animal {
  live(): void;
}
interface Dog extends Animal {
  woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string;
 
type Example2 = RegExp extends Animal ? number : string;
```

### 条件类型与泛型结合

例如`createLabel`支持多种入参类型和返回类型。一般这种情况，我们可以用函数重载来实现。为了支持每一种类型定义，导致定义的函数类型非常多，无论对于使用者和开发者来说都很麻烦

```ts
interface IdLabel {
  id: number /* some fields */;
}
interface NameLabel {
  name: string /* other fields */;
}
 
function createLabel(id: number): IdLabel;
function createLabel(name: string): NameLabel;
function createLabel(nameOrId: string | number): IdLabel | NameLabel;
function createLabel(nameOrId: string | number): IdLabel | NameLabel {
  throw "unimplemented";
}
```

如果换成使用条件类型来定义，就会变得非常简洁：

```ts
type NameOrId<T extends number | string> = T extends number
  ? IdLabel
  : NameLabel;
```

### 条件类型约束

在下面示例中，会抛出`Type '"message"' cannot be used to index type 'T'.`的类型错误。因为类型`T`确实不知道它上面是否有一个`message`属性

```ts
type MessageOf<T> = T["message"];
```

这时候可以使用条件类型来对它做进一步约束：

```ts
type MessageOf<T extends { message: unknown }> = T["message"];
 
interface Email {
  message: string;
}
 
type EmailMessageContents = MessageOf<Email>; // string 类型
```

更进一步的，如果我们希望`MessageOf`类型可以传入任意类型。如果有`message`属性，返回其类型。如果没有`message`属性，则默认为`never`类型

```ts
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;
 
interface Email {
  message: string;
}
 
interface Dog {
  bark(): void;
}
 
type EmailMessageContents = MessageOf<Email>; // 返回 string 类型
 
type DogMessageContents = MessageOf<Dog>; // 返回 never 类型
```

下面定义一个`Flatten`类型，用来提取数组中的元素类型。如果传入的是非数组类型，则直接返回其类型：

```ts
type Flatten<T> = T extends any[] ? T[number] : T

type s = Flatten<string[]> // 返回 string 类型

type n = Flatten<number> // 返回 number 类型
```

`T[number]`表示按下标访问数组元素类型。`T['length']`可以访问数组的长度作为类型：

```ts
type A = ['a', 'b', 'c']

type C = A['length'] // 3
type B = A[number] // "a" | "b" | "c"
```

### 条件类型内使用`infer`操作符

使用`infer`来重新实现上面的`Flatten`类型。当条件分支为`true`时，返回推断出的`Item`类型，否则直接返回传入的类型：

```ts
type Flatten<Type> = Type extends Array<infer Item> ? Item : Type;

type Str = Flatten<string[]>;

type Num = Flatten<number>;
```

使用`infer`推断返回一个函数的返回值类型：

```ts
type ReturnType<Type> = Type extends (...args: never[]) => infer Return
  ? Return
  : never;
 
type Num = ReturnType<() => number>; // 返回 number 类型
     
type Str = ReturnType<(x: string) => string>; // 返回 string 类型
     
type Bools = ReturnType<(a: boolean, b: boolean) => boolean[]>; // 返回 boolean 类型
```

当遇到多个重载函数时，它只会使用最后一个函数定义的返回类型：

```ts
declare function stringOrNum(x: string): number;
declare function stringOrNum(x: number): string;
declare function stringOrNum(x: string | number): string | number;
 
type T1 = ReturnType<typeof stringOrNum>; // 返回 string | number
```

### 类型自动分发

在下面的`ToArray`泛型中，如果`Type`接收了一个复合类型，那么在条件类型中，这个复合类型就会被自动分发成多个类型

```ts
type ToArray<Type> = Type extends any ? Type[] : never;
 
type StrArrOrNumArr = ToArray<string | number>; // 返回 string[] | number[]
```

如果不想让它自动分发成多个类型，可以给`extends`两侧的关键字用`[]`括起来：

```ts
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;
 
type StrArrOrNumArr = ToArrayNonDist<string | number>; // 返回 (string | number)[]
```