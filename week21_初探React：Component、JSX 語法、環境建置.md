###### tags: `React` `JSX` `props`
# [week 21] 初探 React：Component、JSX 語法、props 與 state 的不同

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 我知道 React 的目的以及原理
 P1 我知道我們為什麼需要 React
 P1 我知道使用 React 跟之前使用 jQuery 的區別
 P1 我理解 state 跟 props 的不同
```

---

## 前言

### React 版本差異

關於 React 發展歷史，其實起源於 2013 年由 Facebook 開源出來的一個 Library，直到 2015 年更新到 v15 版本後又為更多人使用。

而 React 16.8.0 則是第一個支援 Hook 的版本，這部分我們之後會再提到。到 2020 年 10 月出現 React v17，課程將以此版本為主，和 v16 基本上沒有太大的差異。

### React 中一定會用到的 ES6 語法

在開始之前，可以先複習這篇：[React 中一定會用到的 JavaScript 語法](https://ithelp.ithome.com.tw/articles/10217085)，主要包括下列幾個重點：

- 樣板字面值（Template literals／Template strings）
- 箭頭函式（arrow functions）
- 物件屬性名稱縮寫（Shorthand property names）
- 解構賦值（Destructuring assignment）
- 展開語法（spread syntax）
- 其餘語法（rest syntax）

可事先具備這些知識，或到時候再搭配 MDN 查詢用法，因為這些是之後實作 React 時一定會碰到的語法。

---

## React：JavaScript 函式庫

我們經常會聽到前端三大框架：React、Vue、Angular，但根據 [React](https://zh-hant.reactjs.org/) 官網定義：

> React: A JavaScript library for building user interfaces

嚴格來說，React 其實不算是一個框架，而是一個 JavaScript Libaray。通常使用 React 時會搭配其他 Libaray 使用，整個生態系結合起來就會和框架差不多。

### 為什麼我們需要 React？

我們可以從「React 提供了哪些功能」的角度來思考這個問題。

之所以會有 React 的誕生，可從原有的 JavaScript MVC 架構談起：Facebook 認為 MVC 模式已無法滿足擴展需求，隨著應用規模增加，系統的複雜度會成級數成長，增加開發難度以及不易進行後續維護。

因此開源出 React 這個 JavaScript Library，負責處理 MVC 的 View（介面）部分，解決思路就是「當狀態改變時，直接重新渲染畫面」，引入 Virtual DOM 的概念，透過 DOM Diff 演算法算出實際需要更新的部分，有效減少渲染次數以提高效能。

瞭解到 React 的發展過程之後，再回到「為什麼我們需要 React？」這個問題，React 的核心概念如下：

- Component 元件化
  - 開發 React 很重要的一點，就是去思考在頁面有哪些重複性高或相似的 Element，再透過 JSX 語法將這些 Element 建立成一個 Component
  - 讓每個 Components 擁有重複性及可擴充性，以模組化的方式進行開發
- JSX 語法
  - 透過 JSX 語法，即可將 HTML 語法轉成 JavaScript 的形式，讓我們用來建立 React elements
- Virtual DOM
  - 因為操作 DOM 這件事，其實會耗費很大的成本；而 React 背後運行機制，就是透過比對 Virtual DOM 來避免直接操作 DOM，藉此來提升效能
  - 此外，因為建立 Virtual DOM 這一虛擬層，也讓我們能夠對程式碼進行更多操作
- Hooks API
  - hook 其實就是 function，讓我們可以在 function component 中管理狀態和使用生命周期等功能，藉此簡化程式碼與提高重用性

### 可以不用 React 嗎？

由於 React 中，強調「模組化」的概念，如果是實作一個靜態網頁，或是專案規模較小、沒有模組化需求時，直接使用 HTML、CSS、JavaScript 等進行開發會更方便省時。

因此，並不是所有專案都適合用 React 進行開發，應該根據網站需求去決定合適的工具。

---

## 初探 React

在開始前可先透過 [CodeSandbox](https://codesandbox.io/) 進行練習，這是一個支援前端框架開發線上編輯器（IDE），類似複雜版的 Codepen。

點選 Create Sandbox，並選擇 React 來快速建立開發環境：

![](https://i.imgur.com/j6UFABA.png)

建立完成的初始畫面如下，藉由像這樣快速建立環境，也能用來幫助線上 debug：

![](https://i.imgur.com/d5LQgbX.png)

### JSX：用來建立 React 元素

> 簡單來說，JSX（JavaScript extension syntax）就是 HTML/XML + JavaScript。

React 提供的 JSX 語法，是透過底層的 Babel 機制，將 HTML 語法轉成 JavaScript Function 的形式，讓我們能用來建立 React elements：

```javascript=
const rootElement = document.getElementById("root");
ReactDOM.render(<h2>app</h2>, rootElement
);
```

以 mount Hello 這個 component 為例：

> mount：意思是把 component 放到畫面上

```javascript=
function Hello() {
  // 在 Hello() 中 return 什麼，就會在畫面 render 出什麼
  return <h1>hello world!</h1>
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Hello />, rootElement
);
```

![](https://i.imgur.com/CapcKxx.png)


Hello() 這個 component 可以接收參數 props，寫在大括號中會被解讀 React 為 JavaScript 程式碼執行，例如 `{JS code}`：

```javascript=
function Hello(props) {
  return <h1>Hello, {props.name}!</h1>
}

