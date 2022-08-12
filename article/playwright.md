playwright 配置文件说明：

```ts
import type { PlaywrightTestConfig } from '@playwright/test'

const config: PlaywrightTestConfig = {
  use: {
    headless: false, // 关闭无头浏览器模式
  }
}

export default config
```

打开网站自动记录当前登录的账号 cookie：
```
npx playwright cr https://XXX.top --save-storage cway
```

自动加载之前记录的账号 cookie：
```
npx playwright cr https://XXX.top --load-storage cway
```

发起 fetch 请求：

```ts
const cookieString = ''

test('test', async ({ browser, page }) => {
  const context = await request.newContext({
    baseURL: 'http://hdj.jd.com/',
  })

  const userInfo = await context.get('/api/xxx', {
    headers: {
      cookie: cookieString,
    },
  })
  const json = await userInfo.text()
  console.log(json)
})

```

