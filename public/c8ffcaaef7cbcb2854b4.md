---
title: Denoでdelay()を使って待機処理
tags:
  - JavaScript
  - Node.js
  - TypeScript
  - Deno
private: false
updated_at: '2022-10-29T23:03:30+09:00'
id: c8ffcaaef7cbcb2854b4
organization_url_name: null
slide: false
ignorePublish: false
---
Denoで待機処理するのん、どうするんかなと思って試してみた。
こんな感じ。
すごくシンプル。

```ts
import { delay } from "https://deno.land/std@0.161.0/async/mod.ts";

console.log(new Date());
await delay(1000);
console.log(new Date());
```

[delay | /async/mod.ts | std@0.161.0 | Deno](https://deno.land/std@0.161.0/async/mod.ts?s=delay)
[Deno 標準ライブラリ async | Octo's blog](https://www.ccbaxy.xyz/blog/2022/01/29/js37/)


ちなみに、Nodeではこんな感じ。
```ts
import { setTimeout } from 'node:timers/promises'
await setTimeout(1000);
```
Node v.15から、setTimeout()はPromise返してくれるようになったらしい。
[Timers | Node.js v19.0.0 Documentation](https://nodejs.org/api/timers.html#timerspromisessettimeoutdelay-value-options)
[setTimeoutをawait/asyncで書く方法](https://zenn.dev/yogarasu/articles/33e666cac2a646)

ちなみに、ちなみに従来的なやり方はこんな感じ。
```ts
await new Promise((resolve) => setTimeout(resolve, 1000));
```

[awaitできるsetTimeoutを1行で書く方法 - Qiita](https://qiita.com/suin/items/99aa8641d06b5f819656)
