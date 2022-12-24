### 字符串模版类型是基于字符串类型

使用语法与 js 中的字符串模版是一样的，只不过字符串模版类型只能用于类型中

```ts
type World = 'world'

type Greeting = `hello ${World}`
```

此时`Greeting`的类型为`hello world`

### 配合使用联合类型，还可以展开为更多的字符串类型

```ts
type EmailLocaleIDs = "welcome_email" | "email_heading";
type FooterLocaleIDs = "footer_title" | "footer_sendoff";
 
type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
```

此时`AllLocaleIDS`的类型为`type AllLocaleIDs = "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"`

虽然这种生成大型字符串类型的使用场景比较少，但还是推荐开发者可以优先考虑使用它

### 再看一种实例应用

有这样一个普通对象：

```ts
const passedObject = {
  firstName: "Saoirse",
  lastName: "Ronan",
  age: 26,
};
```

还有一个`makeWatchedObject`方法，使用它可以给传入的对象添加一个监听方法`on`。`on`方法的使用形式一般就是`on(eventName: string, callBack: (newValue: any) => void)`

这里可以使用对象模版字符串生成类型对`on`的两个参数类型做进一步约束。第一个参数只能为`属性名称 + Changed`的形式，第二个回调函数的参数类型必须和属性类型保持一致

```ts
type PropEventSource<Type> = {
    on<Key extends string & keyof Type>
        (eventName: `${Key}Changed`, callback: (newValue: Type[Key]) => void ): void;
};

declare function makeWatchedObject<Type>(obj: Type): Type & PropEventSource<Type>;

const person = makeWatchedObject({
  firstName: "Saoirse",
  lastName: "Ronan2222",
  age: 26
});

person.on("firstNameChanged", newName => {
    console.log(`new name is ${newName.toUpperCase()}`);
});

person.on("ageChanged", newAge => {
    if (newAge < 0) {
        console.warn("warning! negative age");
    }
})
```

### ts 内置的字符串类型工具

1. 用来将每一个字符转换为大写形式

```ts
Uppercase<StringType>
```

```ts
type Greeting = "Hello, world"
type ShoutyGreeting = Uppercase<Greeting>
```

此时`ShoutyGreeting`的类型为`HELLO WORLD`

2. 用来将每一个字符转换为小写形式
```ts
Lowercase<StringType>
```

3. 用来将第一个字符转换为大写形式

```ts
Capitalize<StringType>
```


