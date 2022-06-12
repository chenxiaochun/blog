### vscode 环境配置

安装插件：

[rust-analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer)，这个不是官方插件，据说官方的问题太多，所以基本都在推荐此插件

保存代码自动格式化：

```json
{
  "[rust]": {
    "editor.defaultFormatter": "matklad.rust-analyzer",
    "editor.formatOnSave": true
  }
}
```

### 监听本地文件变化，并自动编译运行

```
cargo install cargo-watch
```

```
cargo watch -x run
```

### `dbg!`函数

通过它打印内容，输出的内容会带文件名、行号等信息，方便进行程序调试。建议需要打印日志时，可以使用`dbg!`来代替`println!`

```rust
let log = "我是一条日志";
dbg!(log);
```

```
[src/main.rs:8] log = "我是一条日志"
```

### Rust GC 机制

对于 Rust 而言，安全和性能是写到骨子里的核心特性。如果使用 GC，就会牺牲性能；如果使用手动管理内存，就会牺牲安全，这该怎么办？Rust 的机制是：变量在离开作用域后，就会自动释放其占用的内存

```rust
{
  let s = String::from("hello"); // 从此处起，s 是有效的

  // 使用 s
} // 此作用域已结束，
  // s 不再有效，内存被释放
```

### 引入其它文件模块

1. 被引入的文件与`main.rs`处于同一层级目录中

```
src/
├── main.rs
└── say.rs
```

`say.rs`的内容：

```rust
mod say {
  pub fn hello () {
    println!("hello");
  }
}
```

`main.rs`的内容：

```rust
mod say;

fn main() {
    say::say::hello();
}
```

如果使用了`use say::say:hello;`，则可直接调用`hello();`

### 数组

**数组类型容易跟数组切片混淆**，`[T;n]`描述了一个数组的类型，而`[T]`描述了切片的类型。切片是运行期的数据结构，在编译期无法得知，因此不能用`[T;n]`去进行描述

在实际开发中，使用最多的是数组切片，通常使用`&[T]`的方式进行引用，因为它有固定的类型大小

### 学习资料

* Rust and WebAssembly：https://rustwasm.github.io/
* Rust圣经：https://course.rs
* Writing an OS in Rust：https://os.phil-opp.com/?continueFlag=337dcf70445017414c8f184d55fb43c4
* 微软 Rust 基础教程（非常基础）：https://docs.microsoft.com/zh-cn/learn/paths/rust-first-steps/
* 字节提供的 Rust 包下载镜像：https://rsproxy.cn/