const rootElement = document.getElementById("root");
ReactDOM.render(<Hello name="Heidi"/>, rootElement
);
```

#### ES6 的解構寫法

更常見的寫法，是把 props 參數改成 ES6 的解構寫法，也就是在大括號內傳入參數：

```javascript=
function Hello({name}) {
  return <h1>Hello, {name}!</h1>
}
```

### 使用 JSX 語法需注意的地方

JSX 語法和 template engine 不同，在使用上沒有那麼自由，必須遵守幾點規則，以避免寫出不合法的 JSX ：

- 沒有迴圈的概念
- 沒有 if-else 判斷式

那麼該如何解決這種情況呢？以下有兩種解決方式，以及範例程式碼：

- 透過三元運算子來進行判斷：適用於只有 ture/false 的情況

```javascript=
{todo.isDone ? '已完成' : '未完成'}
```

- 透過邏輯運算子 && 的短路行為：適用於多種可能的情況

```javascript=
{todo.isDone && '已完成'}
{!todo.isDone && '未完成'}
```

### JSX 特性：自動 escape

此外，根據[官方文件](https://reactjs.org/docs/introducing-jsx.html#jsx-prevents-injection-attacks)，JSX 語法能夠預防 Injection Attacks，提供 escape 功能。

![](https://i.imgur.com/eRabFE4.png)

如果真的想要 render 出 innerHTML，則需透過 [dangerouslysetinnerhtml](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml) 這個冗長的標籤，一般而言不會使用這個方法。

但需要注意，如果在 a 連結標間中，有使用者輸入的區塊，例如：

```javascript=
<a href={todo.content}>click me!</a>
```

這時若被惡意輸入 `javascript:alert()` 程式碼，點擊 a 連結就會執行該 JS 程式碼。這其實因為 React 沒有跳脫冒號，造成的 click based XSS：

![](https://i.imgur.com/bnbqnMb.png)

防範方式有兩種：
- 不要在 a 標籤內放入使用者輸入
- 加上 encodeURIComponent() 語法，把字串轉化成 escape 格式的字符串

```javascript=
 <a href={window.encodeURIComponent(todo.content)}>click me!</a>
```

### 常見的 component：Counter

最後再以常見的 Counter component 為例：

```javascript=
function Counter() {
  // useState() 會建立一個陣列 state, 傳入參數為初始值
  const [value, setValue] = React.useState(1);
  function handleClick() {
    // 透過呼叫 setValue() 來更動 state
    setValue(value + 1);
  }
  // 建立 onClick 監聽事件
  return <button onClick={handleClick}>{value}</button>;
}

