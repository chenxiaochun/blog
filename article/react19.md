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

### `useFormStatus`

### 使用`use`获取 Promise 返回值

```jsx
import { Suspense, useDeferredValue, useState, use } from "react";

function List({ query, fetchData }) {
  const data = use(fetchData);
  return (
    <div>
      {query}: {data}
    </div>
  );
}
function App() {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);

  const fetchData = new Promise((resolve) =>
    setTimeout(() => resolve(Math.random()), 1000)
  );

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <Suspense fallback={<div>Loading...</div>}>
        <List query={deferredQuery} fetchData={fetchData} />
      </Suspense>
    </div>
  );
}

export default App;
```

### 使用`use`引用 context

```jsx
import { use, createContext, useState } from "react";

const ThemeContext = createContext("light");

function Test() {
  const theme = use(ThemeContext);
  return <div>{theme}</div>;
}

function App() {
  const [value, setValue] = useState(ThemeContext.value);
  return (
    <ThemeContext value={value}>
      <Test />
      <button onClick={() => setValue(value === "light" ? "dark" : "light")}>
        Change Theme
      </button>
    </ThemeContext>
  );
}

export default App;
```

### `refs`支持清理函数

```jsx
import { useState } from "react";

function App() {
  const [visible, setVisible] = useState(false);
  const setRef = (ref) => {
    if (ref) {
      const handler = () => console.log("点击了按钮");
      ref.addEventListener("click", handler);
      console.log("添加事件监听");
      return () => {
        ref.removeEventListener("click", handler);
        console.log("移除事件监听");
      };
    }
  };

  return (
    <div>
      <button onClick={() => setVisible(!visible)}>展示按钮</button>
      {visible && <button ref={setRef}>按钮</button>}
    </div>
  );
}

export default App;
```

### 使用`use`和`useDeferredValue`处理异步调用

```jsx
import { Suspense, useDeferredValue, useState, use, memo } from "react";

const sleep = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms));
};

const random = async () => {
  await sleep(1000);
  return Math.random();
};

const cache = new Map();

function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData());
  }
  return cache.get(url);
}

async function getData() {
  return await random();
}

const List = memo(({ query }) => {
  const data = use(fetchData(query));

  if (!data) {
    return null;
  }
  return (
    <div>
      {query}: {data}
    </div>
  );
});

function App() {
  const [query, setQuery] = useState("11");
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <Suspense fallback={<div>Loading...</div>}>
        <div style={{ color: isStale ? "red" : "black" }}>
          <List query={deferredQuery} />
        </div>
      </Suspense>
    </div>
  );
}

export default App;
```
