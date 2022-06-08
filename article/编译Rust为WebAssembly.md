新建一个工程，别忘了添加`--lib`参数。这样生成的`src`目录下会有一个`lib.rs`文件，而不是`main.rs`文件。如果忘了添加此参数，直接将`main.rs`改为`lib.rs`也是可以的

```
cargo new --lib hello_wasm
```

打开`src/lib.rs`添加以下代码：

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    pub fn alert(s: &str);
}

#[wasm_bindgen]
pub fn greet(name: &str) {
    alert(&format!("Hello, {}!", name));
}
```

简单解释一下其中的代码释义：

* `wasm_bindgen::prelude`将会引入其全部模块。类似于 js 中的`import * as module from './module'`
* `#[]`语法，在 rust 中被称为『属性』，它会以某种方式改变下面的那条语句的行为，此处为`extern`
* `extern`用来告诉 rust，下面调用的函数是需要从外部获取的，而且是要从`wasm_bindgen`那里去获取。`alert`接受一个引用类型的参数，具体为什么是这种类型。需要去了解 rust 的
所有权和借用机制
* 接着下面的`#[wasm_bindgen]`用来修饰`fn`语句，前面添加`pub`将此函数暴露出去，用来被外部调用。它的含义正好和`extern`是相反的。`greet`函数调用了上面的`alert`函数，并且给它传递一个经过`format!`宏处理过的参数
* `format!`宏接受两个参数，其实就是将后面的`name`参数值塞到了`{}`处

### 编译 Rust 为 WebAssembly

安装`wasm-pack`打包工具：

```
cargo install wasm-pack
```

在项目的`Cargo.toml`文件中添加以下配置：

```toml
[package]
name = "hello_wasm"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
wasm-bindgen = "0.2"
```

执行打包编译命令：

```
wasm-pack build --target web
```

编译完成之后，项目根目录中会多出一个`pkg`文件夹。这里面就是最终产出的 webAssembly js 文件了。可以看到，不仅有可执行的 js 文件，还有相应的类型定义文件

```
pkg
├── hello_wasm.d.ts
├── hello_wasm.js
├── hello_wasm_bg.wasm
├── hello_wasm_bg.wasm.d.ts
└── package.json
```

### 在 web 中使用它

新建一个 html 文件，添加以下代码：

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>hello-wasm example</title>
</head>

<body>
  <script type="module">
    import init, {greet} from './pkg/hello_wasm.js'

    init().then(() => {
      greet('cxc');
    }); 
  </script>
</body>

</html>
```

需要在本地开启一个静态服务器环境，可以使用：`npx http-server -c-1`。如果一切没问题，就可以看到一个 alert 弹窗了

### 在 rust 中调用`console.log`

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = console)]
    fn log(s: &str);

    #[wasm_bindgen(js_namespace = console, js_name = log)]
    fn log_u32(a: u32);

    #[wasm_bindgen(js_namespace = console, js_name = log)]
    fn log_many(a: &str, b: &str);
}

#[wasm_bindgen]
pub fn greet(name: &str) {
    log(name);
}
```

执行`wasm-pack build --target web`编译完成之后，在 web 中进行调用：

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>hello-wasm example</title>
</head>

<body>
  <script type="module">
    import init, { greet } from './pkg/hello_wasm.js'

    init().then(() => {
      greet('Hello Wasm');
    });
  </script>
</body>
</html>
```

### 再看另外一个加和求值的示例

rust 求和代码：

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn sum(value: i32) -> i32 {
    let mut s = 0;
    for i in 0..=value {
        s += i;
    }
    s
}
```

在 html 中写了两种计算方式。一种是调用 webAssembly 进行求和，另外一种是直接使用 js 进行求和。然后分别输出它们的计算结果和消耗时间来进行对比：

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>hello-wasm example</title>
</head>

<body>
  <script>
    const target = 10000
  </script>
  <script type="module">
    import init, { sum } from './pkg/hello_wasm.js'

    init().then(() => {
      console.time('rust timer')
      console.log('rust result is: ', sum(target))
      console.timeEnd('rust timer')
    });
  </script>

  <script>
    function sum(value) {
      let sum = 0
      for (let i = 0; i <= value; i++) {
        sum += i
      }
      return sum
    }

    console.time('js timer')
    console.log('js result is: ', sum(target))
    console.timeEnd('js timer')
  </script>
</body>

</html>
```

从 0 加到 10000，从输出时间可以看到 rust 的计算时间比 js 快了三倍左右：

```
js result is:  50005000
js timer: 0.31396484375 ms

rust result is:  50005000
rust timer: 0.118896484375 ms
```

原文链接：https://developer.mozilla.org/en-US/docs/WebAssembly/Rust_to_wasm







