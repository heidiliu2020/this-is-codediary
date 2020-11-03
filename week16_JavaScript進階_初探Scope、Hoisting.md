###### tags: `JavaScript`  `Hoisting` `Execution Context` `Variable Object` `TDZ`
# [week 16] JavaScript 進階 - 初探 Hoisting & Execution Context

> 本篇為 [[JS201] 進階 JavaScript：那些你一直搞不懂的地方](https://lidemy.com/p/js201-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

  你知道 Hoisting（提升）是什麼
  你知道 Hoisting 的原理為何
  你知道 Hoisting 只會提升宣告而非賦值
  你知道 function 宣告、function 的參數以及一般變數宣告同時出現時的提升優先順序
  你知道 let 跟 const 其實也有 Hoisting，只是表現形式不太相同
```

---

## 什麼是 Hoisting？

如果我們試圖在 JavaScript 中，對一個尚未宣告的變數取值，會出現 `a is not defined` 的錯誤訊息：

```javascript=
console.log(a)
// ReferenceError: a is not defined
```

但如果在下面加上一行 `var a = 10`，也就是宣告變數 a，神奇的事就發生了：

```javascript=
console.log(a);
var a = 10;
// undefined

=== 經過 Hoisting ===

var a;
console.log(a);
a = 10;
// undefined
```

我們知道，在 JavaScript 中程式是一行一行執行的。但是在上方程式碼的情況下，console.log() 的變數 a 卻能夠先被宣告，然後輸出 undefined，可以想成下方的程式碼。

這種現象就叫做 Hoisting（提升），會發生在變數宣告。也就是說，當我們宣告一個變數時，宣告本身會被提升至程式碼最上面，而賦值則留在原處，因此也就不會出現 `a is not defined` 的錯誤。

或是以我們熟悉的宣告 function 為例，就算在宣告以前就先呼叫它，還是能夠順利執行：

```javascript=
test();

function test() {
  console.log(123);
}
// 123

=== 經過 Hoisting ===

function test() {
  console.log(123);
}

test();
// 123
```

因為整段 function 會被提升到上面，所以不管在哪呼叫 function 都能夠執行。

這在有些程式語言其實是做不到的，但在 JavaScript 進行宣告能夠做到提升，也使程式碼撰寫更加方便。

### 只有宣告會提升，賦值不會提升

但需注意的是，只有「宣告」會被提升到最上方，但「賦值」不會。

若宣告一個變數，並賦值為 function 時，以下方程式碼為例：

1. 若把呼叫改寫在 function 後面，能夠順利執行：

```javascript=
var test = function() {
  console.log(123);
}

test();
// 123
```

2. 若把呼叫改寫在 function 前面，則會出現「test 不是 function」的錯誤：

```javascript=
test();

var test = function() {
  console.log(123);
}
// TypeError: test is not a function

=== 經過 Hoisting ===

var test;
test();

test = function() {
  console.log(123);
}

```

之所以會出現錯誤，是因為即使經過提升先宣告變數 `var = test`，卻無法先賦值。因此呼叫的 `test()` 其實是 undefined 而非 function，所以執行就會顯示錯誤。

## Hoisting 的順序

### 情境一：同時宣告函式與變數

那如果同時宣告一個 function 和變數呢？

以下列程式碼為例：

```javascript=
console.log(a);   // [Function: a]
var a = 'global';

function a() {
  var a = 'local';
}
```

會發現答案既不是 global 也非 local，而是輸出 function。

這是因為除了宣告變數以外，function 的宣告也會提升，而且「**function 的提升會優先於變數的提升**」。

### 情境二：重複的函式

如果有兩個相同的 function，則會依照順序，提升比較後面宣告的 function：

```javascript=
function test() {
  a();
  function a() {
    console.log(1);
  };
  function a() {
    console.log(2);
  };
}

test();
// 2

=== 經過 Hoisting ===

function test() {
  /* 會優先提升後面的函式
  function a() {
    console.log(1);
  };
  */ 
  function a() {
    console.log(2);
  }
  a();
}

test();
// 2
```

### 情境三：傳入參數到 function

在探討參數之前，先來複習參數和引數的區別：

- 參數（Parameter）：是方法的宣告
- 引數（Argument）：用於呼叫函式

> 可參考：[引數(Argument) vs. 參數(Parameter) - NotFalse 技術客](https://notfalse.net/6/arg-vs-param)

因此，當我們傳入參數到 function 時，其實指的就是引數（argument），而 argument 的宣告會優先於被提升的變數宣告。

以下方傳入參數的程式碼為例：

```javascript=
function test(a) {
  console.log(a)
  var a = 456
}

test(123)
// 123

=== 經過 Hoisting ===

function test(a) {
  var a;      // 宣告變數就沒用了QQ
  console.log(a);
  a = 456;
}

test(123)    // argument 的宣告優先度較高
// 123
```

那如果同時宣告 function 和 argument 呢？宣告 function 會優先於 argument 的宣告：

```javascript=
function test(a) {
  console.log(a);
  function a() {
  };
}

test(123);
// [Function a]

=== 經過 Hoisting ===

function test(a) {
    function a () {  // 函式的宣告優先度較高
    };
    console.log(a);
}

test(123);  // 參數寫多少都沒用QQ
// [Function a]
```

### 情境四：宣告變數並賦值

但如果是在函式內「宣告變數並賦值」的話，結果就不相同了：

```javascript=
function test(a) {
  console.log(a);
  var a = 'hello'
  console.log(a);
  
  function a () {
    console.log(2);
  };
}

test(123)

=== 經過 Hoisting ===

function test(a) {
  function a () {     // 函式宣告提升
    console.log(2);
  }
  console.log(a);    // [Function: a] 
  
  var a = 'hello';    // 賦值
  console.log(a);     // hello
}

test(123);        // 參數還是沒用QQ
```

綜合上述幾個例子，可整理出有關 Hoisting 的幾個重點：

- Hoisting 的優先順序為：

1. 函式宣告（function）
2. 傳進函式的參數（argument）
3. 變數宣告（variable）

- 只有宣告會提升，賦值不會提升

### 小試身手

最後再來進行這個小測驗，判斷 log 值依序是什麼：

```javascript=
var a = 1;
function test(){
  console.log('1.', a);
  var a = 7;
  console.log('2.', a);
  a++;
  var a;
  inner();
  console.log('4.', a);
  function inner(){
    console.log('3.', a);
    a = 30;
    b = 200;
  }
}
test();
console.log('5.', a);
a = 70;
console.log('6.', a);
console.log('7.', b);
```

思考方式：

- `console.log('1.', a)`

先看執行函式 test()，因為函式內有宣告變數，所以會提升，得到 a = undefined。

- `console.log('2.', a);`

在函式 test()，賦值 a = 7。

- `console.log('3.', a);`

進入函式 inner()，函式內沒有宣告 a，所以會往上層 test() 宣告過 `var a = 7`，經過 a++ 得到 a = 8。

又因為外層沒有宣告過 b ，在呼叫函式 inner() 的同時，會宣告一個全域變數 b 並賦值為 200。

- `console.log('4.', a);`

前面呼叫 inner() 時，重新賦值變數 a 為 30，因此這時外層的 a = 30。

- `console.log('5.', a);`

看全域變數的 `var a = 1`，因此 a = 1。

- `console.log('6.', a);`

經過賦值 a = 70。

- `console.log('7.', a);`

前面提到在函式中，宣告了一個全域變數 b 並賦值為 200，因此 b = 200

---

## 所以為什麼需要 Hoisting？

至於我們為什麼會需要 Hoisting 呢？這個問題其實可以倒過來思考：「假如沒有 Hoisting 會怎麼樣？」

- 一定要先宣告變數才能使用

養成宣告的好習慣！這樣就不會汙染變數了，感覺還不賴？

- 一定要先宣告函示才能使用

這點其實會造成很大的不便，因為這樣在每個檔案都必須把 function 宣告放在最上面，才能保證底下的程式碼都可以呼叫這些 function。

- 無法達成 function 互相呼叫

什麼意思？舉個下面的例子：

```javascript=
function loop(n){
  if (n > 1) {
    logEvenOrOdd(--n);
  }
}

function logEvenOrOdd(n) {
  console.log(n, n % 2 ? 'Odd' : 'Even');
  loop(n);
}

loop(10);
```

因為有 Hoisting，我們才能在 loop() 函式中呼叫 logEvenOrOdd()，然後也在 logEvenOrOdd() 函式中呼叫 loop()。

如果沒有 Hoisting，那以上的程式碼就不可能達成，因為這牽涉到順序問題，不可能同時做到 A 在 B 上面而 B 又在 A 上面。

為了解決上述問題，所以我們需要 Hoisting。

延伸閱讀：
- [Note 4. Two words about “hoisting”.](http://dmitrysoshnikov.com/notes/note-4-two-words-about-hoisting/)
- [JavaScript系列文章：变量提升和函数提升](https://www.cnblogs.com/liuhe688/p/5891273.html)

---

## Hoisting 的運作原理

在瞭解什麼是 Hoisting，還有我們為什麼需要 Hoisting 之後，接著就來探討：「Hoisting 究竟是如何運作的？」

這部分可參考 JavaScript 所遵循的標準 [ECMAScript](https://zh.wikipedia.org/wiki/ECMAScript) 規格書，因為後期版本規格眾多，這裡以 [ES3 規格書](https://www-archive.mozilla.org/js/language/E262-3.pdf)當作範例。

其實在 ES3 的規則中，並出現沒有 Hoisting 這個詞，與此現象有關的段落出現在第十章：Execution Contexts（執行環境，以下部分內容簡稱 EC）。

### 什麼是 Execution Context？

在 JavaScript 中，可執行的 JS 程式碼可分成三種型別：

1. Global Code：全域性的，不在任何函式裡面的程式碼
2. Function Code：在自定義函式中的程式碼
3. Eval Code：使用 eval() 函式動態執行的程式碼，但因為有兼容性與安全問題，JavaScript 並不推薦使用，這裡先不討論

當 JavaScript 開始執行時，根據執行的程式碼型別，程式碼必須被執行在這三種環境之一，也就是 Execution Context（執行環境）。

以下要來探討的是全域執行環境和函式執行環境：

- 全域執行環境（Global Execution Context）
  - 當執行 JavaScript 時，會先建立的第一層執行環境
  - 位在最外圍的執行環境，且只會有一個
  - 其他執行環境都可存取全域的資料
- 函式執行環境（Function Execution Context）
  - 又稱為區域性執行環境（Local Execution Context）
  - 每個 function 都有自己的執行環境
  - 裡面儲存該 function 的相關資料，例如變數和函式定義
  - 每當呼叫一個 function 時，都會建立一個新的 local EC，並且被放到執行堆疊（Call Stack）最上面

![](https://i.imgur.com/BeuXQfQ.png)
（參考來源：https://medium.com/%E9%AD%94%E9%AC%BC%E8%97%8F%E5%9C%A8%E7%A8%8B%E5%BC%8F%E7%B4%B0%E7%AF%80%E8%A3%A1/%E6%B7%BA%E8%AB%87-javascript-%E5%9F%B7%E8%A1%8C%E7%92%B0%E5%A2%83-2976b3eaf248）

### 呼叫 & 執行堆疊（Call Stack）

因為 JavaScript 是[單執行緒](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B)的程式語言，用白話文解釋就是「JavaScript 一次只能做一件事情」，因此 JS 中等待執行的任務會被放入堆疊（Stack）

JavaScript 在調用一個執行環境時，其實會經過兩個階段：

1. 建立階段：呼叫堆疊（Call Stack）

前面有提到，當開始執行 JavaScript 時，會先進入 Global EC。直到我們呼叫一個 function 時，才會建立一個新的執行環境繼續往 Global EC 的上層依序堆疊。

這些過程都發生在開始執行內部程式碼之前，也就是建立階段。對 JS 引擎來說，屬於執行前的編譯階段，而 hoisting 就是在此階段進行處理。

2. 執行階段：執行堆疊（Execution Stack）

接著 JavaScript 會優先處理執行堆疊中最上面的執行環境。一旦執行完該 function 後，該執行環境就會從最上面被移除（pop off），同時儲存在該 function 中的資訊也會被銷毀，然後再回到之前的執行環境，直到回到 Global EC 為止。

可參考下方的執行環境流程示意圖：

![](https://i.imgur.com/gEIWBNG.png)
（參考來源：https://dev.to/ahmedtahir/what-is-the-execution-context-execution-stack-scope-chain-in-js-26nc）

最後整理關於執行環境的幾個重點：

- JavavaScript 是單執行緒的語言
- 且為同步執行：一次一個指令，而且有一定的順序
- Global Context 只會有一個
- Function Context 則沒有限制
- 就算是自己呼叫自己，只要有呼叫 function 就會建立執行環境

---

## Execution Context Object 執行環境物件

前面也曾提到，執行環境負責存放該環境需要用到的各種資料，我們可以把執行環境想像成一個物件，又被稱作執行環境物件（Execution Context Object）。

而每個執行環境物件會有下列三個屬性：

- 作用域鏈（Scope Chain）
- 變數物件（Variable Object）
- 'this' 變數（'this' Variable）

若以 Object 型態表示會如下：

```javascript=
executionContextObject = {
  scopeChain: { 
    // 變數物件 + 所有父代執行環境物件的變數物件
  },
  variableObject: { 
    // 函式的參數、內部的變數和函式
  },
  this: {
    //
  }
}
```

## Variable Object 變數物件（VO）

以下是 ECMA [10.1.3 Variable Instantiation](https://www.ecma-international.org/archive/ecmascript/1999/TC39WG/990220-es2_func.pdf) 對於 Variable Object 的解釋：

> Every execution context has associated with it a variable object. Variables and functions declared in the source text are added as properties of the variable object. For function, anonymous, and implementation-supplied code, parameters are added as properties of the variable object.

意思是說，每一個執行環境都會有一個相對應的變數物件（Variable Object），這個物件負責記錄執行環境中定義的變數和函式。

而執行 function 所創造的執行環境中，則會把參數（parameters）也加進去變數物件中，也就是活化成執行物件（Activation Object），這部分我們後面章節會再提到。

那麼，Variable Object 實際上是如何運作的呢？我們可從 JavaScript 執行流程談起。

從呼叫 function 到執行程式碼，這段過程中其實發生很多事情，流程大致如下：

1. 尋找呼叫 function 
2. 在執行 function 之前先建立執行環境
3. 進入建立階段
  - 初始化 Scope Chain
  - 建立 Variable Object
  - 判斷決定 this 的值
4. 執行階段
  - 一行一行執行程式碼，賦值 

其中，在建立 Variable Object 時，則會進行下列三件事：

- 建立 Argument Object：檢查執行環境的參數，初始化參數的名稱與值，若沒有值就初始為undefined
- 掃描 Function 宣告：為每一個 function 建立一個新屬性，其值指向該 function 在記憶體中的位置。如果已經有同名的就取代之
- 掃描變數宣告：為每一個變數宣告建立一個新屬性，並將該屬性的值初始為 undefined。如果已經有同名的就略過

以下舉簡單的範例進行說明。

### 範例：宣告變數 `var a = 123` 

這句會分成兩部分：

1. `var a`：如果 VO 裡沒有屬性 a，就會新增 a 並初始化成 undefined
2. `a = 10`：在 VO 裡找到叫做 a 的屬性，並設定為 10

VO 模型：

```javascript=
VO: {
  a: 123,   // 把變數放進 VO，初始化成 123
}
```

> 至於 VO 是如何找到屬性 a，則是會透過透 Scope chain 不斷往上層找，如果每一層都找不到就會出現錯誤訊息。這過程中涉及的部分較廣，因此我們先不進行討論。

### 範例：在 function 傳入一個參數 123

程式碼如下：

```javascript=
function test(a, b) {
  console.log(a, b);
}
test(123);
// 123, undefined
```

1. 把參數放到 VO，並初始化成 123
2. 若參數沒有值，就會被初始化成 undefined

VO 模型如下：

```javascript=
VO: {
  a: 123,   // 把參數放進 VO，初始化成 123
  b: undefined,   // 把變數放進 VO，初始化成 undefined
}
```

### 範例： 在 function 中宣告一個 function

程式碼如下：

```javascript=
function test(a, b){
  function b(){
  }
  var c = 30;
}
test(123);
```

1. 宣告 function 一樣會在 VO 裡面新增一個屬性
2. 但如果 VO 裡面已經有同名的屬性，就會取代之。因此 function b 會取代傳入的參數 b

VO 模型如下：

```javascript=
VO: {
    a: 123,   // 把參數放進 VO，初始化成 123
    b: function b, // 把 funcion b 放進 VO
    c: undefined // 把變數放進 VO，初始化成 undefined
}
```

對於宣告變數，則會在 VO 裡面新增一個屬性並且把值設為 undefined，如上述範例中的 `var c` 但如果 VO 已經有這個屬性時，值不會被改變。

### 小結

由上方幾個範例可知，當我們進入一個 EC，但還沒開始執行 function 以前，會先建立 Variable Object，並按照順序進行以下三件事：

1. 把參數放到 VO 裡面並設定傳進來的值，沒有值的設成 undefined
2. 把 function 宣告放到 VO 裡，如果已經有同名的就取代之
3. 把變數宣告放到 VO 裡，如果已經有同名的則忽略

### 最後再來個牛刀小試

```javascript=
function test(v){
  console.log(v);      // 10
  var v = 3;
  console.log(v);      // 3
}
test(10);
```

思考方式：

1. 在執行 function 前，因為 test() 有傳參數進去，所以把 v 放到 VO 並設定為 10
2. 接著開始執行 functon 內容程式碼，第二行印出 10
3. 執行到第三行，把變數 v 的值換成 3
4. 執行到第四行，印出 3

---

## TDZ：Temporal Dend Zone 暫時死區

接著再回到 let 和 const，其實以 let 和 const 宣告變數同樣會有 Hoisting 的情形，只是執行方式不太相同。

先以下列程式碼為例，和文章最一開始的 `var a` 不同，結果出現 `a is not defined` 的錯誤：

```javascript=
console.log(a); // ReferenceError: a is not defined
let a;
```

這樣是不是代表 let 和 const 沒有變數提升呢？否則宣告經過提升後應該不會出現 Error 才對？

事實上，let 與 const 確實有 Hoisting，與 var 的差別在於提升之後，var 宣告的變數會被初始化為 undefined；而 let 與 const 的宣告則不會被初始化為 undefined，如果在「賦值之前」就存取它，會拋出錯誤訊息。

簡單來說，如果是在「提升之後」以及「賦值之前」這段期間存取變數就會拋出錯誤，而這段期間就稱作 TDZ（Temporal Dend Zone），中文為「暫行性死去」或「暫時死區」，是為了解釋 let 與 const 的 Hoisting 行為所提出的一個名詞。

再來看以下範例：

```javascript=
var a = 10;
function test() {
  console.log(a);
  let a = 30;
}
test();
// ReferenceError: Cannot access 'a' before initialization

=== 經過 Hoisting ===

var a = 10;
function test() {
  let a;           // 宣告變數 a 經過提升之後
  console.log(a);  // 在賦值之前不能存取 a
  a = 30;
}
test();
```

同理可證，假如 let 沒有 Hoisting，結果應該會印出 10 才會，因為 log 那一行會存取全域變數的 `var a = 10`，但是卻印出 `錯誤：無法在初始化之前存取變數 a`。這是因為 let 的確有提升，只是沒有初始為 undefined。

總結上述例子：
- let 與 const 宣告確實也有 Hoisting 行為，但沒有像 var 宣告初始化為 undefined
- 如果在提升之後，賦值之前這段期間，存取該值會發生錯誤
- 這段期間稱為 Temporal Dend Zone（暫時死區）

---

## 結語

從理解什麼是 Hoisting（提升），瞭解我們位什麼需要提升，再延伸到運作原理。過程中建立的 Execution Contexts（執行環境）、與之對應的 Variable Object（變數物件）等等，其實涉及到有關 JavaScript 的知識範圍非常廣。

除了課堂影片提到的內容，自己也上網查了許多有關執行環境、執行堆疊的資料，雖然花費不少時間，卻也藉由瞭解 JavaScript 的編譯與執行過程，從建立到執行階段，加深對整個架構的理解。

參考資料：

- [我知道你懂 hoisting，可是你了解到多深？](https://blog.techbridge.cc/2018/11/10/javascript-hoisting/)
- [JavaScript: Scope & Hoisting](https://nicolakacha.coderbridge.io/2020/09/29/javascript-scope-hoisting/)
- [理解 Javascript 執行環境](https://andyyou.github.io/2015/04/18/what-is-the-execution-context-in-javascript/)
- [javascript執行環境及作用域詳解](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/267342/)
- [秒懂！JavaSript 執行環境與堆疊](https://medium.com/%E9%AD%94%E9%AC%BC%E8%97%8F%E5%9C%A8%E7%A8%8B%E5%BC%8F%E7%B4%B0%E7%AF%80%E8%A3%A1/%E6%B7%BA%E8%AB%87-javascript-%E5%9F%B7%E8%A1%8C%E7%92%B0%E5%A2%83-2976b3eaf248)
- [JavaScript 深入淺出 Variable Object & Activation Object](https://shawnlin0201.github.io/JavaScript/JavaScript-Variable-Object-and-Activation-Object/)
