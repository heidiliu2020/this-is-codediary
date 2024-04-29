[![hackmd-github-sync-badge](https://hackmd.io/fufpeNLzSu-Wgut0fBOg2w/badge)](https://hackmd.io/fufpeNLzSu-Wgut0fBOg2w)
###### `Front-End` `React` `Next.js` 
# 【學習筆記】Next.js 錯誤修復紀錄： 「Window is not defined」&「use client & missing generateStaticParams() error」

[toc]

output: 'export' with use client in dynamic routes

## 前言

這篇主要用來記錄 Next.js 開發過程採的坑，後續也會不定期更新：

+ 問題一：如何修復 `"Window is not defined"` error
+ 問題二：在 use client 頁面實作動態路由顯示 `missing generateStaticParams()` error

## 問題一：如何修復 "Window is not defined" error in Next.js

事情發生在開發 Next.js APP，要實作 Google 登入驗證，需要使用 [Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)、[Document](https://developer.mozilla.org/en-US/docs/Web/API/Document) 物件時，發現程式會報出以下錯誤：

```=
ReferenceError: window is not defined

or

ReferenceError: document is not defined
```

### 發生原因

這是由於 Next.js 預設為伺服器渲染（Server-side Rendering），會在 Node.js 環境下預渲染頁面，並將生成的 HTML 內容發送給 Client 端。

因此渲染過程是在 Server 端而非在瀏覽器中，由於程式無法識別 Window / Document 物件而回報上述 Error。

### 如何解決？

解決方法可透過條件渲染（Conditional Rendering），確保 Next.js 只在 Client 端執行指定的程式碼，

可透過以下兩種方式來實現：

+ 檢查 window 是否存在
+ 使用 useEffect hook

#### (1) 檢查 window 是否存在

判斷 window 是否存在，確定在瀏覽器中才執行指定的程式碼：

```typescript=
const isBrowser = () => typeof window !== 'undefined';

if (typeof window !== 'undefined') {
    // Client-side-only
    console.log('window: ', window);
};
```


#### (2) 使用 useEffect 等 Hooks

透過 useEffect 等方法，可確保程式碼只會在 Client 端執行：

```typescript=
'use client';
import React, { useEffect } from 'react';

// ...

useEffect(() => {
    // Client-side-only        
    console.log('window: ', window);
    
    window.addEventListener('scroll', (e) => {
    console.log('srcoll: ', e)
  })
},[])

```

### 參考資料

+ [Window is not defined in Next.js React app](https://stackoverflow.com/questions/55151041/window-is-not-defined-in-next-js-react-app)
+ [如何修复Next.js中的 "window is not defined"？ - 掘金](https://juejin.cn/s/react%20window%20is%20not%20defined%20next%20js)


## 問題二：在 use client 頁面實作動態路由顯示 `missing generateStaticParams()` error

事情發生在開發 Next.js APP，實作 dynamic routing 時（如：`server/[evo]/page.tsx`），會顯示以下錯誤：

![error](https://hackmd.io/_uploads/HJ8MieYbA.png)

上述錯誤訊息中的`"output: export"`，是 Next.js 提供支援 [Static Exports（靜態導出）](https://nextjs.org/docs/app/building-your-application/deploying/static-exports)，透過在設定檔 `next.config.js` 加上參數：

```javascript=
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export', // Outputs a Single-Page Application (SPA)

export default nextConfig
```

如此一來，即可在 `next build` 建置時實現 SPA （single-page application），將會在 `out` 資料夾底下生成靜態檔案，將每個路由分解為單獨的 HTML 檔案，避免在 Client 端載入不必要的 JS 程式碼，減少 bundle 大小以提高頁面效能。

### 發生原因

但在 Next.js 若想要實現 Dynamic Routes（動態路由），必須加上 `generateStaticParams()` 方法，這段 function 只能在 Server Component 執行，在 Client Component 並不支援。

[官方文件（Deploying: Static Exports | Next.js）](https://nextjs.org/docs/app/building-your-application/deploying/static-exports#unsupported-features) 也提到 App Routing 若想要 Dynamic Routes 必須搭配「只能在 SSR 運行的 `generateStaticParams()`」：

![unsupported](https://hackmd.io/_uploads/S1xrigt-A.png)

### 如何解決？

如果想要輸出 SPA，又希望能在 `'use client'` 情境中實現動態路由，則元件拆成兩個部分實作：

+ 在 Server Component 引入 `generateStaticParams()` 方法，以實現動態路由
+ 接著引入 Client Component，即可使用 Hooks

以下是範例程式碼，詳細可參考這篇文章[《usage of generateStaticParams with use client | by Vivi - Medium》](https://medium.com/@givvemeee/usage-of-generatestaticparams-with-use-client-a059c23f7316)

+ `server/[evo]/page.tsx`：在 Server Component 引入 `generateStaticParams()` 方法，以實現動態路由

```typescript=
// server/[evo]/page.tsx

import EvoPage, { Props } from ".";

export function generateStaticParams() {
  return [
    { evo: 'test' },
    { evo: 'stage' },
    { evo: 'public' }
  ];
}

export default function ServerEvoPage({ params }: Props) {
  console.log('[ServerEvoPage] params', params)
  return <EvoPage params={{ evo: params.evo }}/>;
}
```

+ `server/[evo]/index.tsx`：在上述 Server Component 引入 Client Component，即可使用 Hooks：

```typescript=
// server/[evo]/index.tsx

'use client';

export type Props = {
  params: { evo: string};
};

export default function EvoPage({ params }: Props) {
  // const params = usePathname();
  console.log('[EvoPage] params: ', params);
    
 return (
    <div>
        This is Client Component.
    </div>
  );
}
```


### 參考資料

+ [[NEXT-1049] use client with generateStaticParams will opt out of static generation #46735](https://github.com/vercel/next.js/issues/46735)
+ [[NEXT-1030] output: 'export' with use client in dynamic routes doesn't work #48022](https://github.com/vercel/next.js/issues/48022)
+ [App Router with output: export does not support useParams() on client #54393](https://github.com/vercel/next.js/issues/54393)
+ [NextJs-静态导出](https://www.yuansudong.net/document/NextJs/35.%E9%9D%99%E6%80%81%E5%AF%BC%E5%87%BA.html)
+ [Day 19 - Next.js 13 App Router 動態路由 Dynamic Routes & getStaticParams()](https://ithelp.ithome.com.tw/articles/10322261)
