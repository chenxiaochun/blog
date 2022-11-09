
服务器发送事件（server-sent events）

一般情况下都是页面发送请求给服务器，然后接收返回的新数据。那有了『服务器发送事件』机制之后，服务器就可以在任意时间给页面主动的推送数据了

EventSource 正是服务器发送事件机制的一种实现

每个连接到服务器的 EventSource 实例，打开的都是一个持久化连接。然后会以`text/event-stream`的格式给客户端发送数据。之后此长连接会一直保持打开状态，直到主动使用`EventSource.close()`将其关闭

不同于 WebSockets，服务器发送事件只能是单向的数据发送。也就是，只能将数据从服务器侧发送给客户端。这个机制特别适合于以下场景：

1. 比如更新社交账号的状态
2. 比如更新当前用户的信息流

### EventSource 实例属性

`EventSource.readyState`

只读属性，表示当前连接状。它有三个值：

* `0`：表示正在连接中
* `1`：表示已经打开连接
* `2`：表示已经关闭连接

`EventSource.url`

只读属性，表示要连接的源 url

`EventSource.withCredentials`

一个布尔类型的只读属性。在跨域请求需要验证时，会被设置为`true`，否则为`false`

### EventSource 事件

`error`

连接失败时会被触发

`message`

当从事件源上接收到数据时会被触发

`open`

当连接到事件源上时会被触发

### 示例代码

```js
const evtSource = new EventSource('sse.php');
const eventList = document.querySelector('ul');

evtSource.onmessage = (e) => {
  const newElement = document.createElement("li");

  newElement.textContent = `message: ${e.data}`;
  eventList.appendChild(newElement);
}
```




