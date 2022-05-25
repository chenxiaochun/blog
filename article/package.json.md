### `exports`

如果在`package.json`中定义了`exports`字段，那么它的优先级会高于`main`、`file`、`module`、`browser`

```json
{
  "name": "test",
  "exports": {
    ".": "./index.js",
    "./foo": "./foo.js"
  }
}
```

```js
import { foo } from "test"; // 从 test/index.js 中引入 foo
```

```js
const { foo } = require("test/foo"); // 从 test/foo.js 中引入 foo
```

还可以根据不同的引用方式来指定不同的引用入口文件

```json
{ 
  "name":"test",
  "exports": {
    "import": "./foo.mjs",
    "require": "./bar.cjs"
  },
  "type": "module"
}
```

```js
import t from 'test' // 引入的是 foo.mjs
```

```js
const t = require('test') // 引入的是 bar.cjs
```

### `peerDependencies`

假设`lib-a`和`lib-b`两个组件都依赖了`react`和`react-dom`。此时，如果我们在`lib-a`和`lib-b`中定义了`peerDependencies`，那么就不会重复下载`react`和`react-dom`依赖了

```
node_modules
  ├── lib-a
  ├── lib-b
```

```json
{
  "name": "lib-a",
  "peerDependencies": {
    "react": "^16.3.0",
    "react-dom": "^16.3.0"
  }
}
```

### `peerDependenciesMeta`

一般情况下，在安装一个组件时，如果`peerDependencies`中定义的依赖没有被安装，就会收到警告信息。那`peerDependenciesMeta`就是用来修饰`peerDependencies`的，比如可以将某些依赖指定为可选的

```json
{
  "name": "tea-latte",
  "version": "1.3.5",
  "peerDependencies": {
    "tea": "2.x",
    "soy-milk": "1.2"
  },
  "peerDependenciesMeta": {
    "soy-milk": {
      "optional": true
    }
  }
}
```

此时，即使没有安装`soy-milk`，也不会收到警告信息。但你要在本地自行处理对此包的替代逻辑，以免代码逻辑报错