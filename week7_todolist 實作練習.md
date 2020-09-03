###### tags: `JavaScript` 
# 【學習筆記】利用 JavaScript 實作簡易 Todo List

> 本篇為 [[FE102] 前端必備：JavaScript](https://lidemy.com/p/fe102-javascript) 這門課程的學習筆記。如有錯誤歡迎指正。

## 前言

久久未更新，自從加入程師導師計畫後，不知不覺持續寫程式也快滿三個月了。這禮拜進入第二次複習週，雖然仍有許多進度要補上，回頭複習也同樣重要！

於是趁這週把之前實作過的 Todo List 觀念再重整一次，除了練習如何透過 JavaScript 操控 DOM 元素、如何進行事件監聽，寫下這篇筆記做記錄。

## 目標功能

- 新增 todo
- 刪除 todo
- 標記 todo 為已完成/未完成
- 延伸：搭配 localStorage 本地儲存

## Step1. 分析網頁所需元素

在開始切版前，可利用 [whimsical](https://whimsical.com/a) 等工具先畫出設計稿：

![](https://i.imgur.com/lKFBsU5.png)

- `wrapper`：包含整個 Todo List 的容器
- `.todo__input`：輸入欄位 input 和新增按鈕
- `.todo__list`：放置所有 todo 的容器
- `.todo`：切換狀態 checkbox、todo 標題、刪除按紐

## Step2. 開始切版

按照設計稿，寫出程式碼如下：

```htmlmixed=
<div class="wrapper">
  <h1>Todo List</h1>
  <div class="todo__input-block">
    <input class="todo__input" type="text" placeholder="Add New Todo Here..." minlength="1" maxlength="48">
    <button class="btn-new"></button>
  </div>
  <div class="todo__hr"></div>

  <ul class="todo__list">
    <li class="todo">
      <label class="todo__title">
        <input class="todo__check" type="checkbox">
        <p>Fix debug</p>
      </label>
      <button class="btn-delete"></button>
    </li>
    <li class="todo">
      <label class="todo__title">
        <input class="todo__check" type="checkbox">
        <p>Write code</p>
      </label>
      <button class="btn-delete"></button>
    </li>
  </ul>
</div>
```

> 在 `<label>` 標籤中放入 `input` 標籤，就不需指定 for 和 id，可參考：[HTML `<label>` 标签的 for 属性](https://www.w3school.com.cn/tags/att_label_for.asp)

將上述程式碼再加入 css 屬性，排版後版面如下：

![](https://i.imgur.com/LM6u1e2.png)

## Step3. 建立監聽事件

在處理事件時，通常會需要指派監聽者（Event listeners）來監聽事件觸發。

透過監測 DOM 中的某一元素（element），當使用者觸發某事件（event）時，就會執行後續動作（functuion），語法如下：

```javascript=
element.addEventListener('event', function, useCapture)
```

- event 事件：監聽事件類型，如：`click` 或 `keypress`
- function 功能：當指定事件觸發時執行函式
- useCapture：指定事件在捕獲或冒泡階段執行，預設值為 flase（冒泡）

參考資料：[[week 7] DOM 事件傳遞機制：捕獲與冒泡、事件代理](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week7_%E6%8D%95%E7%8D%B2%E8%88%87%E5%86%92%E6%B3%A1%E3%80%81%E4%BA%8B%E4%BB%B6%E4%BB%A3%E7%90%86.md)

### 新增 todo

- 在輸入框 `點選 Add` / `按下 Enter 鍵`，可新增 todo
- 新增後清空輸入框內容

#### 步驟如下：

1. 選取需要的 DOM 元素，監聽該元素的 `click` / `keypress` 事件

```javascript=
// 點擊按鈕新增
document.querySelector('.btn-new').addEventListener('click', function () {
  addTodos();
});

// 按 Enter 新增
document.querySelector('.todo__input').addEventListener('keypress', function (e) {
  // Enter 對應鍵盤代碼：13
  if (e.which === 13) {
    addTodos();
  }
});
```

2. 若接收到事件，以函式 `addTodos` 處理新增 todo

```javascript=
function addTodos() {
  const inputValue = document.querySelector('.todo__input').value;
  
 // 檢查輸入欄位是否為空值，trim() 可清除字串前後空白
  if (inputValue.trim().length === 0) return;
  // 新增 todo
  const newTodo = document.createElement('li');
  newTodo.classList.add('todo');
  newTodo.innerHTML = `
    <label class="todo__title">
      <input class="todo__check" type="checkbox">
      <p>${escapeHtml(inputValue)}</p>
    </label>
    <button class="btn-delete"></button>
  `
  document.querySelector('.todo__list').appendChild(newTodo);
  // 新增成功後，清空輸入欄
  document.querySelector('.todo__input').value = '';
}
```

3. 利用跳脫函式 `escapeHtml` 處理特殊字元，防止程式碼無法正常顯示

```javascript=
function escapeHtml(unsafe) {
  return unsafe
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#039;');
}
```

### 刪除 todo

- 點擊右側叉叉圖示，可刪除 todo

#### 遇到問題：動態新增

由於 todo 是「動態新增」，若直接選取 `btn-delete`，將無法對後來新增的按鈕進行監聽。

#### 解決辦法：事件代理

我們可透過事件傳遞機制，改成對父元素（事件代理）進行事件監聽，如此即可對底下的所有 `btn-delete` 進行監聽。

```javascript=
// 事件代理：透過父元素來監聽 click 事件
document.querySelector('.todo__list').addEventListener('click', function (event) {
  const target = event.target;
  // 刪除 todo
  if (target.classList.contains('btn-delete')) {
    target.parentNode.remove()
  }
});
```

### 標記 todo 為已完成/未完成

- 點擊左側框框，能夠切換狀態為 `已完成` / `未完成` 

#### 步驟如下：

1. 同樣監聽 checkbox 的父元素 `.todo__list`
2. 若點擊目標包含 checkbox，即可切換狀態

```javascript=
document.querySelector('.todo__list').addEventListener('click', function (event) {
  const target = event.target;
  // check / uncheck todo
  if (target.classList.contains('todo__check')) {
    target.parentNode.classList.toggle('todo__done')
  }
}
```

## 延伸：搭配 localStorage 本地儲存

### localStorage

- 是由 HTML5 提供的一種 Web Storage
- 儲存大小約 5MB
- 可將資料儲存在用戶端，資料就不會因重整頁面或關閉瀏覽器而消失
- 以 key-value pair 的形式保存，且會轉成 JSON 字串格式

### localStorage method

- 儲存資料：`localStorage.setItem('key', 'value')`
- 讀取資料：`let storageValue = localStorage.getItem(key, - value)`
- 清除資料：`localStorage.removeItem(key)`
- 清除全部資料：`localStorage.clear()`

> 搭配 localStorage 實作 Todo List，我們可將資料存放在瀏覽器。但似乎還需要時間來研究如何使用，等之後熟悉用法會再來更新！

參考內容：
1. [[JS初心者]帶你用localStorage做出一個陽春版「To-Do List」(待辦清單)-1](https://medium.com/coding-girl-life/js%E5%88%9D%E5%BF%83%E8%80%85-%E5%B8%B6%E4%BD%A0%E7%94%A8localstorage%E5%81%9A%E5%87%BA%E4%B8%80%E5%80%8B%E9%99%BD%E6%98%A5%E7%89%88-to-do-list-%E5%BE%85%E8%BE%A6%E6%B8%85%E5%96%AE-1-8342dc1ea7d0)
2. [Day 20 - 30 天 Progressive Web App 學習筆記 - To-Do List 實作 PWA - (新增/修改/刪除待辦事項清單)](https://ithelp.ithome.com.tw/articles/10188458)
3. [JS30-Day15-LocalStorage](https://ithelp.ithome.com.tw/articles/10195522)
4. [HTML5 Web Storage](https://www.huanlintalk.com/2012/06/html5-web-storage.html)
5. [[JavaScript] Cookie、LocalStorage、SessionStorage 三種差異](https://medium.com/@jscinin/javascript-cookie-localstorage-sessionstorage-%E4%B8%89%E7%A8%AE%E5%B7%AE%E7%95%B0-fe7f38260439)
