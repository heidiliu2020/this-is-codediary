# 【學習筆記】談談 Next.js：基於 React 的 SSR 框架

![image](https://hackmd.io/_uploads/SkvRg5cd6.png)

[toc]

## 前言

在認識 Next.js 之前，建議先複習 React 的相關知識，可參考過去的[《[week 21] 初探 React：Component、JSX 語法、props 與 state 的不同》](https://heidiliu2020.github.io/react-jsx-props/)系列筆記。

距離上次碰 React 感覺已經是好久以前的事情了，趁著這次組內專案需求，預計導入 Next.js 框架技術，寫下這篇學習筆記做記錄。

## 初探 Next.js

### React vs Next.js：函式庫與框架

眾所周知，React 其實並不是框架（Framework），而是用來建構使用者介面的 JavaScript 函式庫（Library）。

而 Next.js 則是建立 React 之上的框架，除了客戶端渲染（Client-side Rendering），還支援兩種形式的預渲染：靜態網頁生成（Static Site Generation）和伺服器端渲染（Server-side Rendering）。

可參考官網的標語介紹：

+ [React](https://react.dev/)：用於打造使用者介面的函式庫（The **library** for web and native user interfaces）
    + 此外官方也提到：「建議使用者使用全端 React 框架，如 Next.js 或 [Remix](https://remix.run/)，以處理專案中的路由與資料處理等需求」
+ [Next.js](https://nextjs.org/)：用於網頁開發的 React 框架（The React **Framework** for the Web）

## 為何選擇 Next.js？

在使用任何新技術之前，都要先問為什麼會做出這個選擇，是否有解決實作上面臨的問題 ~~（畢竟面試也很常會被問到）~~，先瞭解前因後果，也有助於掌握各種工具的使用時機。

這裡我們可以從 React 提供的功能作為切入點，React 是由 [Facebook](https://github.com/facebook/react) 團隊所維護的 JavaScript 函式庫。

Angular、React、Vue，也就是俗稱的前端三大框架，常用於快速開發單頁式應用程式（Single Page Application，SPA），可達到前後端分離，以及與提升使用者體驗等目的，例如 Gmail、Facebook 和 Netflix 等平台。

然而，由於 SPA 是利用客戶端渲染
（CSR），由 JavaScript 動態產生內容，檢視原始碼會發現無法看到新增的內容，將導致 SEO（搜尋引擎最佳化）較差的問題；也因為初次載入頁面需下載大量的 JavaScript 檔案，使第一次渲染較為費時，反而降低使用者體驗。

那該怎麼辦呢？

只要第一個畫面改由伺服器端渲染（SSR），即可解決 SEO 問題，其他畫面仍維持使用者端渲染（CSR），減少伺服器運算負荷，如此不就能夠兩者兼得了嗎？

React 可透過「搭建後端 Server + 處理 Hydration + Webpack 打包配置」等設定來實現 SSR，但相對來說門檻較高，需耗費較多成本學習。

於是前端 SSR 框架就誕生了，如 React 的 [Next.js](https://nextjs.org/) 和 [Gatsby.js](https://www.gatsbyjs.com/)，以及 Vue 的 [Nuxt.js](https://nuxt.com/)。

【延伸閱讀】關於 MVC、SPA 到 SSR 詳細演進，可閱讀 Huli 寫的這兩篇文章：[《跟著小明一起搞懂技術名詞：MVC、SPA 與SSR》](https://life.huli.tw/2018/05/04/introduction-mvc-spa-and-ssr-545c941669e9/)以及[《從歷史的角度探討多種 SSR（Server-side rendering）》](https://blog.huli.tw/2023/11/27/server-side-rendering-ssr-and-isomorphic/)，前者明明是兩年前才讀過的文章，現在又有一層新的體悟。

### Next.js 特別在哪？

![image](https://hackmd.io/_uploads/r1anuqcu6.png)

由 [Vercel](https://github.com/vercel/next.js) 團隊創建的 Next.js，解決了上述幾點網頁開發遇到的問題，以下是官網提及有關 Next.js v14（發布於 2023 年 12 月）的幾項特點：

+ Data Fetching：可控制資料載入的時機點
+ CSS Support：內建支援 CSS、Sass 檔案，並支援 CSS 模組化
+ Route Handlers：基於檔案架構的路由系統，如 `page/home.tsx`
+ API Routes：支援 API 路由，易於建立與管理 API 端點
+ Pre-rendering：支援兩種形式的預渲染，分別是靜態網頁生成（SSG）和伺服器渲染（SSR）
+ Built-in Optimizations：針對圖片、字體、JavaScript 載入進行自動優化，包括延遲載入與緩存處理

綜合上述優點，Next.js 有助於優化網頁效能與 SEO，適合用於建立著陸頁面（Landing Page）或是產品展示頁面，但較不適合應用在經常變動的網站，避免伺服器負荷過大。

而 Next.js 實際使用案例可參考 [Showcase](https://nextjs.org/showcase) 頁面，如 Notion、Tik Tok 以及 Twitch 等網站。

## 比較：CSR vs SSR vs SSG vs ISR

![image](https://hackmd.io/_uploads/Sk0QYQzYa.png)
> Ref: [Next.js: The Ultimate Cheat Sheet To Page Rendering](https://dev.to/guydumais/next-js-the-ultimate-cheat-sheet-to-page-rendering-55eb)

最後針對 Next.js 支援的網頁渲染方式，以及如何在 Next.js 搭配使用，列點整理如下：

+ Client-side Rendering（CSR）：客戶端渲染
    + Client（瀏覽器）第一次發送 Request 給 Server => Server 回傳只有容器不含內容的 HTML 檔 => 再由瀏覽器執行 JavaScript 動態產生資料 => 最後將資料渲染到畫面上
    + 因此第一次渲染較為費時，而只有容器沒有內容的原始碼也將不利於 SEO
    + 執行函數：`useEffect()` 或使用由 Next.js 團隊開發的 [SWR](https://swr.vercel.app/) 開源
套件
+ Server-side Rendering（SSR）：伺服器端渲染
    + Server（伺服器）在每次收到 Request 時，會建立好完整的 HTML 內容並發送給 Client
    + 適用於需經常更新數據的頁面，缺點是可能導致伺服器負荷較大
    + 執行函數：`getServerSideProps()`，只會在 Server side 執行，可跳過呼叫 API 步驟直接到資料庫抓取資料
+ Static Site Generation（SSG）：靜態網站生成
    + 在網頁打包（built time）時，就由 Server 產生所有需要用到的內容，因此每次 Server 收到 Request 均回傳相同的 HTML 給 Client
    + 因 HTML 內容不變可搭配快取機制，適用於資料變動較小的頁面，無法動態更新
    + 執行函數：`getStaticProps()`
+ Incremental Site Rendering（ISR）：增量式網站渲染
    + 結合 SSG 和 SSR 的渲染方式，可設定條件保存上一次渲染結果，當靜態檔案過期，將觸發 Server 重新 build HTML 檔案以更新頁面
    + 執行函數: `getStaticProps()` 搭配 `revalidate` 屬性

也可參考 web.dev 這篇文章[《Rendering on the Web》](https://web.dev/articles/rendering-on-the-web?hl=zh-tw) 提供的表格整理：

![image](https://hackmd.io/_uploads/SyQLDXGKp.png)

## 結語

在實際瞭解 Next.js 這套框架之前，在社群或其他平台略有耳聞，透過這次學習，除了把 React 的基礎，也把過去常被問到的 MVC、SPA、CSR、SSR 等專有名詞再重新複習一遍。

不管選擇什麼技術或框架，重要的是為何要使用，又想解決什麼問題，不外乎結果都是為了提升使用者體驗，畢竟科技始終源自於人性。這篇只提及基本觀念，再來就是實際動手操作啦！

## 參考資料
+ [18. [FE] 為什麼網站要做成SPA？SSR 的優點是什麼？](https://ithelp.ithome.com.tw/articles/10224772)
+ [深入淺出，完整認識Next.js 13 - iT 邦幫忙](https://ithelp.ithome.com.tw/users/20161853/ironman/6122)
+ [快速了解Next.js中的網頁渲染技術重點: SSR、SSG、ISR與CSR](https://hackmd.io/@pericode/HyRJHvAsj)
