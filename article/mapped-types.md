映射类型一般用来基于其它类型来创建类型，提高类型的复用性

映射类型的语法类似于“索引”语法，可以用来提前声明还没有用到的属性类型

下面的`OnlyBools`定义的对象类型表示，属性名称可以是任意的一个字符串类型，属性值必须是布尔类型：

```ts
type OnlyBools = {
  [key: string]: boolean;
};

const conforms: OnlyBools = {
  del: true,
  rodney: false,
};
```

下面是示例是通过映射类型递归的将传入的泛型的属性提取出来，并将其每个属性的值都改为布尔类型：

```ts
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};

type FeatureFlags = {
  darkMode: () => void;
  newUserProfile: () => void;
};

type FeatureOptions = OptionsFlags<FeatureFlags>;

```

将返回类型：

```ts
type FeatureOptions = {
    darkMode: boolean;
    newUserProfile: boolean;
}
```

### 映射类型修改器

在映射类型中有两种修改器可以使用：`readonly`和`?`。前者表示只读类型，后者表示可选类型

可以在修改器前面添加`+`或者`-`来表示是给当前类型添加此修改器，还是去除此修改器

例如，创建一个`CreateMutable`用于去除类型上的`readonly`修改器：

```ts
type CreateMutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property];
};

type LockedAccount = {
  readonly id: string;
  readonly name: string;
};
 
type UnlockedAccount = CreateMutable<LockedAccount>;
```

经过`CreateMutable`处理之后，`UnlockedAccount`上的`readonly`修改器就被全部去除了：

```ts
type UnlockedAccount = {
    id: string;
    name: string;
}
```

### 在映射类型中使用`as`

1. 在 ts4.1 及之后的版本中可以在映射类型中使用`as`，对一个类型进行重新映射

```ts
type MappedTypeWithNewProperties<Type> = {
    [Properties in keyof Type as NewKeyType]: Type[Properties]
}
```

2. 可以结合模版字符串类型，对之前的类型重新进行映射，进而生成一个新的类型。下面示例中，就是将每一个属性名称的首字母通过`Capitalize`改成了大写，并在其前面都加了一个`get`

```ts
type Getters<Type> = {
    [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
};
 
interface Person {
    name: string;
    age: number;
    location: string;
}
 
type LazyPerson = Getters<Person>;
```

最后`LazyPerson`的类型为：

```ts
type LazyPerson = {
    getName: () => string;
    getAge: () => number;
    getLocation: () => string;
}
```

3. 可以通过最终产生`never`类型将某些类型过滤掉：

```ts
type RemoveKindField<Type> = {
    [Property in keyof Type as Exclude<Property, "kind">]: Type[Property]
};
 
interface Circle {
    kind: "circle";
    radius: number;
}
 
type KindlessCircle = RemoveKindField<Circle>;
```

`KindlessCircle`的类型会变成：

```ts
type KindlessCircle = {
    radius: number;
}
```

4. 可以对任意类型进行映射，不仅仅只是`string`或者`number`等基础类型：

```ts
type EventConfig<Events extends { kind: string }> = {
    [E in Events as E["kind"]]: (event: E) => void;
}
 
type SquareEvent = { kind: "square", x: number, y: number };
type CircleEvent = { kind: "circle", radius: number };
 
type Config = EventConfig<SquareEvent | CircleEvent>
```

`Config`的类型最终为：

```ts
type Config = {
    square: (event: SquareEvent) => void;
    circle: (event: CircleEvent) => void;
}
```