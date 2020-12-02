###### tags: `React` `JSX` `props`
# [week 21] 初探 React：Component、JSX 語法、環境建置

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

關於 React 發展歷史，其實起源於 2013 年由 Facebook 開源出來的一個 Library，直到 2015 年更新到 v15 版本後又為更多人使用。

而 React 16.8.0 則是第一個支援 Hook 的版本，這部分我們之後會再提到。到 2020 年 10 月出現 React v17，課程將以此版本為主，和 v16 基本上沒有太大的差異。

## React：JavaScript 函式庫

我們經常會聽到前端三大框架：React、Vue、Angular，但根據 [React](https://zh-hant.reactjs.org/) 官網定義：

> React: A JavaScript library for building user interfaces

嚴格來說，React 其實不算是一個框架，而是一個 JavaScript Libaray。通常使用 React 時會搭配其他 Libaray 使用，整個生態系結合起來就會和框架差不多。

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
```

重整頁面即可看到結果：

![](https://i.imgur.com/HPUh5ly.png)

## 結論

到這裡我們瞭解 React 重要的概念 Component，以及如何使用 JSX 語法來建立 React 元素。

此外，除了可以利用線上編輯器 CodeSandbox，也可以在本地端安裝官方提供的 create-react-app 套件，來快速建置 React 開發環境。