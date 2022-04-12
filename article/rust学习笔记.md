监听本地文件变化，并自动编译运行：

```
cargo install cargo-watch
```

```
cargo watch -x run
```

`dbg!`函数：

通过它打印内容，输出的内容会带文件名、行号等信息，方便进行程序调试。建议需要打印日志时，可以使用`dbg!`来代替`println!`

```rust
let log = "我是一条日志";
dbg!(log);
```

```
[src/main.rs:8] log = "我是一条日志"
```
