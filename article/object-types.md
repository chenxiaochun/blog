在 ts 中拥有对象类型

下面的`person`可以是一个匿名的对象类型

```ts
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name;
}
```

可以是使用`interface`定义的对象类型：

```ts
interface Person {
  name: string;
  age: number;
}
 
function greet(person: Person) {
  return "Hello " + person.name;
}
```

可以是使用`type`定义的对象类型：

```ts
type Person = {
  name: string;
  age: number;
};
 
function greet(person: Person) {
  return "Hello " + person.name;
}
```

## 属性修改器

在对象类型中，使用`?`表示一个属性是可选的。