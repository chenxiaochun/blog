### `useTransition`

它不会影响状态更新本身，而是影响由状态更新之后触发的密集型计算任务

```jsx
import { memo, useState, useTransition } from "react";
import "./App.css";

const Item = memo(({ item }) => {
  const startTime = performance.now();
  const endTime = startTime + 10;
  while (performance.now() < endTime) {
    // 空循环，模拟耗时操作
  }
  return <li key={item}>{item}</li>;
});

const Delay = memo(() => {
  const result = [];
  for (let i = 0; i < 100; i++) {
    result.push(<Item key={i} item={i} />);
  }
  return result;
});

function App() {
  const [count, setCount] = useState(0);
  const [isPending, startTransition] = useTransition();

  return (
    <>
      <div className="card">
        <button
          onClick={() => {
            startTransition(() => {
              setCount((count) => count + 1);
            });
          }}
        >
          count is {count}
        </button>
        <div>
          {isPending ? <div>Loading...</div> : count === 2 && <Delay />}
        </div>
      </div>
    </>
  );
}

export default App;
```

react19 中的`useTransition`支持异步函数，这些异步函数就被称为`Actions`

```jsx
import { memo, useState, useTransition } from "react";
import "./App.css";
import axios from "axios";

function updateName(name) {
  return axios({
    method: "post",
    url: "http://jsonplaceholder.typicode.com/posts",
    data: {
      title: name,
    },
  });
}

function App() {
  const [name, setName] = useState("");
  const [isPending, startTransition] = useTransition();

  return (
    <>
      <div className="card">
        <input
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
        <button
          onClick={() =>
            startTransition(async () => {
              const res = await updateName(name);
              setName(res.data.id);
            })
          }
          disabled={isPending}
        >
          Submit
        </button>
        <div>
          <h1>{name}</h1>
        </div>
      </div>
    </>
  );
}

export default App;
```

### `useActionState`的基本用法

```jsx
import {
  memo,
  useState,
  useTransition,
  useActionState,
  startTransition,
} from "react";
import "./App.css";
import axios from "axios";

function updateName(name) {
  return axios({
    method: "post",
    url: "http://jsonplaceholder.typicode.com/posts",
    data: {
      title: name,
    },
  });
}

function App() {
  const [name, setName] = useState("");
  const [state, formAction, isPending] = useActionState(
    async (preName, name) => {
      const res = await updateName(name);
      return res.data.id;
    },
    ""
  );

  return (
    <>
      <div className="card">
        <input
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
        <button
          onClick={() =>
            startTransition(async () => {
              formAction(name);
            })
          }
          disabled={isPending}
        >
          Submit
        </button>
        <div>
          <h1>{state}</h1>
        </div>
      </div>
    </>
  );
}

export default App;
```

### `useActionState`与 form 元素的结合使用

```jsx
import { useState, useOptimistic, startTransition } from "react";

function update(task) {
  return new Promise((resolve, reject) => {
    // resolve(task);
    if (Math.random() > 0.5) {
      resolve(task);
    } else {
      reject("error");
    }
  });
}

function App() {
  const [list, setList] = useState([]);
  const [optimisticList, addOptimisticList] = useOptimistic(
    list,
    (currentState, optimisticValue) => [...currentState, optimisticValue]
  );

  async function formAction(formData) {
    const task = formData.get("task");
    addOptimisticList(task);
    startTransition(async () => {
      const res = await update(task);
      setList((currentList) => [...currentList, res]);
    });
  }

  return (
    <>
      <form action={formAction}>
        <input name="task" />
        <button type="submit">Submit</button>
      </form>
      <div>
        {optimisticList.map((v, i) => (
          <div key={i}>{v}</div>
        ))}
      </div>
    </>
  );
}

export default App;
```
