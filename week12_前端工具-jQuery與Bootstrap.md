###### tags: `Front-End` `jQuery` `Bootstrap`
# [week 12] 前端工具之一 - jQuery 與 Bootstrap

> 本篇為 [[FE201] 前端中階：那些前端會用到的工具們](https://lidemy.com/p/fe201) 這門課程的學習筆記。如有錯誤歡迎指正。

## jQuery：JS 函式庫

![](https://i.imgur.com/KKhdOms.png)

[jQuery](https://jquery.com/) 是一個 JavaScript 函式庫（Javascipt Framework），是幫助加速網頁程式開發的工具。可解決跨瀏覽器的問題，能夠快速又方面操作 DOM，縮短開發時間與簡化程式碼。

### 如何使用

和引入 JavaScript 方法類似：

```javascript=
<script src="https://code.jquery.com/jquery-3.5.1.js"></script>
```

### 基本格式

使用 jQuery 的基本語法如下，意即指派元素到指定的事件：

```javascript=
// 選取 `$(selector)` 元素，並指派它進行 `.action()` 事件
$(selector).action()

// 範例
$( "button.continue" ).html( "Next Step..." )
```

其中的 `$()` 符號就是 jQuery 常用的選擇器，善用選擇器可以快速找到網頁當中的物件。

以按鈕 Button 的點擊事件為例：

#### JavaScript 原生語法

```javascript
// 使用 DOMContentLoaded 或 load 事件，可確保 DOM 結構被完整的讀取跟解析
document.addEventListener('DOMContentLoaded', function () {
  document.querySelector('.btn').addEventListener('click', function() {
    alert('click!')
  })
})
```

兩者結構其實非常類似，多數情況下 JavaScript 和 jQuery 的語法兩者也能夠混用。

#### 使用 jQuery

```javascript=
// jQuery === $
$(document).ready (function() {
  $('.btn').click(function(e) {
    alert('click jq!')
  })
})
```

參考資料：
- [DOMContentLoaded與load的區別](https://www.cnblogs.com/caizhenbo/p/6679478.html)
- [jQuery Core API Documentation](https://api.jquery.com/load-event/#load1)：用來查閱官方說明文件
- [You might not need jQuery](http://youmightnotneedjquery.com/)：可對照 jQuery 和 JavaScript 的寫法

### 相關語法

#### 淡入淡出效果

```javascript=
<style>
  .box {
    height: 200px;
    width: 200px;
    background: brown;
  }
</style>
<script src="https://code.jquery.com/jquery-3.5.1.js"></script>

<script>
  var isHide = false
  $(document).ready (function() {
    $('.btn').click(function(e) {
      if (isHide) {
        $('.box').fadeIn(500)    // show()
      } else {
        $('.box').fadeOut(500)   // hide()
      }
      isHide = !isHide
    })
  })
  </script>
<body>
  <button class="btn">I am a button</button>
  <div class="box"></div>
</body>
```

#### 簡易 todo list

首先建立點擊事件，來拿取 input 欄位輸入的值：

```javascript=
<script src="https://code.jquery.com/jquery-3.5.1.js"></script>
<script>
$(document).ready (function() {
  $('.btn').click(function(e) {
    console.log($('.todo-input').val()) // 測試是否讀取成功
  })
})
</script>
<body>
  <input class="todo-input" />
  <button class="btn">Add todo</button>
  <div class="todos"></div>
</body>
```

再來加入新增 todo 到 todos 的功能：

- `prepend()`：新增元素到最上面
- `append()`：新增元素到最下面

```javascript=
<style>
  .todo {
    padding: 10px;
    border: 1px solid #eee;
  }
</style>
<script src="https://code.jquery.com/jquery-3.5.1.js"></script>
<script>
  $(document).ready (function() {
    $('.btn').click(function(e) {             // 建立點擊事件
      const value = $('.todo-input').val()    // 拿取 value 值
      $('.todo-input').val('')                // 讀取完後清空 input 欄位
      $('.todos').prepend(`<div class="todo">${value}</div>`)    // 把該元素新增到 todos
    })
  })
</script>
<body>
  <input class="todo-input" />
  <button class="btn">Add todo</button>
  <div class="todos"></div>
```

####  ajax

```javascript=
$.ajax({
  type: "GET", // 指定 method
  url: 'ajax/test.html',
  success: function(data) {
  $('.result').html(data);
  alert('Load was performed.');
  }
});

// 取得 HTML: load()
$("#htmDoc").load("test.html");

// 取得 JSON 格式的資料: getJSON()
$.getJSON("test.json", function(data){  
  for (var idx in data)  
    $("#menu").append("<li>" + data[idx] + "</li>"); 
});  

// document ready 事件
$(document).ready(function() {  
  alert('Hello');    // 網頁下載完成後立即執行 alert()
});

```

參考資料：
- [jQuery 簡單介紹](https://jw310.github.io/2020/01/16/jQuery-base/#%E4%BD%BF%E7%94%A8-jQuery)
- [JQuery 筆記 (一) – 淺談 JQuery](https://ithelp.ithome.com.tw/articles/10092592)

---

## Bootstrap：UI Library

[Bootstrap](https://getbootstrap.com/) 是一個由 HTML、CSS 和 JavaScript 寫成的前端框架，內建多項模組化的版型與元件樣式。例如：字體排印、表單、按鈕、導航及其他各種元件及 Javascript 擴充套件。主要用於創建網站和 Web 應用程式，能幫助快速開發響應式網頁。

參考資料：

- [Bootstrap 是什麼？給網頁設計新手的 Bootstrap 4入門教學](https://tw.alphacamp.co/blog/bootstrap-4-introduction)
- [Bootswatch](https://bootswatch.com/)：提供各種經過調整顏色的 Bootstrap 版本，可以套用在已有的 Bootstrap 網站上

---

## SASS：CSS 預處理器

在進入 CSS 預處理器之前，先談談 CSS 在開發上可能遇到哪些問題：

- 全域會互相干擾
  - 例如：在 index.html 同時引入 main.css 和 normalize.css 可能會互相干擾，不易進行 debug 與維護
- 沒有變數
  - 現在有 CSS Variable
- 沒有辦法組合

因此 CSS 預處理器就誕生了，讓我們能夠以寫程式的方式處理樣式，方便進行維護。

![](https://i.imgur.com/p8sM9vr.png)

### 什麼是 SASS？

#### [SASS（Syntactically Awesome Stylesheets）/ SCSS](https://sass-lang.com/install) 
- 是 CSS 預處理器，能更有結構撰寫程式碼，方便後續維護
- 適合大型專案使用
- SASS 無大括號；SCSS 有，因此後者和 CSS 相容
- CSS 預處理器需另外編譯成 CSS，瀏覽器才能看得懂
> 【註】[SAS（Statistical Analysis System） / SPSS](https://www.sas.com/zh_tw/home.html)：統計分析系統，用來進行數據分析的軟體

### SASS 提供的功能

- 參數與結構化 CSS
  - Nesting：巢狀語法
  - Variables：變數設定
- 模組化 CSS
  - Import
  - Extend
  - Mixin
  - Functions
- 自動化 CSS
  - Condition
  - Loop

### 相關語法

- 安裝 Sass

`npm install -g sass`：`-g` 表示在全域安裝

- 一次性編譯

`sass main.sass main.css`：將 main.sass 檔編譯成 main.css

- watch 模式

`sass --watch main.sass main.css`：每次存檔均會自動進行編譯

- 壓縮功能

`sass --style=compressed mais.sass main.css`：通常是在開發最後才會執行

## Sass 實作補充

main.css 通常由下列要素組成：
1. utils：整理出常用的 variables（背景顏色等）和 mixins（垂直至中、對齊等樣式功能）
2. Components：整理跨頁元件
3. Layouts：獨立的大元件
4. Pages
5. 其他樣式：themes（dark mode）、vendors（bootstrap css）

### 巢狀與變數

1. 巢狀語法：注意 `&` 前的空格

```sass=
.section
  width: 100%
  height: 50%
  border-bottom: 1px solid #000
  padding: 2rem
  &__title
    text-align: center
  &__wrapper
    display: flex
    justify-content: center
    align-items: center
```

編譯結果：

```css=
.section {
  width: 100%;
  height: 50%;
  border-bottom: 1px solid #000;
  padding: 2rem;
}
.section__title {
  text-align: center;
}
.section__wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

/*# sourceMappingURL=main.css.map */
```

2. 變數設定：使用 `$` 來命名

```sass=
$primary: #0047AB
$secondary: #4D80E6
$warning: #CD5C5C
$text:#CCCCFF
$background: #eeeeee

.color
  width: 50px
  height: 50px
  margin: 10px
  background: #000
  &-primary
    background: $primary
  &-secondary
    background: $secondary
  &-warning
    background: $warning
  &-text
    background: $text
  &-background
    background: $background
```

編譯結果：

```css=
.color {
  width: 50px;
  height: 50px;
  margin: 10px;
  background: #000;
}
.color-primary {
  background: #0047AB;
}
.color-secondary {
  background: #4D80E6;
}
.color-warning {
  background: #CD5C5C;
}
.color-text {
  background: #CCCCFF;
}
.color-background {
  background: #eeeeee;
}

/*# sourceMappingURL=main.css.map */
```

### 模組化 CSS

#### `@import`：引入檔案，用來分別進行管理
  - 例如：將變數放到 `_variables.sass` 統一管理，然後在 `main.sass` 加上 `@import _variables.sass` 即可引入檔案

#### `export`：繼承，處理共同樣式
  - 使用時機：可將所有相同樣式的內容合併，減少重複的行為
  - 例如：`<a>` 統一去除底線、制定 template
```sass=
%btn
  padding: 1rem 2rem
  color: $background
  font-size: 1rem
  margin: 1rem
  transition: .1s

.btn
  &-primary
    @extend %btn
  &-secondary
    @extend %btn
  &-waring
    @extend %btn
```

編譯結果：

```css=
.btn-waring, .btn-secondary, .btn-primary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
```

#### `mixin`：打包常用功能，替換局部變數

- 使用時機：用於需重複使用到的屬性，且可帶入變數，以進行微調
- 例如：width、height、flex-center 等

```sass=
@mixin btn
  padding: 1rem 2rem
  color: $background
  font-size: 1rem
  margin: 1rem
  transition: .1s

.btn
  &-primary
    +btn
  &-secondary
    +btn
  &-waring
    +btn
```

編譯結果：

```css=
.btn-primary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
.btn-secondary {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
.btn-waring {
  padding: 1rem 2rem;
  color: #eeeeee;
  font-size: 1rem;
  margin: 1rem;
  transition: 0.1s;
}
```

#### `function`：可回傳數值，搭配變數和 mixin 使用
  - 與 `@mixin` 的不同之處，在於 `@function` 只會回傳一個值，而 `@mixin` 是回傳一段CSS程式碼

### Sass 自動化

#### loop 迴圈

- 使用 `@each` 搭配 array
  - 以逗號表示 `center, start, end`

```sass=
$direction-typs: center, start, end

@each $type in $direction-typs
  .flex-#{$type}
    display: flex
    justify-content: $type
    align-items: center
```
編譯結果：
```css=
.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flex-start {
  display: flex;
  justify-content: start;
  align-items: center;
}

.flex-end {
  display: flex;
  justify-content: end;
  align-items: center;
}
```

- 使用 `@each` 搭配 map
  - 在括號以 `參數:值` 表示 `(center: center, start: flex-start, end: flex-end)`

```sass=
$direction-typs: (center: center, start: flex-start, end: flex-end)

@each $type, $value in $direction-typs
  .flex-#{$type}
    display: flex
    justify-content: $value
    align-items: center
```

編譯結果：

```css=
.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flex-start {
  display: flex;
  justify-content: flex-start;
  align-items: center;
}

.flex-end {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
```

- for 迴圈
  - 使用時機：用於預先設定，或特效網站需產生密集大小值
```sass=
@for $i from 0 through 5
    .h#{5 - $i + 1}
        font-size: 1 + 0.2rem * $i
```

編譯結果：

```css=
.h6 {
  font-size: 1rem;
}

.h5 {
  font-size: 1.2rem;
}

.h4 {
  font-size: 1.4rem;
}

.h3 {
  font-size: 1.6rem;
}

.h2 {
  font-size: 1.8rem;
}

.h1 {
  font-size: 2rem;
}
```

#### condition

有關 Flow Control 可參考[官方文件](https://sass-lang.com/documentation/at-rules/control/for)。

參考資料：
- [Sass/SCSS 基本語法介紹，搞懂CSS 預處理器](https://tw.alphacamp.co/blog/css-preprocessor-sass-scss)
- [常用色彩表](http://www.ebaomonthly.com/window/photo/lesson/colorList.htm)
- [SCSS 筆記(2) - extend、mixin、function](https://icguanyu.github.io/scss/scss_2/)

### 重構檔案

- 步驟一：將重複的部分用巢狀方式撰寫
- 步驟二：將顏色、字體大小，統一使用變數命名 `_variables.sass`
- 步驟三：抽檔案，把重複的區塊放到 `_components.sass`
- 步驟四：將分出的區塊以 `@import` 引入，完成後將所有檔案打包

---

## BABEL：JS 轉譯器

[BABEL](https://babeljs.io/) 是 JavaScript 轉譯器，可將 ES6+ 程式碼轉為等效的 ES5 程式碼。

參考資料：[利用 Babel 支援現代 JavaScript 的特性](https://michaelchen.tech/javascript-programming/babel/)

---

## gulp：整合流程

---

## Webpack：打包程式碼

### 如何安裝

跟著官方教學步驟 [Getting Started](https://webpack.js.org/guides/getting-started/) 安裝 webpack：

```
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

![](https://i.imgur.com/MP3ufbd.png)

### 執行步驟

- 需要進行編譯的檔案，通常不會直接放在根目錄，而是會先放在 src（source code），使用工具轉換後再放到其他地方。
- 路徑如下所示：

```
webpack-demo/src/index.js
```

接著以 index.js 引入 utils.js 為例：

#### utils.js

```javascript=
const Utils = {
  first: function(str) {
    return str[0]
  }
}

module.exports = Utils
```

#### index.js

```javascript=
const utils = require('./utils')

console.log(utils.first('abc'))
```

在 CLI 執行 `npm src/index.js` 後印出 `a`，代表成功引入檔案。

![](https://i.imgur.com/QmPof6p.png)