const rootElement = document.getElementById("root");
ReactDOM.render(
<Counter />, rootElement
);
```

透過點擊 button 觸發事件來改變 state，React 再根據 state 內容來 render 出畫面：

<iframe src="https://codesandbox.io/embed/chutan-react-n4z9g?fontsize=14&hidenavigation=1&theme=dark"
     style="width:80%; height:200px; border:0; border-radius: 4px; overflow:hidden;"
     title="初探 React"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

---

## React 的思考模式跟以前的思考模式有什麼不一樣？

React 最大的不同，在於多了 Component 和 State 這兩個概念。

### Component 元件

這和以往的思考模式其實很不一樣，像是在切好的 UI 畫面上，將每個 element 新增各種功能；或是以 MVC 架構進行開發。

而在 React 中，一個元件（component）是 React 的最小單位，再透過 Props 來設定屬性或資料。由於所有東西或介面都是由元件所組成，強調的是 UI 元件的封裝性、共用性及擴展性。

### React State

在 React 是「透過資料的狀態，決定是否重新渲染畫面」，這和以往直接更改操作 DOM 元素不同；而是以類似間接的方式，透過 React 演算法比對 Virtual DOM 來決定是否更新真實 DOM。

也就是說，Component 是透過資料狀態，來決定是否更新 UI 畫面，而 Component 中有兩種資料來源：

1. 外部傳進 Component 的 Props
2. Component 內部的 State

每當 React 偵測到 Props 或 State 有改變時，就會自動重新渲染。

剛開始對這種以 Component 和 State 為核心概念的寫法很不習慣，但卻也有點熟悉的感覺，就像以前在實作前後端分離的留言板或 Todo List 時很像。可能也因為 function component，又和透過功能區分的模組化很類似，但需要考慮到該如何更改 State 來呈現畫面。

---

## State 跟 Props 的差別在哪裡？

State 和 Props 都是 JavaScript 物件，我們在前面有提到，當這兩者之一有改變時，會觸發 React 重新渲染畫面。兩者差別在於：

### State

- 在 Component 內部被管理，類似於 function 中的宣告變數
- 是 Component 本身的狀態，只有該 Component 能透過 setState 變更 state，這部分我們會在之後的 Hooks 詳細介紹

### Props

- 從外部傳進 Component，類似於 function 的參數
- 由於 React 單向資料流的特性，Props 是父層由上往下傳遞給子層
- Props 不能被接收的子層修改，但也可能是父層的 State，可透過 setState 進行變更，再把更新的 State 值做為新的 Props 傳遞給子層

### 包在標籤中間的 props：children

在 React component 中，包在標籤中間的東西，稱為 children，children 也是一個 props。

以 Todoitem 為例，這裡的 children 指的就是 `Watch a movie`：

```javascript=
function Todoitem({n, children}) {
  return <h1>Todo {n}: {children}</h1>
}

const rootElement = document.getElementById("root");
ReactDOM.render(
<Todoitem n={1}>
  Watch a movie
</Todoitem>, rootElement
);
```

![](https://i.imgur.com/gX8yUJ5.png)

---

## 環境建置：create-react-app

React 環境建置可分為兩種：

- 自己從頭開始做
  - 使用 React + Webpack 打包 + Bable 編譯 + Webpack Dev Server
- 使用現成的套件
  - create-react-app，由 React 官方提供

這裡會以現成的程式來做示範，詳細說明可參考 [facebook/create-react-app](https://github.com/facebook/create-react-app)。

### 安裝指令

依照下方指令安裝相關套件並啟動：

```
$ npx create-react-app my-app
$ cd my-app
$ npm start
```

成功運行後，就會在 localhost 開一個 server：

![](https://i.imgur.com/K7dEnLz.png)

在開始專案之前，首先要閱讀 `README.md` 的說明，還有查看 `package.json` 確認安裝了哪些套件等訊息。

再來是 src\index.js 檔案，和我們在 CodeSandbox 看到的內容非常類似：

```javascript=
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

- `React.StrictMode`：代表[嚴格模式](https://reactjs.org/docs/strict-mode.html#identifying-unsafe-lifecycles)中，會像 ESLint 進行檢查與警告，但有時為了偵測，可能會呼叫兩次，造成和想像中不同的結果，因此建議不加上
- `<App />`：render 出 App conponent，也就是 render 的畫面

可在 src\App.js 檔案，修改要 render 的畫面：

```javascript=
function App() {
  return (
    <div className="App">
      Hello World!
    </div>
  );
}

export default App;
```

重整頁面即可看到結果：

![](https://i.imgur.com/HPUh5ly.png)

## 結論

到這裡我們瞭解 React 重要的概念 Component，以及如何使用 JSX 語法來建立 React 元素。

此外，除了可以利用線上編輯器 CodeSandbox，也可以在本地端安裝官方提供的 create-react-app 套件，來快速建置 React 開發環境。