###### tags: `HTML & CSS`
# [week 6] HTML - 認識基礎標籤

> 本篇為 [[FE101] 前端基礎：HTML 與 CSS](https://lidemy.com/p/fe101-html-css) 這門課程的學習筆記。如有錯誤歡迎指正。

```
學習目標
 P1 你知道 HTML 是在做什麼的
 P1 你知道如何使用有語意的（semantic）標籤
 P1 你知道基本 SEO 的概念
```

## 網頁是如何製作的？

- 所有網站都使用 HTML 和 CSS 編寫，且根據不同需求，可能還會加入其他技術。
- 網頁其實就是有結構的「文字檔」，藉由瀏覽器解析渲染，呈現我們看到的畫面。

## 什麼是 HTML？

- 全名為 HyperText Markup Language，中文翻作「超文本標記語言」。
- 由名字可知 HTML 是一種標記語言，而非程式語言。
- HTML 使用「元素」來描述頁面結構，通常由兩個標記（tag）所組成：
  - 開始標記：可承載屬性，說明該元素的內容。例如：`<p>`
  - 結束標記：多一道斜線。例如：`</p>`

---

## 基本架構 - 必要的標籤

> 小技巧：在編輯器上輸入 `!` 再按 tab 鍵，可快速生成基本架構。

```htmlmixed=
<!-- 開頭需定義撰寫格式，告訴瀏覽器使用最新版本 HTML5 -->
<!DOCTYPE html>

<!-- 網頁的最根部元素 -->
<html lang="en">

  <!-- 包含網頁的基本資訊 -->
  <head>

  </head>

  <!-- 網頁顯示的內容 -->
  <body>

  </body>
  
</html>
```

## `<head>` 內常見的標籤

head 中的文檔並不會呈現在瀏覽器上，通常會包含一些資訊，例如：標題（`<title>`）、meatadata（提供網頁資訊給瀏覽器或搜尋引擎）、引入 CSS 或 JavaScript 文件並應用於 HTML 檔。

```htmlmixed=
<head>
    <!-- 聲明該頁面的編碼方式 -->
    <meta charset="UTF-8"> 
        
  　<!-- 在 RWD 網頁，用來設定在不同裝置下的顯示方式：
        width=device-width：符合裝置寬度最佳解析度
        initial-scale=1.0：設定手機螢幕畫面的初始縮放比例為 100% 
    -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- 在瀏覽器 tab 標籤上的標題 -->
    <title>網頁標題</title>
    <!-- 在瀏覽器 tab 標籤上的小圖示 -->
    <link rel="icon" href="favicon.ico">
    <!-- 引入 main.css -->
    <link href="main.css" rel="stylesheet">
    <!-- 引入 common.js -->
    <script src="common.js"></script>
</head>
```

參考資料：[HTML meta 標籤- Wibibi](https://www.wibibi.com/info.php?tid=415)

---

## `<body>` 內常見的標籤

- `<div>`：為區塊元素（block）

### 文字相關標籤

- `<h1>` ～ `<h6>`：標題有六階
  - 重要程度由高至低
  - 一個網頁通常只能有一個 h1
- `<p>`：段落
- `<span>`：為行內元素
- `<sup>` 和 `<sub>`：用來將內容上標、下標。為行內元素。

```htmlmixed=
<h3>上標（superscript）：常用來表示日期字尾、次方數</h3>
<p>Today is July 23<sup>rd</sup>.</p>

<h3>下標（subscript）：常用在註解或化學式上</h3>
<p>H<sub>2</sub>O</p>
```

![sup sub](https://i.imgur.com/SzbbSNA.png)


### 空白壓縮

當瀏覽器遇到兩個以上的空白字元時，只會顯示一個空白字元；同樣的，遇到斷行也會視為單一空白字元處理。

因此我們可以利用下列標籤幫助排版：

- `<pre>`：保留完整格式，可用於顯示整段程式碼
- `<br />` 或 `<br>`：換行
- `<hr />` 或 `<hr />`：水平線，大小寫均可，斜線可加可不加

舉個例子：

```htmlmixed=
我是標題
<hr>
我是段落一
<HR>
我是段落二
```

![](https://i.imgur.com/EFy5QbP.png)

### 清單列表

- `<ul>`：項目清單（unordered list）
- `<ol>`：編號清單（ordered list）
- `<li>`：清單中的每個項目（list item）
- 去除清單預設樣式 CSS：`list-style: none`

```htmlmixed=
<ol>
  <li>項目一</li>
  <li>項目二</li>
  <li>項目三</li>
</ol>
```

### `<a>`：加入連結

- `href` 屬性：指定要連結的目標網頁，可分為：
  - 絕對 URL：完整網址
  - 相對 URL：連到同一網站的其他頁面可使用，例如：contact.html
  - 上述兩種方式，也可使用 id 屬性：指定連結到網頁的目標元素

```htmlmixed=
<h1 id="top">網頁前端</h1>
<a href="#html">前往 HTML</a>
<a href="#css">前往 CSS</a>

<h2 id="html">HTML 介紹</h2>
<p>Lorem數字 + tab 鍵：可產生假字</p>

<h2 id="css">CSS 介紹</h2>
<p>Lorem1000 + tab 鍵：產生 1000 個假字</p>

<a href="#top">回到 TOP</a>
```

- `target` 屬性：
    - `_self`：為預設值，會在原頁面開啟連結
    - `_blank`：在新視窗開啟連結

```htmlmixed=
<a href="https://www.google.com.tw/">連到 Google 首頁</a>
<a href="https://www.google.com.tw/" target="_blank">另開新視窗</a>
```

### `<img>`：嵌入圖片

- `src` 屬性：指定圖片來源
- `alt` 屬性：當圖片無法顯示時的替代文字，可用來描述影像內容
- `title` 屬性：滑鼠停留在圖片上的提示文字

```htmlmixed=
<img src="https://www.google.com.tw/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png" alt="This is logo" title="google logo" >
```

- `<Iframe>`：內嵌網頁

- 

```htmlmixed=
<figure>
  <img src="https://images.unsplash.com/photo-1528138326811-ec5dd3b594be?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=750&q=80" alt="photo" title="tasty break">
  <figcaption>
    Photo by Vita Marija Murenaite on <a href="https://unsplash.com/photos/HyO7jwH4C6g">Unsplash</a>
  </figcaption>
</figure>
```

![figure](https://i.imgur.com/zRRRzrC.png)

### `<table>`：表格

表格是以一列一列的方式撰寫，每一列由 `<tr>` 元素來製作。

- `<tr>`：表格列（table row），表示每一列的起始
- `<td>`：表格資料（table data）
- `<th>`：表格標題（table head），用法和 `<td>` 元素相同，會以**粗體**顯示
  - 使用 scope 屬性：可標明是行（row）或列（col）的標題
  - 使用 rowspan、colspan 屬性：可跨行或跨列
- 製作長表格時，可分割成 `<thead>`、`<tbody>`、`<tfoot>`
- 舉個例子：

![table](https://i.imgur.com/2mddzqX.png)

```htmlmixed=
<!-- 上述表格程式碼如下 -->
<table>
  <tr>
    <th></th>
    <th>A</th>
    <th>B</th>
    <th>C</th>
  </tr>
  <tr>
    <th>內容</th>
    <td>漢堡</td>
    <td>薯條</td>
    <td>炸雞</td>
  </tr>
  <tr>
    <th>價格</th>
    <td>99</td>
    <td>25</td>
    <td>70</td>
  </tr>
</table>
```

### `<form>`：表單

其實 [Google 首頁](https://www.google.com.tw/)中央的搜尋框，就是最為人熟知的表單。除此之外，表單還提供其他線上功能，例如：註冊網站會員、網路購物、問卷調查等。


表單控制項：
from、input、textarea

`text`：純文字
`password`：顯示會以符號代替文字
可以使用 maxlength and minlength 來控制輸入字數
`email`：會自動做簡單驗證
`search`：搜尋框
`tel`：電話
`url`：網址

（待補）

參考資料：
1. [[第六週] HTML - 表單 form 介紹 - Yakim shu](https://yakimhsu.com/project/project_w6_HTML_form.html)

### 語意化元素（Semantic Elements）

使用 HTML5 的語意化元素取代 `<div>` 有以下優點：

1. 可使網頁結構和區塊位置更精確
2. 幫助優化 SEO（搜尋引擎最佳化）

![](https://i.imgur.com/o04CF37.png)
> 圖片來源：[快速了解HTML語意化標籤| by Kira Yang | Medium](https://medium.com/@changru.studio/%E5%BF%AB%E9%80%9F%E4%BA%86%E8%A7%A3html%E8%AA%9E%E6%84%8F%E5%8C%96%E6%A8%99%E7%B1%A4-33dd8247d779)

- `<header>` 頁首：常包含 Logo 圖案、導航列、搜尋欄位
- `<nav>` 導航列：主選單、頁尾選單
- `<main>` 主要內容
- `<footer>` 頁尾，通常包含版權訊息、法律訊息連結
- `<article>` 主要文章
- `<section>` 段落
- `<aside>` 側欄：與主要內文無關的區塊，也就是額外資訊（廣告、推薦文章等）

參考資料：
1. [金魚都能懂 網頁設計入門 : 網頁入門第一步 (第二天)](https://ithelp.ithome.com.tw/users/20112550/ironman/2072)
2. [HTML&CSS：網站設計建置優化之道](https://www.tenlong.com.tw/products/9789862765012)
3. [[第七週] HTML — Tag 基本標籤認識 - Miashu](https://medium.com/@miahsuwork/%E7%AC%AC%E5%85%AD%E9%80%B1-api-%E5%9F%BA%E7%A4%8E-html-tag-%E5%9F%BA%E6%9C%AC%E6%A8%99%E7%B1%A4%E8%AA%8D%E8%AD%98-d2d9a1c66449)
4. [[第六週] HTML - tag 基礎標籤介紹 - Yakim](https://yakimhsu.com/project/project_w6_HTML_tag.html)

---

### 補充：如何引入 js 檔案

1. 寫在 HTML 文件的 `<script>` 標籤內

也就是內嵌在 `<head>` 或 `<body>` 結束標籤之前。兩者差別在於：
- head 部分：會在被調用的時候才執行。
- body 部分：會在頁面載入的時候被執行。

由於 html 檔是由上而下依序載入，放在接近檔案底部，可確保 JavaScript 較晚被讀取，再去影響先被生成的 HTML 程式碼。但也可能導致腳本載入受阻，拖慢網站效能。

2. 外部引入 JavaScript 文件

建立 .js 檔案後，在 HTML 檔案中的 `<body>` 或 `<head>` 透過 `<script src= "">` 標籤引用。這是最常使用的方法，可橫跨多個 HTML 檔案使用。

```htmlmixed=
<!-- .js 檔案路徑為:scripts/common.js -->

<script src="scripts/common.js"></script>
```

參考資料：
1. [JavaScript 寫在哪裡？ - HiSKIO](https://hiskio.com/courses/51/lectures/856)
2. [JavaScript 是什麼? - 學習該如何開發Web | MDN](https://developer.mozilla.org/zh-TW/docs/Learn/JavaScript/First_steps/What_is_JavaScript)

