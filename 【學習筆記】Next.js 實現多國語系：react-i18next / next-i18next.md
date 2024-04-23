###### `Front-End` `React` `Next.js` `Router`
# 【學習筆記】Next.js 實現多國語系：react-i18next & next-i18next

[TOC]

![i18n](https://hackmd.io/_uploads/ry0hNlrZA.png)

## 前言

在開發網站時，多國語系功能（i18n）是很常見的需求，能根據使用者需求切換網站顯示的語言。這陣子在 Next.js 專案中意外踩了幾個坑，寫下學習筆記作為紀錄。

本篇分為以下幾個段落：

+ What is i18n？
+ 如何實作
    + Next.js 內建：路由層級
    + 選擇套件：react-i18next & next-i18next
+ 使用範例
    + SSG/SSR：使用 next-i18next
    + SPA：使用 react-i18next

## What is i18n？

i18n 是由  internationalization（國際化）英文縮寫而來，18 代表 i 到 n 之間的字母數量。

透過 i18n 多國語系功能，能夠讓不同語系的使用者，根據需求選定顯示語言和格式，減少在地化的時間成本，達到國際化的目的。

## 如何實作

### Next.js 內建：路由層級

在介紹套件之前，先介紹 Next.js 官方[內建 i18n 功能](https://nextjs.org/docs/pages/building-your-application/routing/internationalization)，透過路由層級（routing）設定不同語言轉至不同路徑，設定範例如下：

```js=
// next.config.js
module.exports = {
  i18n: {
    locales: ['en', 'zh', 'jp'],
    defaultLocale: 'en',
  },
}
```

根據上述配置，即可搭配 `next/link`、`next/router`，根據路由顯示對應的語言，路徑如下：

* `/posts`：預設為 `en`
* `/zh/blog`
* `/jp/blog`

其中需注意 [Page Router](https://nextjs.org/docs/pages/building-your-application/routing/internationalization) 和 [App Router](https://nextjs.org/docs/app/building-your-application/routing/internationalization) 在路由設定上有所不同，詳細可參考官方提供的範例，以下介紹常見的 i18n 套件作為範例。

> 關於 Page router 和 App router 的差別，可參考之前的筆記：[【學習筆記】Next.js 路由系統：App Router vs Page Router](https://heidiliu2020.github.io/nextjs-app-router/)

### 選擇套件：react-i18next & next-i18next

+ [i18next/next-i18next](https://github.com/i18next/next-i18next)
    + 支援 Page Router 
    + 支援 SSG/SSR
+ [i18next/react-i18next](https://github.com/i18next/react-i18next)
    + 支援 APP Router，因此 Next.js v13 後的版本建議搭配使用
    + 支援 SPA

可參考上述兩種套件的 npm 下載數：

![next-i18n](https://hackmd.io/_uploads/HyMMe1Sb0.png)


## 使用範例

### SSR：使用 [next-i18next](https://github.com/i18next/next-i18next)

(1) 首先是安裝套件：

```
npm install next-i18next --save
```

(2) 在根目錄新增 `next-i18next.config.js` 設定檔：

```typescript=
// next-i18next.config.js

module.exports = {
  i18n: {
    locales: ['en', 'jp', 'zh'],
    defaultLocale: 'en',
  },
  fallbackLng: {
    default: ['en'],
  },
};
```

(3) 在 `next.config.js` 設定檔引入使用 `next-i18next`：

```typescript=
const { i18n } = require('./next-i18next.config')

module.exports = {
  i18n,
}
```

(4) 修改 `src/pages/_app.tsx` 檔案，以 `appWithTranslation` 這個 [HOC（高階組件）](https://legacy.reactjs.org/docs/higher-order-components.html)包住整個 App：

```typescript=
import { appWithTranslation } from 'next-i18next'

const MyApp = ({ Component, pageProps }) => (
  <Component {...pageProps} />
)

export default appWithTranslation(MyApp)
```

#### 【補充】Higher-Order Components（HOC，高階組件）

+ HOC 並不是 React 提供的 API，而是和 JavsScript 中的 [Higher Order Function（高階函式）](https://zh.wikipedia.org/zh-tw/%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0)類似的一個函式，高階函式可代入另一個函式作為參數，最終回傳一個函式作為結果
+ 而 HOC 則是可代入元件（Component）作為參數，並回傳一個新的元件
+ 目的是將共用邏輯放在 HOC 中，變動的部分由 Component 的 props 和 state 傳入

(5) 在 `public/locales/<locale>/<namespace>.json` 路徑加入多國語系檔案，架構參考如下：

```
.
└── public
    └── locales
        ├── en
        |   ├── posts.json
        |   └── common.json
        ├── jp
        |   ├── posts.json
        |   └── common.json
        └── zh
            ├── posts.json
            └── common.json
```

(6) 在頁面引入語系檔案：

```typescript=
import { serverSideTranslations } from 'next-i18next/serverSideTranslations'

export async function getStaticProps({ locale }) {
  return {
    props: {
      ...(await serverSideTranslations(locale, [
        'common',
        'posts',
      ])),
      // Will be passed to the page component as props
    },
  }
}
```

(7) 即可在頁面引入 `useTranslation` hook 使用，注意需從 `next-i18next` 引用：

```typescript=
import { useTranslation } from 'next-i18next'

export const Header = () => {
  const { t } = useTranslation('common')

  return (
    <div>
      <h1>{t('home-title')}</h1>
    </div>
  )
}
```

### SPA：使用 [react-i18next](https://github.com/i18next/react-i18next)

非 SSR、SSG 環境，只需直接引用 `react-i18next` 套件即可。

(1) 首先是安裝套件：

```
npm install react-i18next i18next --save
```

(2) 建立 `app/i18n` 目錄，放置管理 i18n 的相關檔案（初始 i18n、翻譯文檔），範例如下：

+ `i18n/index.ts`：初始 i18n 相關設定

```typescript=
// i18n/index.ts

import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import resources from './lang-resource';

i18n
  .use(initReactI18next)  // 初始化設定
  .init({
    resources,            // 引入定義語系與對應文字的 json 檔
    lng: 'en',            // 預設語系為 en
    fallbackLng: 'en',    // 若找不到對應語系則回傳 en
    defaultNS: 'common',
    preload: ['en', 'ja', 'zh'],
    ns: 'common',
    interpolation: {
      escapeValue: false,
    },
    parseMissingKeyHandler: () => {
      return '';
    } ,
    react: {
      useSuspense: false
    },
  });

export default i18n;
```

+ `i18n/lang-resource`：定義語系與對應文字的 json 檔

```jsonld=
// i18n/lang-resource

const resources = {
  en: {
    common: {
        'login': 'Login',
        'logout': 'Log out',
      },
  },
  ja: {
    common: {
        'login': 'ログイン',
        'logout': 'ログアウト', 
    },
  },
  zh: {
    common: {
        'login': '登入',
        'logout': '登出', 
    },
  },
};

export default resources;
```

(3) 設定完成後，即可在 `app/pages/layout` 引入 `i18n/index.ts` 使用：

```typescript=
// app/pages/layout

import './i18n'

export default function RootLayout() {
    // ...
}
```

以 `app/pages/login/page.tsx` 為例：

```typescript=
// app/pages/login/page.tsx

import { useTranslation } from 'react-i18next';

export default function Login() {
      const { t } = useTranslation();
    
        return (
        <div>
            <p>{t('login')}</p>
            <p>{t('logout')}</p>
        <div/>
    )    
}
```

(4) 若要切換語系，可使用 `i18n.changeLanguage` 方法：

```typescript=
// app/component/header.tsx

import { useTranslation } from 'react-i18next';

export function Header() {
  const { t, i18n } = useTranslation();
    
  const handleChangeLanguage = () => {
      i18n.changeLanguage('en')
  }
}
```

## 結語

其實一開始研究實作 i18n 功能時，看到官方文件整個頭昏眼花，Next.js 內建的路由實作上又稍嫌複雜，React 生態不像 Angular 框架能直接引入內建功能實作，反而有很多種套件能夠選擇使用。

但問題來了，套件引入使用下來卻噴一堆 Error，才發現到由於 Next.js 路由系統架構，需搭配支援 App Router 或 Page Router 的套件使用，這部分覺得官方文件說明稍嫌複雜，其實想成是在實作 React 多國語系功能就單純許多；雖然文中提及的 Page Router 還沒有機會實作，概念上還有點模糊，但還是先寫下筆記留作紀錄。

## 參考資料

+ [一篇文章了解如何在 Next.js 中集成 i18n 国际化（含踩坑及开发配置）](https://juejin.cn/post/7238619890993758263)
+ [【Next】i18n使用 - JohnShu Blog](https://jiangshuuu.com/docs/React/next-i18n/)
+ [【DAY20】React Native - 多語系切換 (react-i18next)](https://ithelp.ithome.com.tw/articles/10323017)
+ [使用 next-i18next 實作中英文多語系 - Modern Next.js Blog 系列 #28](https://ithelp.ithome.com.tw/articles/10308315)
