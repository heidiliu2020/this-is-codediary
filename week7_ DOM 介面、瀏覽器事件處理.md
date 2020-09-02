###### tags: `JavaScript` `API`
# [week 7] DOM 介面、瀏覽器事件處理

> 本篇為 [[FE102] 前端必備：JavaScript](https://lidemy.com/p/fe102-javascript) 這門課程的學習筆記。如有錯誤歡迎指正。

當我們運用 JavaScript 在網頁進行操作時，主要可分為下列三大面向：

1. 介面（Interface）：如何改變介面
2. 事件（Event）：如何監聽事件並做出反應
3. 資料（Data）：如何和伺服器交換資料

> 以下主要探討「改變介面」與「事件監聽」的部分。

## JavaScript 與瀏覽器的溝通

那麼，該把 `<script>` 標籤放在哪呢？首先要瞭解，我們可以放在 HTML 檔案的任何位置，通常會有兩種回答：

1. 放在 `<head>` ... `</head>` 之間
2. 放在 `</body>` 之前

由於瀏覽器是由上而下渲染，相較之下以第二種方法較佳。但更好的做法，是另外新增 js 檔，以外連檔的形式執行 JS，更有助於管理與維護。

```htmlmixed=
<head>
  <title>This is title.</title>
  <script src="./main.js"></script>
</head>
```

### 執行環境

我們可以在 Node.js 或瀏覽器執行 JS。由於兩者是不同的執行環境，在語法支援度也有些微差異。

> 例如：
> 1. require() 引入模組的語法，只能在 Node.js上執行
> 2. alert() 叫出提示窗，只能在瀏覽器上執行

### DOM 是什麼？

DOM，全名是 Document Object Model（文件物件模型）。

是由「瀏覽器」提供用來和「程式語言」溝通的橋樑。最常被用在網頁與 JavaScript 的溝通。

DOM 類似於把 HTML 的文件（Document）轉成物件（Object）。JavaScript 即可透過 DOM 提供的 API 來存取並操作 HTML。

![DOM](https://i.imgur.com/yR0I3JG.png)

### 如何選取 DOM 元素

document 是瀏覽器提供的特殊物件，內部放著許多 function。而我們可以透過「物件」的方式去呼叫想要的元素：

- 根據 Tag 名稱選取：`document.getElementsByTagName()`
- 根據 Class 名稱選取：`document.getElementsByClassName()`
- 根據 ID 名稱選取：`document.getElementById()`
- 選取 CSS 選擇器：`document.querySelector()`

以 `document.getElementsByTagName('div')`，選取所有 tag 名稱是 div 元素為範例：

```htmlmixed=
<body>
  <div>
    This is tagName.
    <div class="box">This is className.</div>
    <div id="name">This is idName.</div>
  </div>

  <script>
    const elements = document.getElementsByTagName('div')
    console.log(elements)
  </script>
</body>
```

在瀏覽器運行結果，會得到類陣列如下：

![Tag](https://i.imgur.com/8i7ohCd.png)

若改選取 `elements[1]`，即可選取類陣列中的元素：

![類陣列](https://i.imgur.com/4LdiX9r.png)

若根據 Class 和 ID 名稱選取，也是類似用法：

- `document.getElementsByClassName('box')`：括弧內不需加 `.` 即可表示 class

![Class](https://i.imgur.com/O5eCQ6y.png)

- `document.getElementById('name')`：注意 ID 只會有一個

![Id](https://i.imgur.com/fia65T6.png)

相較於前面三種選取方式，又以 querySelector 更方便且直覺。就像寫 CSS 選擇器來選取元素：

- 選取標籤：`const element = document.querySelector('div')`
- 選取 class：`const element = document.querySelector('.box')`
- 選取 id：`const element = document.querySelector('#name')`

但 `querySelector()` 只會選取第一個匹配的元素，舉例如下：

```htmlmixed=
<body>

  <a href="#">1st</a>

  <div>
    <a href="#">2rd</a>
  </div>

  <script>
    const element = document.querySelector('a')
    console.log(element)
  </script>
</body>
```

結果只會選取第一個 `<a>`：

![querySelector](https://i.imgur.com/ZE1Ue7g.png)

若想選取到所有匹配到的元素，可使用 `querySelectorAll()`：

```htmlmixed=
 const element = document.querySelectorAll('a')
```

同樣能夠得到類陣列：

![querySelectorAll](https://i.imgur.com/ih6mj9T.png)

### 改變元素的 CSS：`.style`

- 直接寫上行內樣式

```htmlmixed=
const elements = document.querySelector('.box')

element.style.background = 'red';
element.style.paddingTop = '10px';         // 駝峰式
element.style["padding-top"] = '10px';     // 字串
```

- 但這種寫法其實不易修改，因此並不推薦。通常會寫好另外的 class，藉由「改變元素的 class」來套用該狀態，也就是下面要介紹的方法。

### 改變元素的 Class：`.classList`

- `.classList.add()`：增加
- `.classList.remove()`：移除
- `.classList.toggle()`：開關（有就刪、沒有就加）
- `.classList.contains()`：判斷是否包含該 class

```htmlmixed=
<style>
.active { 
  background: red;
}
</style>
element.classList.add('active')
<!-- 將元素套用 active 這個 class 的樣式 -->
```

### 改變元素的內容

- `.innerText`：只抓取標籤內的文字。最常用來改變文字內容。

```htmlmixed=
<div>
  <div class="box">This is <span>class</span>.</div>
  <div id="name">This is id.</div>
</div>

<script>
  const element = document.querySelector('.box')
  console.log(element.innerText)
</script>
<!-- 印出：This is class. -->
```

- `.innerHTML`：抓取標籤內的所有內容，包含 HTML 標籤。

```htmlmixed=
<div>
  <div class="box">This is <span>class</span>.</div>
  <div id="name">This is id.</div>
</div>

<script>
  const element = document.querySelector('.box')
  console.log(element.innerHTML)
</script>
<!-- 印出：This is <span>class</span>. -->
```

- `.outerHTML`：抓取整個元素，可用來重新設置元素本身。較少使用。

```htmlmixed=
<div class="block">
  <div class="box">This is <span>class</span>.</div>
  <div id="name">This is id.</div>
</div>

<script>
  const element = document.querySelector('.box')
  console.log(element.outerHTML)
</script>
<!-- 印出：<div class="box">This is <span>class</span>.</div> -->
```

### 刪除與插入元素：removechild 與 appendChild

- `.removechild()`：移除子元素

```htmlmixed=
<div class="block">
  <div class="box">This is <span>class</span>.</div>
  <a>This is a.</a>
</div>

<script>
  const element = document.querySelector('.block')
  element.removeChild(document.querySelector('a'))
</script>
<!-- 成功刪除在 block 元素底下，標籤為 a 的元素 -->
```

- `.appendChild()`：插入元素在最後方

1. 插入元素之前，要先建立元素或文字：
    - `const newElement = document.createElement('h2')`
    - `const newTextElement = document.createTextNode('Hello World')`
2. 增加節點：
    - `element.appendChild(newElement)`
    - `element.appendChild(newTextElement)`

參考資料：

1. [前端基礎JavaScript篇：JavaScript 與瀏覽器的溝通| by Hugh's Program learning](https://medium.com/@hugh_Program_learning_diary_Js/%E5%89%8D%E7%AB%AF%E5%9F%BA%E7%A4%8E-javascript%E7%AF%87-javascript-%E8%88%87%E7%80%8F%E8%A6%BD%E5%99%A8%E7%9A%84%E6%BA%9D%E9%80%9A-e00ff05d0fdc)
2. [JavaScript入門系列：BOM和DOM筆記| 快樂學程式](https://www.happycoding.today/posts/43)
3. [HTML DOM 元素](https://www.runoob.com/jsref/dom-obj-document.html)

---

## JavaScript 網頁事件處理

JavaScript 是以事件驅動（Event-driven）的程式語言。

也就是說，當 JS 被瀏覽器載入後並不會馬上執行，而是透過使用者操作來觸發事件，才會啟動對應程式。例如：滑鼠點擊、鍵盤輸入等。

> 可參考：[HTML DOM Event 對象列表](https://www.w3schools.com/jsref/dom_obj_event.asp)

### 監聽事件 `addEventListener()`

在處理事件時，通常會需要指派監聽者（Event listeners）來監聽事件觸發。

也就是監測 DOM 中的某一元素，當使用者觸發某事件時，就會執行後續動作，語法如下：

```javascript=
element.addEventListener('event', function, useCapture)
```

### 參數值說明

#### 1. event 事件
- 必須
- 指定事件類型
- 常見的事件類別：（詳見 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Events)）
  - 滑鼠相關：click、mousedown、mouseenter、mouseleave
  - 鍵盤相關：keydown、keypress、keyup
  - 瀏覽器相關：scroll、resize

#### 2. function 功能
- 必須
- 指定事件觸發時執行的函式

而 function 的部分有下列兩種寫法，以最常見的 click 做舉例：

- 直接將函式作為參數帶入：回呼函式（callback function）

```javascript=
const element = document.querySelector('.box')
// 先註冊一個事件
element.addEventListener('click', onClick())

// 等事件被觸發才執行 callback function
function onClick(){     
   alert('click!')
}
```
- 匿名函式（anonymous）

```javascript=
const element = document.querySelector('.box')

element.addEventListener('click', function(){
   alert('click!')
})
```

#### 3. useCapture
- 可選
- 布林值，指定事件在捕獲或冒泡階段執行
  1. true：補獲
  2. false：冒泡（預設值）

參考資料：
1. [重新認識 JavaScript: Day 14 事件機制的原理](https://ithelp.ithome.com.tw/articles/10191970)
2. [HTML DOM addEventListener()方法](https://www.runoob.com/jsref/met-element-addeventlistener.html)

### 事件資訊 `event(e)`

一個事件的發生會包含各種資訊。event 資訊會放在 callback function 的第一個參數。通常取名 `event` 或簡寫 `e`，可以當作是一個「物件」，裡面放著關於此事件的參數值。

以 click 事件為例：

```javascript=
const element = document.querySelector('.box')

element.addEventListener('click', function(e) {
  console.log(e)
})
```

點擊元素後會出現下列資訊：

![event](https://i.imgur.com/JJEo1ZR.png)

我們可以用「物件」的方式來取得需要的元素，以常用的事件資訊為例。

### 常用的事件資訊

#### click 事件：點擊 DOM 元素時觸發

- e.target：點擊到的元素
- e.screenX：滑鼠離視窗左邊的距離
- e.screenY：滑鼠離視窗上邊的距離

#### keydown 事件：按下鍵盤時觸發

- e.key：按鍵號碼

#### submit 事件：提交表單前觸發，通常用來驗證表單內容

首先建立一個簡易表單：

```htmlmixed=
  <form class="login-form">
    <div class="account">
      Account: <input name="account" type="text">
    </div>
    <div class="password">
      Password: <input name="password" type="password">
    </div>
    <div class="password2">
      Password again: <input name="password2" type="password">
    </div>
    <div class="submit">
      <input name="submit" type="submit" value="Submit">
    </div>
  </form>
```

![form](https://i.imgur.com/wWjVaIv.png)

點選表單中的 submit 按鈕後，會以預設方法 `GET` 送出資料，也就是把參數帶入原網址送出。而 submit 事件是在表單送出前觸發，通常用來驗證表單內容。

以點選 submit 後彈出視窗為例：
```javascript=
  const element = document.querySelector('.login-form')
  element.addEventListener('submit', function () {
    alert("成功")
})
```

![](https://i.imgur.com/grkuUrS.png)

### 阻止預設行為 `e.preventDefault()`

用來阻止瀏覽器上特定元素的預設行為。以下為常見使用方式：

- `<form>` 的 submit 事件：阻止送出表單
- `<a>` 的 click 事件：阻止轉址
- `<input>` 的 keypress 事件：阻止輸入按鍵

以上述例子來說，當 Password 跟 Password again 輸入的值不同時，就可使用 `e.preventDefault()` 來阻止表單送出：

```javascript=
<script>
  const element = document.querySelector('.login-form');

  element.addEventListener('submit', function(e) {
    const pw1 = document.querySelector('input[name="password1"]');
    const pw2 = document.querySelector('input[name="password2"]');
    // 若密碼不同，就不送出表單
    if (pw1.value !== pw2.value) {
      alert('密碼不同！')
      e.preventDefault();
    } 
  });
</script>
```

![](https://i.imgur.com/vjBsv0i.png)

參考資料：
1. [[第七週] DOM - 操作 DOM 介面、事件監聽 - Yakim](https://yakimhsu.com/project/project_w7_DOM.html)
2. [[第八週]DOM — 瀏覽器事件處理 - Miahsu](https://medium.com/@miahsuwork/%E7%AC%AC%E5%85%AB%E9%80%B1-dom-%E7%80%8F%E8%A6%BD%E5%99%A8%E4%BA%8B%E4%BB%B6%E8%99%95%E7%90%86-fb8f6178178a)
