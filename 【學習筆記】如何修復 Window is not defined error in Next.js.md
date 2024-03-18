[![hackmd-github-sync-badge](https://hackmd.io/fufpeNLzSu-Wgut0fBOg2w/badge)](https://hackmd.io/fufpeNLzSu-Wgut0fBOg2w)
###### `Front-End` `React` `Next.js` 
# 【學習筆記】如何修復 "Window is not defined" error in Next.js

[toc]

## 前言

事情發生在開發 Next.js APP，要實作 Google 登入驗證，需要使用 [Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)、[Document](https://developer.mozilla.org/en-US/docs/Web/API/Document) 物件時，發現程式會報出以下錯誤：

```=
ReferenceError: window is not defined

or

ReferenceError: document is not defined
```

## 發生原因

這是由於 Next.js 預設為伺服器渲染（Server-side Rendering），會在 Node.js 環境下預渲染頁面，並將生成的 HTML 內容發送給 Client 端。

因此渲染過程是在 Server 端而非在瀏覽器中，由於程式無法識別 Window / Document 物件而回報上述 Error。

## 如何解決？

解決方法可透過條件渲染（Conditional Rendering），確保 Next.js 只在 Client 端執行指定的程式碼，

可透過以下兩種方式來實現：

+ 檢查 window 是否存在
+ 使用 useEffect hook

### (1) 檢查 window 是否存在

判斷 window 是否存在，確定在瀏覽器中才執行指定的程式碼：

```typescript=
const isBrowser = () => typeof window !== 'undefined';

if (typeof window !== 'undefined') {
    // Client-side-only
    console.log('window: ', window);
};
```


### (2) 使用 useEffect 等 Hooks

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


## 參考資料

+ [Window is not defined in Next.js React app](https://stackoverflow.com/questions/55151041/window-is-not-defined-in-next-js-react-app)
+ [如何修复Next.js中的 "window is not defined"？ - 掘金](https://juejin.cn/s/react%20window%20is%20not%20defined%20next%20js)