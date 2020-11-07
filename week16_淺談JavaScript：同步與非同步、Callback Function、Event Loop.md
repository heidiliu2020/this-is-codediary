###### tags: `JavaScript` `callback` `eventloop`
# [week 16] 淺談 JavaScript：同步與非同步 & Callback Function & Event Loop

本篇為 [JavaScript 中的同步與非同步（上）：先成為 callback 大師吧！](https://blog.huli.tw/2019/10/04/javascript-async-sync-and-callback/) 這篇文章的學習筆記。

## JavaScript 的執行環境

JavaScript 是一個程式語言，會有程式語言本身規範能用的東西，例如 var 宣告變數，if else 判斷式，或者是使用 function 宣告函式。

但如果是使用 DOM 物件操作 body 並反映在瀏覽器畫面上，這其實是由瀏覽器提供，讓 JavaScript 能透過 document 這個物件與瀏覽器進行溝通。

其他還有像是用來計時的 setTimeout 與 setInterval，拿來做 AJAX 的 XMLHttpRequest 與 fetch，這些都是瀏覽器這個執行環境所提供的東西。

除了瀏覽器以外，Node.js 其實也是 JavaScript 的一個執行環境，而非單純提供套件的 Library。如同官網所言：

> Node.js® is a JavaScript runtime built on Chrome’s V8 JavaScript engine.

### 瀏覽器 vs Node.js

JS 程式碼可以選擇跑在瀏覽器上，透過瀏覽器這個執行環境提供的東西操控畫面，或是發送 Request。

也可以選擇跑在 Node.js 這個執行環境上面，就可以利用 Node.js 提供的東西，例如使用 file system（控制檔案的介面），或是利用 http這個模組來寫 server。

可參考下方示意圖：

![](https://i.imgur.com/nP7n8as.png)

也就是說，執行環境不同，能夠使用的能就不同，執行方式也會不同。

以瀏覽器來說，是用 `<script src="index.js">` 引入一個 JavaScript 檔案，然後就可以在瀏覽器上執行。

以 Node.js 來說，則必須先在電腦上安裝 Node.js 這個執行環境，然後以 CLI 的方式輸入 `node index.js` 這個指令來執行。


## 阻塞與非阻塞

前面有提過 Node.js 有提供控制檔案的介面，讓我們能夠寫 JavaScript 程式碼來讀取與寫入檔案。

以下方程式碼作為範例：

```javascript=
const fs = require('fs')
const file = fs.readFileSync('./README.md') // 在這邊等好幾秒才往下執行
console.log(file)
  
const arr = []
for (let i = 2; i <= 99999999; i+=2) {
  arr.push(i)
}
  
console.log(arr)
```

上面這段程式碼，意思是先引入 Node.js 提供的內建模組 fs，再來使用 fs.readFileSync 來讀取檔案，最後把檔案的內容用 console.log 給印出來。

但因為程式碼在讀取檔案之前會卡住，必須等到執行完畢並且拿到回傳值為止，程式才會繼續往下執行，這種情況就稱為阻塞（blocking）。

難道不能用非阻塞（non-blocking）的方式讀取檔案嗎？有的！我們能使用 readFile 的 method 來進行。以下為程式碼範例：

```javascript=
// 讀取內建 fs 模組
const fs = require('fs')
  
// 定義讀取檔案完成以後，要執行的 function
function readFileFinished(err, data) {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}
  
// 讀取檔案，第二個參數是 callback function
fs.readFile('./README.md', readFileFinished);

```

可發現 readFile 的用法跟 readFileSync 差不多，但兩種讀取檔案的方式差別在於：

#### readFileSync

- 有回傳值，回傳值就是檔案內容
- 必須等到回傳後才能繼續往下執行，也因此會造成阻塞

#### readFile

- 多了一個參數，而且要傳進參數的是一個 function
- 執行完即可跳到下一行，檔案讀取完畢以後會把結果傳進 callback function

而這同時也是 blocking 與 non-blocking 的差別，在不討論 JavaScript 的其他層面來說：

#### 阻塞（blocking）

代表執行時程式會卡住，會直接回傳結果，因此必須等檔案讀取完畢才能執行下一行。例如 readFileSync。

#### 非阻塞（non-blocking）

代表執行時不會卡住，但執行結果不會放在回傳值，而是透過回呼函式（callback function）來接收結果。例如 readFile。

## Callback function

callback function 的意思其實就是：「當某事發生的時候，請利用這個 function 通知我」。

通常 callback function 會簡化成匿名函式（Anonymous function）的寫法，也就是直接在第二個參數的地方宣告一個 function。

常見的形式會像這樣：

```javascript=
// 讀取內建 fs 模組
const fs = require('fs')
  
// 讀取檔案
fs.readFile('./README.md', function(err, data) {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
});
```

在使用 callback function 時，需注意常見的錯誤：
- 傳進去的參數是 callback function，是一個「function」，而不是 function 執行後的結果
- 舉例來說，fn 是一個 function；fn() 則是執行 function，並且會把執行完的回傳結果當作 callback function

這裡以 setTimeout() 來作為範例：

```javascript=
// 正確
setTimeout(2000, tick)
function tick() {
  alert('時間到！')
}

===以下是錯誤範例===

// 錯誤
setTimeout(2000, tick())
function tick() {
  alert('時間到！')
}

// 上面錯誤會等同於
let fn = tick()
setTimeout(2000, fn)
function tick() {
  alert('時間到！')
}

```

## Node.js 上的同步與非同步

其實到這邊已經差不多把同步與非同步的觀念講完了。而在 [Node.js 的官方文件](https://nodejs.org/en/docs/guides/blocking-vs-non-blocking/)是這麼說的：

> Blocking methods execute synchronously and non-blocking methods execute asynchronously.

阻塞的方法會同步地（synchronously）執行，而非阻塞的方法會非同步地（asynchronously）執行。

因此，當我們討論到 JavaScript 的同步與非同步問題時，基本上可以把非同步跟非阻塞劃上等號。

如果執行一個同步的方法（例如 readFileSync），就一定會阻塞；如果執行一個非同步的方法（例如 readFile），就一定不會阻塞。

也就是說，其實我們可以直接把上段重點改成：

- 同步（synchronous）：代表執行時程式會卡住，會直接回傳結果，因此必須等檔案讀取完畢才能執行下一行。例如 readFileSync
- 非同步（asynchronous）：代表執行時不會卡住，但執行結果不會放在回傳值，而是透過回呼函式（callback function）來接收結果。例如 readFile

## 瀏覽器上的同步與非同步

前面是以 Node.js 當做例子，再來回到我們熟悉的瀏覽器。

假設我們今天在 JavaScript 有一個函式叫做 假設我們有一個函式叫做getAPIResponse，能後和後端 API 串接拿取資料。

同步版本：

```javascript=
const response = getAPIResponse()
console.log(response)
```

JavaScript 執行後會一直等到回傳後，才會繼續執行下一行程式碼。假如今天要等到 10 秒後，API Server 才會回傳資料，這段期間等於說讓執行 JavaScript 的執行緒（thread）凍結。

在瀏覽器裡面，負責執行 JavaScript 的叫做 main thread，負責處理跟畫面渲染相關的也是 main thread。換句話說，如果這個 thread 凍結 10 秒，會造成無法點擊畫面，因為瀏覽器沒有資源去處理其他事情。

而瀏覽器裡執行 JavaScript 的 main thread 同時也負責畫面的 render，因此非同步顯得更加重要而且必須，否則等待的時候畫面會凍結，就像電腦當機了一樣。

那麼依照之前所學的，把程式碼改成非同步，也就是利用 callback function 來接收結果。這裡有三種寫法，解果都會相同：

```javascript=
// 範例一：額外宣告函式
function handleResponst() {
  console.log(response)
}
getAPIResponse(handleResponst)
  
// 範例二：匿名函式
getAPIResponse(function(err, response) {
  console.log(response)
})
  
// 範例三：利用 ES6 箭頭函式簡化過後
getAPIResponse((err, response) => {
  console.log(response)
})
```

---

## Event Loop

> 推薦影片：[What the heck is the event loop anyway? | Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
> 影片中的 Demo：[Event Loop 流程](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

因為 JavaScript 是單執行緒（single thread）的程式語言，用白話文解釋就是「JavaScript 一次只能做一件事情」，因此 JS 中等待執行的任務會被放入 Call Stack。

JavaScript 在調用一個執行環境時，其實會經過兩個階段：

1. 建立階段：呼叫堆疊（Call Stack）

當開始執行 JavaScript 時，會先進入 Global EC。直到我們呼叫一個 function 時，才會建立一個新的執行環境繼續往 Global EC 的上層依序堆疊。

而尚未被執行的任務會進入 call stack，裡面會放著每個 function 執行時需要用到的資源，以及記錄 function 執行的順序。

這些過程都發生在開始執行內部程式碼之前，也就是建立階段。對 JS 引擎來說，屬於執行前的編譯階段，而 hoisting 就是在此階段進行處理。

2. 執行階段：執行堆疊（Execution Stack）

接著 JavaScript 會優先處理執行堆疊中最上面的執行環境。一旦執行完該 function 後，該執行環境就會從最上面被移除（pop off），同時儲存在該 function 中的資訊也會被銷毀，然後再回到之前的執行環境，直到回到 Global EC 為止。

可參考下方示意圖：

![](https://i.imgur.com/SiRxsOL.png)
（參考來源：https://dev.to/ahmedtahir/what-is-the-execution-context-execution-stack-scope-chain-in-js-26nc）

但 call stack 能夠存取的內容是有限的，一旦超出 stack 負荷就有可能出現錯誤 stack overflow。例如遞迴呼叫一個 function 十萬次，因為 stack 沒辦法存這麼多東西，就會拋出錯誤。

既然 JavaScript 同一個時間只能執行一件事，那要如何達到非同步操作呢？回到先前談的執行環境。

這裡以 setTimeout 為例：

setTimeout(fn, 2000) 只是在告訴瀏覽器說：「2 秒以後幫我呼叫 fn 這個 function」，瀏覽器就可以開另外一個 thread 去計時，而不是利用 main thread 做這件事。

當這些其他 thread 的事情做完以後，要再重新丟回 main thread 執行 JavaScript，這就是 Event Loop 在做的事情。

可參考這張 Event Loop 示意圖：

![](https://i.imgur.com/F4oAQmS.png)

（圖片來源：[Understanding Event Loop, Call Stack, Event & Job Queue in Javascript](https://medium.com/@Rahulx1/understanding-event-loop-call-stack-event-job-queue-in-javascript-63dcd2c71ecd)）

以執行 setTimeout(fn, 2000) 這行程式碼來說，步驟可想像成：

1. 執行 setTimeout(fn, 2000)，先把 setTimeout(fn, 2000) 丟到 call stack 去執行
2. 因為 setTimeout 屬於 Web API，會告訴瀏覽器「設定計時，經過 2000 毫秒以後呼叫 fn」
3. 執行結束，從 call stack 移除任務
4. 當瀏覽器計時 2000 毫秒後，會呼叫 fn 並丟到 callback queue 等待執行

接著是 Event Loop，藉由這個機制不斷偵測 call stack 是否為空，如果是空的，就把 callback queue 裡面的東西丟到 call stack。

Event Loop 其實有 loop（迴圈）概念在，可以表示成這樣：

```javascript=
while(true) {
  // 如果 call stack 為空 且 callback quere 有排程
  if (callStack.length === 0 && callbackQueue.length > 0) {
    // 拿出 callbackQueue 的第一個元素，並放到 callStack
    callStack.push(callbackQueue.dequeue())
  }
}
```

簡單來說，非同步的 callback function 會先被放到 callback queue，並且等到 call stack 為空時候才被 Event loop 丟進去 call stack，真正負責執行的還是 JavaScript 的 main thread。

### 牛刀小試

再舉一個常見例子，下面程式碼中應該哪行會先被執行？

```javascript=
setTimeout(() => {
  console.log('零秒出手')
}, 0)
console.log('hello')
// hello
```

思考方式：

1. 在 0ms 後，callback function 會被放到 callback queue 中等待執行
2. 因為 call stack 還不是空的，這裡會先繼續執行 console.log('hello')
3. 執行結束，call stack 被清空
4. event loop 才會把 callback 放到 call stack
5. 執行 callback 裡面的 console.log('零秒出手')

因此輸出順序會先是 `hello`，然後才是 `零秒出手`。

---

## 結語

閱讀完這篇文章，原本對 callback 概念薄弱的自己，對同步與非同步又有了新的一層認識。

尤其是前幾天才剛學完有關 [Hoisting](https://hackmd.io/@Heidi-Liu/note-js201-hoisting) 的運作原理，瞭解什麼是執行環境以及執行堆疊以後，再回來看 Event Loop 似乎也更能夠理解當中的執行流程。

是說現在再次看到 Node.js 是 JavaScript 的 runtime（執行環境）這句話時，會想到 Execution context 中文也被翻成執行環境，但兩者指的對象不同。前者指的是「執行時系統」（run-time system）；後者指的是 JavaScript 在執行時會建立的環境，又可分為全域與函式執行環境。翻成中文的壞處就是容易撞名混淆，還是讓自己盡量去理解原文的意思。
