[![hackmd-github-sync-badge](https://hackmd.io/_q5WFK3LQL-JHMBnv07IzQ/badge)](https://hackmd.io/_q5WFK3LQL-JHMBnv07IzQ)
###### tags: `RxJS`
# 【學習筆記】RxJS - 封裝程式的藝術

[TOC]

> 本篇為 [JSDC 2017 - R201#D105​ RxJS - 封裝程式的藝術 By Jerry Hong](https://www.youtube.com/watch?v=f1KjK8irCbY) 這場演講的學習筆記。如有錯誤歡迎指正。

## What's RxJS？

有一種說法，我們可以把 RxJS 想像成 `Lodash for async.`，專門用來處理非同步行為。

或更精準的說法，RxJS 就是「一個透過 Observable 來組合各種非同步行為的 Library」。

但我們不是已經有 Promise 來處理非同步，為什麼我們還需要學 Observable 呢？

## Why we need RxJS？

以下方程式碼為例，這是用來實作無限滾動的程式碼：

![](https://i.imgur.com/wXgT9xs.png)

這段程式碼的邏輯大致如下：

+ 註冊滾動事件：因為是無限滾動，我們需針對 Scroll 監聽事件。
+ 判斷滾動高度：透過 event 拿到 DOM 物件，即可計算出使用者目前 Scroll 頁面多少 %。
+ 發送 Request：當使用者 Scroll 超過 90%，就讓程式發送 Request，取得文章資料並顯示在畫面上。

但這種寫法其實有些問題，每當使用者持續 Scroll 到 90% 時，會不斷發送同樣的 Request，此時畫面就會顯示許多重複的資料。

### 一般解法：設定 Flag

我們可以透過「設定 Flag（標記）」來解決上述遇到的問題。在範例中，就是藉由判斷 isRequesting 的布林值，來決定要不要發送 Request：

+ isRequesting 初始值為 false，代表沒有發送請求，當 isRequesting 為 false 時才會發送 Request。
+ 發送 Request 前，設定 isRequesting 為 true。
+ 接收到 Response 後，設定 isRequesting 為 false。

### 使用 Promise 的限制

但是這段複雜的程式碼，其實隱藏一些問題：

+ 兩個非同步行為 Scroll 事件 & 發送 request
+ 同樣是非同步行為，卻使用不同的 pattern：Callback pattern & Promise
+ 由於 Promise 無法處理 Scroll 等事件，因此必須用兩種不同的 pattern
+ 假設需求改成：接收到 fetch 資料，經過幾秒後再顯示訊息，則必須透過設定 setTimeout

Promise 應該是為了解決 Callback 產生的問題，卻無法改善上述情形。

此外，這是在只設置一個 Flag 的情況下，假如需求不斷增長，必須設定更多的 Flags 時，程式碼將會變得更加複雜⋯⋯

> 我們一定要寫這麼醜的程式碼嗎？

### VanillaJS vs RxJS

以下分別是使用原生 JavaScript 和 RxJS
所撰寫的程式碼：

![](https://i.imgur.com/QXTxO4G.png)

可以很明顯看出，後者 RxJS 的寫法，即使不知道這些方法的實際用途，卻也能從語意大概猜出每個 function 的功能，大大提高程式碼可讀性。

---

## What is Observable？

想要瞭解 RxJS，就必須先從 Observable 談起。簡單來說，Observable 就是一個集合，再加上時間的維度：

```
Obervable = Collection + Time
```

再更白話一點，Observable 就像是一個「序列」，裡面的元素會隨著「時間推移」。

### Observable 建立與訂閱

+ 透過 Observable.fromEvent() 建立一個監聽 mousemove 事件的 observable：

```typescript=
// mouseMove 這個變數就是一個 observable
var mouseMove = Observable
                  .fromEvent(DOM, 'mousemove');
```

+ 接著就可以訂閱這個 observable，執行 mouseMove.subscribe() 時，就會傳入的 function：

```typescript=
// 訂閱 mouseMove 這個 observable
var subscription = mouseMove
                    .subscribe(x => console.log(x));          
```

+ 最後可透過 unsubscribe() 來移除這個監聽事件：

```typescript=
subscription.unsubscribe(); 
```

### 更多建立 Observable 的方法

以下舉幾個常見的方法，用來建立 Observable：

```typescript=
// 同步送出 2, 3, 4 後馬上結束
Observable.of(2, 3, 4);

// 與第一種方式相同，可同步送出 2, 3, 4 
Observable.from(2, 3, 4);

// from 可接收 Promise 物件，並轉成 Observable
Observable.from(fetch('url'));

// 有提供 ajax 方法，直接把 http request 包成一個 Observable
Observable.ajax('url');

// 透過 fromEvent 來監聽事件
Observable.fromEvent(DOM, 'click');

// 代表每 1000 毫秒，送出一個從 0 開始遞增的值
Observable.interval(1000);
```

### 觀念整理：observable / operator / oberver / subscription

這裡快速整理有關 observable 和 operators 的幾個重點：

#### / observable /

+ 是 Observable 的物件實例
+ 在尚未被訂閱之前，只是記憶體中的一個物件，不會送出元素
+ 可被訂閱（subscribe）
+ 具有多種運算子（operators）

#### / operators /

+ 是 Observable 的方法
+ 可對元素進行運算處理
+ 永遠回傳一個新的 observable（但有例外）

以下方程式碼為例，其中 map 和 filter 就是 operators，用法其實就和 JavaScript 用來處理陣列的 map 和 filter 類似，只是對象換成了 Observable：

```typescript=
var sub = Observable
            .from([1, 2, 3])
            .map(x => x + 1)          // 回傳一個新的元素
            .filter(x => x % 2 ===0)  // 過濾出想要的元素
            .subscribe({
              next: x => console.log(x),
              error: err => {},
              complete: () => {},
            });
```

#### / observer /

+ 用來訂閱 obervable 的物件
+ 具有三個方法：next, error, complete
  + next 為必要，error 與 complete 則是可選

#### / subscription /

+ 在 observable 訂閱後回傳的物件
+ 可用來退訂（unsubscribe），observable 就不會再執行其他動作
+ 可以和其他訂單合併，一直執行退訂動作

### 舉個例子：生產線

![](https://i.imgur.com/5BLZJz0.png)

+ observable 的元素：生產線上的產品
+ operator：生產線上的機具，用來包裝或篩選產品
+ observer：啟動生產線的人員


## Marble Diagram 彈珠圖

由於 Observable 具有時間的維度，因此我們常會使用圖像的方式來表達，也就是彈珠圖（Marble Diagram）。

### 呈現方式

+ `-`：由左向右代表時間推移
+ `n(0-9/a-z)`：送出的元素（next）
+ `|`：送出結束（complete）
+ `#`：送出錯誤（error）
+ `()`：同步送出

```
---0---1---2---3--
---0---1---2---3|
---0---1---2---3#
```

以下舉幾個範例：

+ `Observable.of(1, 2, 3)`：同步送出後馬上結束

```javascript=
(123|)
```

+ `Observable.interval(10)`：每隔 10 毫秒送出，注意 0 也會占一格

```javascript=
-01234...
```

+ `Observable.fromEvent(DOM, 'click')`：監聽 click 事件，當使用者點擊時才會發送 event 物件

```javascript=
---e--ee-e--...
```

### 綜合範例

#### 例一：take & map & filter

```javascript=
Observable.interval(10)   // 每隔 10 毫秒送出
  .take(3)                // 只取前三個元素
  .map(x => x + 1)        // 將每個元素加 1
  .filter(x => x/2 === 1) // 篩選出除 2 餘數等於 1 的元素
```

彈珠圖如下：

```javascript=
-01234...
-01(2|)
-12(3|)
-1-(3|)
```

#### 例二：takeUntil & fromEvent

```javascript=
Observable.interval(20)   // 每隔 20 毫秒送出
.takeUntil(      // 當某件事情發生時 Observable 會結束
  Observable
    .fromEvent(DOM, 'click')
);
```

彈珠圖如下：

```javascript=
--0-1-2-3-4-5...
-------e--------

--0-1-2|          // 當點擊事件發生時馬上中止
```


#### 例三：map & margeAll => mergeMap => switchMap => exhaustMap

+ map & margeAll

```javascript=
Observable
  .fromEvent(DOM, 'click')
  .map(() => 
    ajax('url...'))
  .margeAll();             // 攤平變成一維
);
```

彈珠圖如下：

```javascript=
-----e-e----   // 使用者點擊兩次
-----o-o----   // 將 event 物件轉成 request 物件，同樣是個 observable
     \ \
     \ ----r|
     ----r|

---------r-r-  // 收到的 response
```

如果不使用 margeAll 就直接訂閱結果，會因為 Observable 裡面還有 Observable，而無法拿到實際的 response，因此需要先將二維的 Observable 攤平，再繼續往下送結果。

+ map + margeAll => 可簡寫成 mergeMap

```javascript=
Observable
  .fromEvent(DOM, 'click')
  .margeMap(() => 
    ajax('url...'))
);
```

+ switchMap => 當第一個 Observable 尚未處理完，又送出第二個 Observable 時，取消舊的訂閱，只想保留新的結果

```javascript=
Observable
  .fromEvent(DOM, 'click')
  .switchMap(() => 
    ajax('url...'))
);
```

彈珠圖如下：

```javascript=
-----e-e----
-----o-o----
     \ \
     \ ----r|    // 訂閱後進來的 Observable
     --!      // 退訂原本的 Observable

-----------r-    // 只保留後面的 response
```

+ exhaustMap => 當第一個 Observable 尚未處理完，又送出第二個 Observable 時，不管新的，只想保留舊的結果

```javascript=
Observable
  .fromEvent(DOM, 'click')
  .exhaustMap(() => 
    ajax('url...'))
);
```

彈珠圖如下：

```javascript=
-----e-e----
-----o-o----
     \ \
     \ ----r|    // 不會訂閱後進來的 Observable
     ----r|    // 訂閱舊的 Observable

---------r---  // 只保留舊的 response
```


### 【範例】無限滾動

接著回到一開始的無限滾動，使用 RxJS 語法邏輯如下：

```javascript=
Observable.fromEvent(scrollView, 'scroll')  // scroll 事件
  .map(event => event.target)        // 轉成被 scroll 的 DOM 物件
  .map(hasScrolled)                  // DOM 物件被 scroll 了幾 %
  .filter(percent => percent > 0.9)  // scroll 超過 90% 就繼續後面動作
  .exhaustMap(() => fetch('url...')) // 在第一個 Observable 處理完之前，不會處理訂閱的 Observable
  .take(3)    // 當接收到 3 次 response，這個 Observable 就直接 complete，不會再送出元素
  .subscribe(res => {
    // Do sometheing to change view
  })
);
```


## Make Your Code Clean

在熟悉如何使用 RxJS 語法之後，能夠提升程式碼可讀性；但，實際上真是如此嗎？

隨著程式碼以及需求漸趨複雜，就算使用 Observable，還是可能會寫出不易閱讀的程式碼，這時我們需要思考的，是如何讓簡潔化我們寫的程式碼。

也就是如何達成 Code Clean 的三大重點：

+ Readable 可讀的
+ Composable 可組合的
+ Testable 可測試的

### (1) Readable 可讀的

### 【範例】`scrollOverNinePercent` & `getPostObservable`

  1. 抽取出有意義的、可能被多次使用的 Observable，並 assign 給一個變數：`$scroll`

![](https://i.imgur.com/02pG8l2.png)

2. 抽取出共用的 Operator，並命名為一個 function：`scrollOverNinePercent()`

![](https://i.imgur.com/nVzS445.png)

3. 為了避免出現多個小括號，可使用 `let` operator，即可取得 function 的回傳值

![](https://i.imgur.com/oYZpodN.png)

4. 將共用 function 放到獨立的檔案

![](https://i.imgur.com/9YHOHSi.png)

5. 抽離 Observable creator，也就是抽出「有建立新的 Observable」的程式碼：`getPostObservable()`

![](https://i.imgur.com/TFVZKyO.png)

修改後的程式碼如下，透過簡化邏輯，提高了程式碼可讀性：

```javascript=
import { scrollOverNinePercent } from '...';
import { getPostObservable } from 'xxx';

Observable.fromEvent(scrollView, 'scroll')  // scroll 事件

scroll$
  .let(scrollOverNinePercent)    // 判斷是否滾動超過 90%
  .exhaustMap(getPostObservable) // 抓取文章 API
  .subscribe(res => {
    // Do sometheing to change view
  });
```

### (2) Composable 可組合的

可組合的意思並不在於是否可重用，重點是「能夠和其他程式碼組合，來針對不同情境使用」。

在 RxJS 中，盡可能使用 Observable 來撰寫程式碼，因為 Observable 本身就是可組合的！

### 透過 Pipe 來組合多個 Operators

在演講中，有提到 RxJS 5.5 版本發佈了新特性：lettable operator，但其實 lettable 這個詞，後來又被改為 pipeable，因此後續皆以 Pipeable Operators（操作符）稱呼。

`pipe` 是 Observable 的一部分，不需導入即可使用，可代替 `let` 操作符，用來組合 Operators，傳入的 Observable 會回傳新的 Observable，再繼續往下傳遞。

```javascript=
source$.let(myOperator) 

source$.pipe(myOperator)
```

要把 Operators 從 Observable 中獨立出來，必須解決 Operator 名稱和 JavaScript 的關鍵字衝突問題。

因此對部分 Pipeable Operators 名稱進行修改：

+ do -> tap
+ catch -> catchError
+ switch -> switchAll
+ finally -> finalize

> 詳細可參考這篇文章：[[譯] Pipeable 操作符](https://read01.com/zh-tw/nxd4yzJ.html#.YV0CRp4zZTY)

### 【範例】`scrollOver` & `onErrorReturn`

#### 1. `scrollOverNinePercent()` => `scrollOver()`

還記得我們剛才寫的 `scrollOverNinePercent()` 嗎？實際上，這個 Function 並不易用來重組，因為 scroll 觸發時機點不一定會在 90%。

![](https://i.imgur.com/9Co9BLb.png)

這時可透過 [Higher Order Function](https://zh.wikipedia.org/wiki/%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0) 解決，也就是用一個 Function 回傳另一個新的 Function，作法如下：

1. 在第一個 Function 傳入 `criticalP`（代表臨界點），取代原本 0.9 的位置 

![](https://i.imgur.com/nC7dCI6.png)

2. 接著可使用 Pipeable Operators，也就是改用 `pipe` 來組合多個 operator，傳入的 Observable 會回傳新的 Observable，再繼續往下傳遞

![](https://i.imgur.com/dec0xAI.png)

修改後的程式碼如下，讓 scrollOver() 的使用更加彈性，同時也透過 pipe 來簡化程式碼邏輯：

```javascript=
import { scrollOver } from '...';
import { getPostObservable } from 'xxx';
import { exhaustMap } from 'rxjs/operators';

Observable.fromEvent(scrollView, 'scroll')  // scroll 事件

scroll$
  .pipe(
    scrollOver(0.9),
    exhaustMap(getPostObservable),
  )
  .subscribe(res => {
    // Do sometheing to change view
  });
```

#### 2. `getPostObservable()` => `onErrorReturn()`

1. 取得文章的 Function，可能會需要進行錯誤處理，例如透過 `retry` 來重新發送

```javascript=
const defaultData = { success: false, data: []};

const getPostObservable = () => 
  Observable.ajax('url...')
    .retry(3);        // 當文章取得失敗時，會重新嘗試三次後，還是失敗就拋出例外
    .catch(() => Observable.of(defaultData));  // 如發送三次仍失敗，則回傳預設值
```

2. 經過簡化改回傳 Array，Array 會自動被轉成 Observable：

```javascript=
const defaultData = { success: false, data: []};

const getPostObservable = () => 
  Observable.ajax('url...')
    .retry(3);
    .catch(() => [defaultData]);
```

3. 同樣改用 pipe 來組合運算子

```javascript=
import { retry, catError } from 'rxjs/operators';

const defaultData = { success: false, data: []};

const getPostObservable = () => 
  Observable.ajax('url...')
    .pipe(
      retry(3),
      catchError(() => [defaultData]),
    )
```

4. 也可以試著自己定義一個 Pipeable Operators：`onErrorReturn`

```javascript=
import { retry, catError } from 'rxjs/operators';

const defaultData = { success: false, data: []};

const onErrorReturn = defaultData =>
  catchError(() => [defaultData]);

const getPostObservable = () => 
  Observable.ajax('url...')
    .pipe(
      retry(3),
      onErrorReturn(defaultData),
    )
```

5. 再把 `onErrorReturn` 放到獨立的檔案，這段邏輯即可被重用

```javascript=
import { retry, catError } from 'rxjs/operators';
import { onErrorReturn } from '...';

const defaultData = { success: false, data: []};

const getPostObservable = () => 
  Observable.ajax('url...')
    .pipe(
      retry(3),
      onErrorReturn(defaultData),
    )
```

### 【其他應用】物件拖拉 vs 取消請求

這裡舉 RxJS 的經典案例「物件拖拉（Drag & Drop）」，可拆解成以下步驟：

1. 使用者對目標物按下滑鼠左鍵：mouseDown$
2. 使用者移動滑鼠：mouseMove$
3. 直到放開滑鼠左鍵：mouseUp$

若以程式碼表示：

```javascript=
mouseDown$
  .switchMap(() =>  // 將監聽對象 mouseDown$ 轉成 mouseMove$
    mouseMove$.takeUntil(mouseUp$))  // 直到 mouseUp$ 時結束
  .subscribe(value => {
    // do something
  });
```

再舉另一個「點擊 Neflix 影片清單後，馬上取消請求」為例，動作可拆解如下：

1. 使用者點擊影片方塊：mouseClick$
2. 發送 Request 讀取影片資訊：request$
3. 如果在尚未接收到 Response 之前，就按下取消關閉視窗：cancel$

程式碼如下：

```javascript=
mouseClick$
  .switchMap(() => 
    request$.takeUntil(cancel$))
    .subscribe(value => {
    // do something
  });
```

嗯？這段程式碼怎麼好像似曾相識？

把剛才的兩個範例拿來比對：

![](https://i.imgur.com/yhKwbV5.png)

可以發現到，程式碼竟然有 87% 像！因為兩段邏輯其實極為類似，都是在「做某件事情後，再做另一個非同步行為」，而這個非同步行為，會在某個時間點、發生某件事情時而被取消。

既然是相同邏輯，何不重用乎？


### (3) Testable 可測試的

程式碼寫得好壞，通常是和「是否可進行測試」畫上等號的。因為能夠被測試的程式，才能被檢驗程式的品質。

然而，在非同步的程式碼，其實是不易去撰寫測試的。

通常在撰寫非同步測試時，會有以下兩種方法：

+ 模擬非同步測試
  + 例如：使用 setTimeout 去模擬等待時間；或透過 [Jest](https://en.wikipedia.org/wiki/Jest_(JavaScript_framework)) 模擬非同步行為，可參考這篇討論：[_.debounce() breaks fake timers #2893](https://github.com/lodash/lodash/issues/2893)
  + 情境複雜不易模擬，且容易出錯
  + 出錯時不知道要找誰：Lodash 提供非同步行為 & Jest 提供時間模擬
+ 精確非同步測試
  + 例如：真的等待需要的時間
  + 測試會花費過多的時間

那麼，RxJS 是如何解決非同步測試遇到的問題呢？

### 問題解決：Marble Testing 彈珠測試

還記得我們在前面提到的彈珠圖嗎？我們可透過彈珠圖來撰寫非同步測試：

+ 用 Marble Diagram 撰寫測試
  + 只要會畫圖就能寫測試
+ 100% 可重複的測試
+ RxJS 提供時間模擬，同時也提供測試方法
  + 不需花時間等待測試結果

以下是之前舉的彈珠圖範例：

![](https://i.imgur.com/s3kEdzR.png)

撰寫測試如下，透過預知最後結果的彈珠圖，我們可用來進行單元測：

![](https://i.imgur.com/asnKEUk.png)

+ 更多 Unit Test 教學，可參考這篇：[如何「畫圖」寫測試 - RxJS Marble Test](https://blog.jerry-hong.com/speaking/rxjs-marble-testing/)


## 結語

> Programming is thinking, not typing. -- Cassy Pottan

我們在撰寫程式時，重要的應該是如何去思考，而非單純敲打文字。

> Be a Programmer, not just a Coder.

當一個有想法的開發者，而不只是一個碼農。


時隔半年再來重新回顧 RxJS 的演講，一開始還不太懂 pipe 和 subscribe 使用時機？Operators 之間的差別？Observable 到底好用在哪？

直到陸續在工作中實際應用，儘管偶爾和是會寫出和自己預期不符的程式碼，踩過一個又一個坑，累積不少經驗值之後，終於逐漸理解 Observable 的神奇魔力，期許自己未來能夠掌握 RxJS，靈活應對各種非同步行為。

## 參考資料

+ [RxJS - 封裝程式的藝術 - 演講簡報](https://blog.jerry-hong.com/speaking/rxjs-art/)
+ [btroncone/learn-rxjs](https://www.learnrxjs.io/)
+ [RxJS-CN/learn-rxjs-operators](https://github.com/RxJS-CN/learn-rxjs-operators)
+ [30 天精通 RxJS](https://ithelp.ithome.com.tw/users/20103367/ironman/1199)
+ [希望是最淺顯易懂的 RxJS 教學](https://blog.techbridge.cc/2017/12/08/rxjs/)
