###### tags: `Front-End` `jQuery` `Bootstrap`
# [week 12] 前端工具 - jQuery 與 Bootstrap

> 本篇為 [[FE201] 前端中階：那些前端會用到的工具們](https://lidemy.com/p/fe201) 這門課程的學習筆記。如有錯誤歡迎指正。

## 什麼是 jQuery

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

## Bootstrap

[Bootstrap](https://getbootstrap.com/) 是一個由 HTML、CSS 和 JavaScript 寫成的前端框架，提供一整套可重覆使用的元件樣式，例如：字體排印、表單、按鈕、導航及其他各種元件及 Javascript 擴充套件。主要用於創建網站和 Web 應用程式，能幫助快速開發響應式網頁。

參考資料：

- [Bootstrap 是什麼？給網頁設計新手的 Bootstrap 4入門教學](https://tw.alphacamp.co/blog/bootstrap-4-introduction)
- [Bootswatch](https://bootswatch.com/)：提供各種經過調整顏色的 Bootstrap 版本，可以套用在已有的 Bootstrap 網站上

