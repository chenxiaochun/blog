## hook 闭包问题

假如有这样一个需求，进入页面 3 秒之后，输出最新的 count：

```tsx
function Demo() {
  const [count, setCount] = React.useState(0)

  useEffect(() => {
    const timer = setTimeout(() => {
      console.log(count)
    }, 3000)
    return () => {
      clearTimeout(timer)
    }
  }, [])

  return <button onClick={() => setCount(c => c + 1)}>click</button>
}
```

你会发现输出的一直都是 0，解决它的唯一办法就是使用`useRef`：

```tsx
function Demo() {
  const count = React.useRef(0)

  useEffect(() => {
    const timer = setTimeout(() => {
      console.log(count.current)
    }, 3000)
    return () => {
      clearTimeout(timer)
    }
  }, [])

  return <button onClick={() => (count.current += 1)}>click</button>
}
```

🔔 凡是延迟调用的场景，一定会存在闭包问题。例如：`setTimeout`、`setInterval`、`Promise.then`、`useEffect`的卸载函数中

## hook 依赖问题

在下面示例中，实现的目的是当`props.count`、`count`发生变化时，就会调用`monitor`函数，并传入几个数据。你发现`eslint-plugin-react-hooks`会提示你应该将`text`和`a`也放到`useEffect`依赖中，因为在当前 hook 里使用了这两个变量

可是，如果将这两个变量也放到 hook 的依赖中，就又不符合需求实现了

🔔 因此，实现需求更重要。应该只将你想引起 hook 重新渲染的变量放到依赖中，而不是因为在 hook 中用到了此变量

```tsx
function Demo(props) {
  const [count, setCount] = useState(0);
  const [text, setText] = useState('');
  const [a, setA] = useState('');

  useEffect(() => {
    monitor(props.count, count, text, a);
  }, [props.count, count]);

  return (
    <div>
      <button
        onClick={() => setCount(c => c + 1)}
      >
        click
      </button>
      <input value={text} onChange={e => setText(e.target.value)} />
      <input value={a} onChange={e => setA(e.target.value)} />
    </div>
  )
}
```