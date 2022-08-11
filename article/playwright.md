```ts
import type { PlaywrightTestConfig } from '@playwright/test'

const config: PlaywrightTestConfig = {
  use: {
    headless: false, // 关闭无头浏览器模式
  }
}

export default config
```
