###### tags: `JavaScript` `API`
# [week 7] DOM 事件傳遞機制：捕獲與冒泡、事件代理

> 本篇為 [[FE102] 前端必備：JavaScript](https://lidemy.com/p/fe102-javascript) 這門課程的學習筆記。如有錯誤歡迎指正。

當我們運用 JavaScript 在網頁進行操作時，主要可分為下列三大面向：

1. 介面（Interface）：如何改變介面
2. 事件（Event）：如何監聽事件並做出反應
3. 資料（Data）：如何和伺服器交換資料

> 以下主要探討「事件傳遞機制」的部分。

## 事件傳遞機制

在開始解釋前，先以下列程式碼為範例：

```htmlmixed=
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>事件傳遞機制</title>

<style>
  .outer {
    width: 200px;
    height: 200px;
    background-color: orange;
  }

  .inner {
    width: 100px;
    height: 100px;
    background-color: lightseagreen;
  }
</style>
</head>
<body>
  <div class="outer">
    <div class="inner">
      <button class="btn">button</button>
  </div>
</div>
<script>
  addEvent('.outer');
  addEvent('.inner');
  addEvent('.btn');
  
  // 監聽按鈕點擊事件
  function addEvent(className) {
    document.querySelector(className)
      .addEventListener('click', function () {
        console.log(className);
      })
  };
</script>
</body>
</html>
```

若在區塊都加上監聽 click 事件，會發現點擊內部區塊，同時也會點擊到外層區塊：

- 點擊 outer 會觸發 outer
- 點擊 inner 會觸發 inner ➡️ outer
- 點擊 button 會觸發 button ➡️ inner ➡️ outer

![](https://i.imgur.com/6YDCa36.png)

由此可知當點擊內部節點，同時也會點擊到外層節點。

## 捕獲與冒泡

> 參考文章：[DOM 的事件傳遞機制：捕獲與冒泡](https://blog.techbridge.cc/2017/07/15/javascript-event-propagation/)

根據上述內容，可知 DOM 事件傳遞機制分成 3 階段：

```
1：Capturing Phase　捕獲階段
2：Target Phase     傳遞到元素本身
3：Bubbling Phase   冒泡階段
```

當我們觸發事件時，會從最外層的根結點開始往內傳遞到 target，也就是「捕獲階段」。接著會再由內往外回傳回去，稱為「冒泡階段」。

任何事件在傳遞時，都會按照這個順序下去傳遞。這也是為什麼，當觸發底層節點的事件同時，上層所有的節點也會被觸發。

![](https://i.imgur.com/zj2brCF.png)

> 可參考：[W3C -  event flow 的示意圖](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)

### 事件傳遞的兩個原則

1. 先捕獲，再冒泡
2. 當事件傳到 target 本身，沒有分捕獲跟冒泡

根據傳遞機制，我們不一定要把監聽的節點設在底層節點，只需設定在外層，就能監聽到所有底層節點的事件。


### 阻止事件傳遞 `e.stopPropagation`

當使用 `event.stopPropagation()`，事件傳遞就會停在設置的地方：

- 若在捕獲階段：阻止事件往下傳遞
- 若在冒泡階段：阻止事件向上傳遞

`event.stopPropagation()`這樣就可以阻止事件繼續往上冒泡，在父元素的監聽器就不會收到孫元素的事件傳遞。

```javascript=
document.querySelector('.btn').addEventListener('click',function(e){
   e.stopPropagation()
   console.log('btn 冒泡');
})
```

### 阻止事件傳遞 `stopPropagation()`

- 事件傳遞會停在設置的地方
- 例如：在 window（最上層）的捕獲階段設置 `event.stopPropagation()`，會阻止後續事件傳遞，造成所有 click 事件均失效。

```javascript
// 監聽 window 捕獲階段的 click 事件，執行函式內指令
window.addEventListener('click', function(e) {
  e.stopPropagation()
}, true)
```

### `stopImmediatePropagation()`

如果要讓同一層的事件也要停止，就用 `stopImmediatePropagation()`，這樣同一層只會觸發這個 listener。

## 容易搞錯的事件機制問題

### 迴圈與觸發時間非同步

首先要注意的，是「迴圈與觸發時間非同步」這件事。以下列程式碼為例：

```javascript=
// 在 html 新增兩個 button：
<body>
  <div class="outer">
    <button class="btn">1</button>
    <button class="btn">2</button>
  </div>

// JS 監控按鈕 click 點擊事件：
<script>
  // querySelectorAll 回傳的值是類陣列
  const btns = document.querySelectorAll('.btn')
  for (i = 0; i < btns.length; i += 1) {
    btns[i].addEventListener('click', () => {
      alert(i)
    })
  }
</script>
</body>
```

會發現兩個 button 跳出的結果都是 2，而非按照迴圈順序一一跳出相對應的數字。

這是因為 click 事件和迴圈是不一樣時間軸。click 只有在點擊瞬間才會執行，但迴圈會先跑完。因此順序是：click →跑完迴圈→觸發事件結果，所以會跳出一樣的數字。

我們可以把程式碼修改成：

```javascript=
// 在 html 新增兩個 button：
<body>
  <div class="outer">
    // 通常以 `data` 開頭是我們自訂的屬性
    <button class="btn" data-value="1">1</button>
    <button class="btn" data-value="2">2</button>
  </div>

// JS 監控按鈕 click 點擊事件：
<script>
  const btns = document.querySelectorAll('.btn')
  for (i = 0; i < btns.length; i += 1) {
    btns[i].addEventListener('click', (e) => {
      alert(e.target.getAttribute('data-value'))
    })
  }
</script>
</body>
```

- `e.target`：觸發到哪個元素，就可該元素的資料
- `getAttribute`：可得到 `data-value` 的值，也就是新增在 `button` 元素的屬性

### 動態新增問題

若想再新增按紐，後來新增的元素並無法擁有已預設好的 addEventListener 的功能：

```javascript=
<div class="outer">
  <button class="add-btn">add</button>
  <button class="btn" data-value="1">1</button>
  <button class="btn" data-value="2">2</button>
</div>

<script>
  // 從數字 3 繼續新增按紐
  let num = 3

  const btns = document.querySelectorAll('.btn')
  for (i = 0; i < btns.length; i += 1) {
    btns[i].addEventListener('click', (e) => {
      alert(e.target.getAttribute('data-value'))
    })
  }

  // 動態新增按紐
  document.querySelector('.add-btn').addEventListener('click', function() {
    const newBtn = document.createElement('button')
    newBtn.classList.add("btn")
    newBtn.setAttribute('data-value', num)
    newBtn.innerText = num
    num += 1
    
    document.querySelector('.outer').appendChild(newBtn)
  })
</script>
```

當按下 add 確實會新增 button 按鈕，並同樣賦予按鈕 class 與 attribute，但新增的按鈕卻沒有 `addEventListener` 的效果。

這是因為「程式只會執行一次」。也就是說，第一段的 `.querySelectorAll` 其實只包含原有的兩顆按鈕，後來新增的按鈕並不會再被加入。我們可以利用「事件代理」來解決動態新增的問題。

### 事件代理 `event delegation`

透過事件傳遞機制，我們可以直接對父元素（事件代理）進行事件監聽，就不需對子元素事件一個一個監聽。如此不只能提高效率，也能處理動態新增的問題。

如先前提到：當我們要新增按紐，若把監聽事件設在子元素，那麼新加入的子元素就必須另外處理。若使用 event delegation，直接父元素進行事件監聽，透過冒泡機制，事件會由父元素傳遞到底下的所有子元素。

因此我們可以將先前的程式碼改為：

```javascript=
<!-- 事件代理 -->
<div class="outer">
  <button class="add-btn">add</button>
  <button class="btn" data-value="1">1</button>
  <button class="btn" data-value="2">2</button>
</div>

<script>
  let num = 3

  // 動態新增按紐
  document.querySelector('.add-btn').addEventListener('click', function() {
    const newBtn = document.createElement('button')
    newBtn.classList.add('btn')
    newBtn.setAttribute('data-value', num)
    newBtn.innerText = num
    num += 1;
    document.querySelector('.outer').appendChild(newBtn)
  })
  // 不管有沒有進行事件監聽，事件捕獲冒泡機制都會進行
  // 新增監聽在父節點上
  document.querySelector('.outer').addEventListener('click', function(e) {
    // .classList.contains()：判斷是否包含該 class
    if (e.target.classList.contains('btn')) {
      alert(e.target.getAttribute('data-value'))
    }
  })
</script>
```


參考資料：[Introduction to Javascript in Front-End #2](https://github.com/RZ-Huang/About-Javascript/issues/2)