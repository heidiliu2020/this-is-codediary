###### tags: `HTML & CSS`
# [week 6] CSS - 認識基礎標籤 & 屬性

> 本篇為 [[FE101] 前端基礎：HTML 與 CSS](https://lidemy.com/p/fe101-html-css) 這門課程的學習筆記。如有錯誤歡迎指正。

```
學習目標:

 P1 你知道 CSS 是什麼
 P1 你知道 inline、block 跟 inline-block 的區別
 P1 你知道什麼是 box model
 P1 你知道 position 的所有屬性及其差別
 P2 你知道 :hover, :before, :after
 P2 你知道 :nth-child 的各種用法
 P2 你熟悉 CSS selector，可以輕鬆選到想選到的元素
```

## 什麼是 CSS？

Cascading Style Sheets 階層式樣式表

## 如何引入 CSS

### 使用內部 CSS

1. 直接在 body 的元素內加上 `style=""`

```htmlmixed=
<html>
  <head>
    <meta charset="UTF-8">
    <title>網頁前端</title>
  </head>
  <body>
    <!--   文字變紅色 -->
    <div style="color: red;">
      網頁前端課程
    </div>

  </body>
</html>
```

2. 在 head 裡加上 `<style>` 標籤：

```htmlmixed=
<html>
  <head>
    <meta charset="UTF-8">
    <title>網頁前端</title>
        <style>
          div {
            color: blue;
          }
        </style>
  </head>
  <body>
    <!--   文字變藍色 -->
    <div>
      網頁前端課程
    </div>
  </body>
</html>
```

### 使用外部 CSS

使用 `<link>` 元素，從外部引用 CSS 檔，這是一個空白的元素（不需要結束標記）：
- `href`：指定 CSS 檔的路徑，通常放在 css 或 styles 資料夾
- `rel`：表示 HTML 頁面與連結其連結檔案的關係，連到 CSS 檔其值應為 stylesheet
 
將 style.css 引入 index.html 為例：

```htmlmixed=
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>網頁前端</title>
    <link rel="stylesheet" href="css/style.css" >
</head>
<body>
  網頁前端課程
</body>
</html>
```

內部引入的兩種方法，因不易維護而較少使用。使用外部引入的優點如下：

1. 將內容和外觀分開，結構較清楚
2. 可在不同頁面使用同一個樣式規則，不需修改每個檔案，便於維護

## CSS Selector

利用 CSS 選取器，可將規則套用在 HTML 文件的特定元素上，寫法如下：

```htmlmixed=
<!-- 選取器 -->
selector {
  <!--   宣告（分成屬性和值兩個部分） -->
  attribute: value;
}
```

### 全域選擇器 

- Universal Selector：套用到文件的所有元素。

```htmlmixed=
<!-- 所有背景均為藍色 -->
* {
  background: blue
}
```

### 標籤選取器

- 找出符合的元素名稱。

```htmlmixed=
<!-- 將所有 <h3> 元素套用該樣式 -->
h3 {
  color: red
}
```

### 類別選取器

- CLASS Selector：找出 class 屬性值符合句號後之值的元素。

```htmlmixed=
<!-- 找出所有 class 屬性之值為 note 的元素 -->
.note {}

<!-- 找出所有 class 屬性之值為 note 的 <h3> 元素 -->
h3.note{}
```

### ID 選取器

- 和 class 的最大不同，在於整個網頁上 id 只能有一個。

```htmlmixed=
<!-- 找出 id 為 name 的元素 -->
#name {}
```

### 子元素選取器

- 利用 `>` 區隔兩個元素，表示在有父子關係的元素才會套用。
- 與後代選取器的差異在於中間不能插入其他元素。

```htmlmixed=
<!-- 找出任何身為 <li> 元素之子的 <a> 元素 -->
li > a {
  color: red;
}
```

### 後代選取器

- 利用空白鍵區隔兩個元素。
- 中間可堆疊其他元素。

```htmlmixed=
<!-- 找出任何位在 <li> 元素內的 <a> 元素 -->
li a {
  color: yellow;
}
```

### 偽類選取器

> [Pseudo-classes 偽類一覽表](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)

以 hover（滑鼠滑入的樣式）為例：

```htmlmixed=
<!-- 當滑鼠移至任何 a 連結上時，背景會變成黃色 -->
a:hover {
  background: yellow
}
```

參考資料：[CSS Selectors Reference - W3Schools](https://www.w3schools.com/cssref/css_selectors.asp)

### nth-child(n) 選擇器

參考資料：[CSS選取第幾個標籤元素：nth-child(n)、first-child、last-child](https://www.itread01.com/content/1541105823.html)

### CSS Selector 的權重計算方式

當選擇器作用在同一元素上時：

- 兩個權重不同：權重值高的規則生效
- 兩個權重相同：後面覆蓋前面

權重由高到低如下：

```
!important > inline style > id > class > tag > *
```

各類選擇器：

- !important：權重最高，但在實際開發過程，幾乎不會使用 !important 來覆蓋其他規則
- inline style 行內樣式
  - 權重為 1-0-0-0
- id 選擇器（`#`）
  - 權重為 0-1-0-0
- class 類別選擇器、pseudo class 偽類選擇器、attribute 屬性選擇器
  - 權重為 0-0-1-0
- tag 標籤選擇器、pseudo elements 偽元素選擇器
  - 權重為 0-0-0-1
- 萬用選擇器（`*`）：選擇所有元素
  - 預設為 0-0-0-0

參考資料：

1. [強烈推薦收藏好物 – CSS Specificity (CSS 權重一覽)](https://muki.tw/tech/css-specificity-document/)
2. [你對 CSS 權重真的足夠了解嗎？](https://juejin.im/post/5afa98bf51882542c832e5ec)


---

## box model 盒模型

盒模型又稱為區塊模型，意思是所有 HTML 元素均可被視為一個盒子。我們能透過 CSS 控制內距、外距、邊框屬性，來調整盒子的外觀及位置。

![box model](https://i.imgur.com/HzfLkAY.png)

盒模型的組成由外而內分別為：

- margin（外邊距）
- border（邊框）
- padding（內邊距）
- content（內容）

### box-sizing 屬性

透過這個屬性，我們能控制盒模型長寬的計算方式。

以長寬同樣為 150px 的 box 為例：

<iframe height="265" style="width: 100%;" scrolling="no" title="Box Model" src="https://codepen.io/heidiliu2020/embed/GRoeoaO?height=265&theme-id=dark&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/GRoeoaO'>Box Model</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

我們可以使用 DevTool 看盒模型：

- `box-sizing: content-box`：為預設屬性。「內容」就等於長寬度。

![content-box](https://i.imgur.com/euaGSlb.png)
加上 border 和 padding 會導致元素實際大小比設定值還大。

- `box-sizing: border-box`：「邊框到邊框之間的範圍」等於長寬度。

![border-box](https://i.imgur.com/BdytGoL.png)
此時長寬設定會套用到 border、padding、content。

而 margin 在兩種盒模型都是額外加上去的，並不會影響盒子長寬。

參考資料：
1. [盒模型- Web 开发者指南| MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Getting_started/Boxes)
2. [CSS box model 盒子模型- Wibibi](https://www.wibibi.com/info.php?tid=CSS_box_model_%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B)
3. [CSS排版基礎觀念（一）—— box-model](https://medium.com/passionred/css%E6%8E%92%E7%89%88%E5%9F%BA%E7%A4%8E%E8%A7%80%E5%BF%B5-%E4%B8%80-box-model-13a9a3dfe84f)

---

## display: inline, block 跟 inline-block 的差別

display 是 CSS 中用於控制排版的屬性。每個 HTML 元素都有一個預設的 display 值，大部分的元素可分為 block（區塊元素）和 inline（行內元素）兩類：

### block：區塊元素

- 元素寬度預設會占滿整行
- 可設定寬高/margin/padding，但會占滿一整行
- 常見區塊元素：div、h1~h6、p、ul、li 等

### inline：行內元素

- 元素可在同一行內呈現
- 無法設定寬高，元素的寬高靠內容物撐開
- 仍可設定上下 margin/padding，但字仍在行內，排版不會隨設定改變
- 常見行內元素：a、span、input、img 等

### inline-block：行內區塊元素

- 以 inline 方式呈現：可水平排列
- 擁有 block 的屬性：可設定元素的寬高/margin/padding

### 以長寬同樣為 100px 的 box 為例：

#### `display: block`

![block](https://i.imgur.com/jx5DjYZ.png)

#### `display: inline`

![inline](https://i.imgur.com/SBjWPnH.png)

#### `display: inline-block`

![inline-block](https://i.imgur.com/I4pokc3.png)

### 以文字排版為例：

<iframe height="265" style="width: 100%;" scrolling="no" title="Box Model" src="https://codepen.io/heidiliu2020/embed/xxZBNPR?height=265&theme-id=dark&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/xxZBNPR'>display</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### Display 實戰篇：以製作圖文小卡為例

<iframe height="327" style="width: 100%;" scrolling="no" title="Display 實戰：切出圖文小卡" src="https://codepen.io/heidiliu2020/embed/VweNLwm?height=327&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/VweNLwm'>Display 實戰：切出圖文小卡</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

參考資料：
1. [CSS教學-關於display:inline、block、inline-block的差別](https://medium.com/@wendy199288/css%E6%95%99%E5%AD%B8-%E9%97%9C%E6%96%BCdisplay-inline-inline-block-block%E7%9A%84%E5%B7%AE%E5%88%A5-1034f38eda82)
2. [金魚都能懂網頁設計入門 : 網頁兩大主角 (鐵人賽第十天)](https://ithelp.ithome.com.tw/articles/10204956)
3. [關於"display" 屬性 - CSS](https://zh-tw.learnlayout.com/display.html)

---

## Position 屬性

- position 屬性可用來指定元素定位方式，以進行版面配置。
- 當我們使用定位元素時，若元素方框重疊，可搭配 z-index 屬性設定元素堆疊順序。

### static：正常流向

- 為預設值。在瀏覽器中會依照預設配置，由上而下自動排列

### relative：相對定位

- 以「原本顯示的位置」作為基準，且該元素原本所佔的空間仍會保留

### absolute：絕對定位

- 跳脫排版流，不會影響頁面其他元素
- 以「基準元素」左上角為起點，進行絕對位移。基準元素是往上層找的「第一個 position 不是 static 的元素」，才可作為定位點
- 若沒有指定基準元素，預設是以 body 元素（整個視窗）左上角為起點

### fixed：固定定位

- 跳脫排版流。是一種絕對定位，同樣不影響其他元素
- 將元素固定在瀏覽器視窗的相對位置，捲動頁面時仍會在固定位置

### 以文字排版為例：

<iframe height="265" style="width: 100%;" scrolling="no" title="定位元素：relative, absolute, fixed" src="https://codepen.io/heidiliu2020/embed/BajbgEK?height=265&theme-id=dark&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/BajbgEK'>定位元素：relative, absolute, fixed</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### Position 實戰篇：以製作彈窗為例

<iframe height="265" style="width: 100%;" scrolling="no" title="Position 實戰：製作彈窗" src="https://codepen.io/heidiliu2020/embed/xxZBobX?height=265&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/xxZBobX'>Position 實戰：製作彈窗</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

參考資料：
1. [關於position 屬性 - 學習CSS 版面配置](https://zh-tw.learnlayout.com/position.html)
2. [CSS position 位置屬性（定位） - Wibibi](https://www.wibibi.com/info.php?tid=156)
3. [position 屬性的基礎概念 - Medium](https://medium.com/ui-ux%E7%B7%B4%E5%8A%9F%E5%9D%8A/position-%E5%B1%AC%E6%80%A7%E7%9A%84%E5%9F%BA%E7%A4%8E%E6%A6%82%E5%BF%B5-5931254e5203)

---

## 水平置中：

### `margin: auto;`

設定左右外邊距（margin-left 與 margin-right）為 auto 可達成水平居中。


參考資料：[學習 CSS 版面配置](https://zh-tw.learnlayout.com/margin-auto.html)

## 補充：盒模型為什麼無法貼邊？

盒模型在瀏覽器中會有默認的外邊距。此時只要利用 CSS Reset 即可解決空隙問題。

### CSS Reset

- 方法一：利用全域選擇器

```htmlmixed=
* {
  margin: 0;
  padding: 0;
}
```

- 方法二：直接對元素進行設定

```htmlmixed=
/* 設定基底字體大小 */
html {
  font-size: 12px;
}
/* 取消預設空隙 */
html, body, h1, h2, h3, h4, p{
  padding: 0;
  margin: 0 ;
}
```

###  CSS Reset 與 CSS normalize

- [CSS Reset](https://meyerweb.com/eric/tools/css/reset/)

優點：統整、重置了各個瀏覽器的樣式設定。
缺點：必須全部重新做設定，比較沒有彈性。且在使用開發者工具時，會看到一大坨的繼承鏈（inheritance chain）。

- [Normalize.css](https://necolas.github.io/normalize.css/)

最大的特色就是保留原本預設 HTML 標籤的樣式，僅針對不同瀏覽器與各版本間不相容的標籤進行些微調整。

參考資料：
[Day21：小事之CSS Reset 與CSS normalize](https://ithelp.ithome.com.tw/articles/10196528)

## 切版注意事項

### 盡量不要把高度寫死

例如：.header、.content、.footer 不用寫死高度，以內容撐出高度，並用 padding 產生留白，往後若更動內容才不易跑版。

### 如何垂直置中？

- 加上 padding 留白
- 使用行高，但僅限只有一行字的時候才能使用

原理：「DIV 區塊的高度設定 = 文字的 line-height（行高）」
     
### 如何水平置中？

- 文字水平置中：`text-align: center` 
- 區塊元素本身置中：`margin: 0 auto`

---

## 如何讓 img 自適應容器大小

不管容器有多大，只要將 img 的寬高設定成 100 %（這裡的100%是相對於父級寬高而言）就能自適應容器大小。

參考資料：[css讓圖片自適應容器（div）大小- IT閱讀 - ITREAD01.COM](https://www.itread01.com/content/1546673250.html)

## CSS 屬性用法紀錄

1. [CSS 限制字數/行數，讓過長的文字隱藏變"…"](https://blog.ja-anything.com/2018/01/17/css-%E8%AE%93%E9%81%8E%E9%95%B7%E7%9A%84%E6%96%87%E5%AD%97%E9%9A%B1%E8%97%8F%E8%AE%8A-css-to-make-text-overflow-elegant-is-with-ellipses-single-or-multiple-lines/)
```css
  /* 修飾過長的文字，ellipsis：用點點點來表示被切斷的字串 */
  text-overflow: ellipsis;
  /* 不進行換行 */
  white-space: nowrap
```

2. [box-shadow 屬性的參數](https://www.w3cplus.com/content/css3-box-shadow)
```css
  /* 投影方式 X軸偏移 Y軸偏移 模糊半徑 擴展半徑 顏色}*/
box-shadow:inset x-offset y-offset blur-radius spread-radius color
```

3. [[教學] CSS3 半透明背景的四種設定方法 (RGBa、HSLa、Opacity、HEX)](https://www.minwt.com/webdesign-dev/css/21538.html)
```css
opacity: x
/* x　取值 0~1，該區塊元素均會調整透明度 */
background: rgba(red, green, blue, alpha)
/* alpha 代表透明度，取值 0~1 */
```

4. 決定換行的屬性
```css
/* 預設不換行 */
flex-wrap: no-wrap;
/* 換行 */
flex-wrap: wrap;
```

5. 文字跑板問題

- `word-break`：文字斷行效果

`word-break: break-all;`：遇到邊界就換行

- `white-space`：如何處理元素內的空白

`white-space: pre-line;`：自動合併多個空白，但保留換行符號
