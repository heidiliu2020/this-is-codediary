###### tags: `JavaScript` `Scope` `Closure`
# [week 16] JavaScript 進階 - 什麼是閉包？探討 Closure & Scope Chain

> 本篇為 [[JS201] 進階 JavaScript：那些你一直搞不懂的地方](https://lidemy.com/p/js201-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

在上一篇筆記 [[week 16] JavaScript 進階 - 初探 Hoisting & Execution Context](https://hackmd.io/@Heidi-Liu/note-js201-scope-hoisting) 中，我們談到 Hoisting（提升）、Execution Context（執行環境）等相關概念。

瞭解到每一個 function 會有一個對應的執行環境，裡面負責存放該環境需要用到的各種資料，由這些參數組成 Variable Object（變數物件）。

包括之前談過的 VO，每個執行環境會有下列三個屬性：

- 作用域鏈（Scope Chain）
- 變數物件（Variable Object）
- ‘this’ 變數（‘this’ Variable）

而當中的作用域鏈（Scope Chain）其實就和本篇所要探討的 Closure（閉包）有關，因此下面會先從 Scope（作用域）開始講起。

```
學習目標：

 P1 你知道什麼是作用域（Scope）
 P1 你知道 Closure（閉包）是什麼
 P1 你能夠舉出一個運用 Closure 的例子
```

---

## Scope 作用域

什麼是 Scope（作用域）？簡言之，就是「一個變數的生存範圍」，一旦出了這個範圍，就會無法存取到這個變數。

舉個簡單的例子，如果在 test() 中以 var 宣告變數 a，在 function 作用域之外會無法存取該變數：

```javascript=
function test(){
  var a = 10
}
console.log(a)
// Uncaught ReferenceError: a is not defined
```

在 ES6 以前，唯一產生作用域的方法就是宣告 function，每一個 function 都有自己的作用域，在作用域外就存取不到這個 function 內部所定義的變數。

在 ES6 出現以後，作用域的概念有些改變，也就是引入 let 跟 const 的宣告，可用大括號 `{...}` 來定義 block（區塊）作用域。

因此 JavaScript 的作用域其實可分為三個層級：

- Global Level Scope：全域作用域
- Function Level Scope：函式作用域
- Block Level Scope（ES6）：區塊作用域

也就是說，變數作用域的範圍，其實就取決於這個變數的宣告方式，以及在哪進行宣告。

而根據變數是在哪宣告，又可分為全域變數和區域變數：

- 全域變數（Global Variable）
  - 在 function 外宣告的變數
  - 任何地方皆能存取到
- 區域變數（Local Variable）
  - 在 function 內宣告的變數
  - 只在該作用域內有效，也就是 function 本身及其內部

### Function Scope 可能發生的問題

接著要來談談 function 作用域中可能遇到的狀況，這可能會導致結果和想像的不同。

#### 狀況一：變數的值被覆蓋

若 var 變數不是宣告在 function 作用域內，而是在迴圈或是判斷式，這個變數可能就會覆蓋到外面的全域函數，造成變數汙染。

在下面的程式碼中，if 判斷式裡面的變數 str，會覆蓋外面的變數 str，因此結果是印出 Local：

```javascript=
var str = 'Global';
if (true) {
  var str = 'Local';
}

console.log(str);
// Local
```

#### 狀況二：迴圈變數可能會向外覆蓋全域變數

當 for 迴圈中的變數 i 循環結束時，會蓋過外面的全域變數 i，因此 function 外面的 i 會被重新賦值為 3：

```javascript=
var str = 'cat';
var i = 1;
for (var i = 0; i < str.length; i++) {
    console.log(str[i]);      // 向外覆蓋全域變數
}

console.log(i);
// c
// a
// t
// 3
```

### E6 以後的作用域：Block Scope

接著再回到 ES6，新增了區塊作用域（block scope）的概念，也就是以 let 和 const 來宣告變數。

在第三週的 [ES6 部份](https://github.com/heidiliu2020/this-is-codediary/blob/master/week3_ES6%20npm%20Jest.md#es6-%E6%96%B0%E8%AA%9E%E6%B3%95)我們也曾提到，以 let、const 或 var 方式來宣告變數，最大的差別在於變數的作用域範圍不同：

- var：作用於整個函數範圍中（function scope）
- let 與 const：均為區塊作用域（block scope），如此可避免污染到大括號 `{...}` 外的變數

而 let 和 const 最大的區別，在於該變數是否能被重新賦值：

- const（constant）：常數宣告後就不能再重新賦值，並且在宣告時就必須賦值
- let：可重新賦值，也可先進行宣告但不賦值

以下面程式碼為例，說明以 var 和 let 宣告變數會有什麼差別：

#### 用 var 在 for 迴圈宣告變數 i

先以 var 來宣告變數 i，for 迴圈結束後，外面的 log 結果是 3：

```javascript=
function test() {
  for(var i = 0; i < 3; i++) {
    console.log('i:', i);
  }
  console.log('final value', i);
}

test()
// i: 0
// i: 1
// i: 2
// final value 3
```

#### 用 let 在 for 迴圈宣告變數 i

若改用 let 在 for 迴圈宣告變數，則會出現錯誤 `i is not defined`：

```javascript=
function test() {
  for(let i = 0; i < 3; i++) {
    console.log('i:', i);        // 
  }
  console.log('final value', i);
}

test();
// ReferenceError: i is not defined
```

這是因為以 let 進行宣告，變數 i 的作用域就僅限於 for 迴圈這個 block 區塊，所以大括號外面就無法存取到變數 i。

### 作用域會往外層找

記住這個重點：「作用與會往外層找」。也就是說，在 function 外面會存取不到裡面，但內層可以存取到外層的東西。

舉下面幾個程式碼作為範例。

#### 範例一：從 function 外往內存取變數

結果會出現錯誤 `a is not defined`：

```javascript=
function test() {
  var a = 10;
  console.log(a);
}

test();
console.log(a);
// ReferenceError: a is not defined`
```

這是因為在 function 外面沒辦法存取內部的變數 a，所以會出現錯誤。

#### 範例二：存取 function 以及 global 變數

若分別宣告全域變數和區域變數，log 結果不會互相干擾：

```javascript=
var a = 20          // global variable
function test() {
  var a = 10;       // function variable 
  console.log(a);    // 10
}

test();
console.log(a);      // 20
```

#### 範例三：直接在 function 內部賦值

結果全域和區域的兩個 a，其 log 結果會相同：

```javascript=
var a = 20;          // global variable
function test() {
  a = 10;
  console.log(a);    // 10，function -> global
}

test();
console.log(a);      // 10
```

原因在於，即使 function 內沒有宣告變數，仍會「往外」找到已經被宣告的全域變數 `var a`，然後再回到內部賦值 `a = 10`。

變數會先在自己的作用域找，若找不到會繼續再往外找，一層一層直到找到為止。而這一連串的行為，就稱作 Scope Chain（作用域鏈），詳細內容稍後會再進行說明。

#### 範例四：function 內外都沒有宣告變數

結果仍會和上述範例相同！

```javascript=
function test() {
  a = 10;
  console.log(a);    // 10，test -> global
}

test();
console.log(a);      // 10
```

這是因為，在 function 中如果 a 找不到值，就會往外層找，如果全域也找不到，就會自動宣告全域變數 `var a`。

這會和前一個例子寫法產生相同結果，但這種情況其實會產生一些 bug，也就是和預期行為不同，甚至可能產生衝突。

## Scope Chain 作用域鏈

在說明之前，先來看以下範例：

```javascript=
function test() {
  var a = 100
  function inner() {
    console.log(a) // 100
  }
  inner()
}
test()
```

在 inner() 中，a 並非該函式中的變數，而這種不在該 function 作用域中，也不是作為參數傳進來的變數，就被稱為 Free Variable（自由變數）。

對 inner() 來說，a 是一個自由變數。因為在 inner() 的作用域中找不到 a，就會往外層 test() 找，如果還是找不到會再往外直到找到為止。

這其實就構成一個 Scope Chain（作用域鏈）：inner function scope -> test function scope -> global scope，如果直到全域作用域還是找不到，就會拋出錯誤。

還記得我們在開頭提到，每個執行環境物件會有下列三個屬性：

- 作用域鏈（Scope Chain）
- 變數物件（Variable Object）
- ‘this’ 變數（‘this’ Variable）

而 Scope Chain 這個屬性，其實就是負責記錄「包含自己的 VO + 所有上層執行環境的 VO」的集合。藉由該屬性，函式內部就可以存取到外部的變數。　

聽起來有點抽像，我們舉個簡單的例子：

```javascript=
function one() {
  var a = 1;
  two();
  function two() {
    var b = 2;
    three();
    function three() {
      var c = 3;
      console.log(a + b + c);   // 6
    }
  }
}

one();
```

從 Global Context 呼叫 one()，one() 再呼叫 two()，接著再呼叫 three()，最後在 function three 執行 console.log()。下圖在建立階段的堆疊示意圖：

![](https://i.imgur.com/RtkT8LP.png)

當 JavaScript 要執行 `console.log(a + b + c)` 這行程式，會不斷往 Scope Chain 去尋找。

就像前面所說的，一開始會先在自己的 VO 找，找不到在換下一個，一直到 global 為止，如果找不到就會拋出錯誤。過程如下圖：

![](https://i.imgur.com/9ROutJb.png)

（圖片來源：https://andyyou.github.io/2015/04/20/understand-closures-and-scope-chain/）

### ECMAScript 中的作用域

每個執行環境都有一個 Scope Chain。也就是說，一旦進入該執行環境，就會建立 Scope Chain 並進行初始化。

以 global 執行環境來說，初始化後會把 VO 放進 Scope Chain 內，可表示為：

```javascript=
scopeChain = [globalEC.VO]
```

此外，每個函式都有一個 [[Scope]] 屬性，當 global 執行環境遇到函式時，會將它初始化為 global 執行環境的 Scope Chain：

```javascript=
function.[[Scope]] = globalEC.scopeChain
```

當函式被呼叫時，會建立 local 執行環境，也會建立 VO，在函式中會稱作 Activation Object（AO），並且除了 AO 之外，外面傳進來的參數也會被加到該 local 執行環境的 Scope Chain：

```javascript=
function.scopeChain = [function.AO, function.[[Scope]]]
```

### 模擬 JS 實際流程

舉個簡單的範例：

```javascript=
var a = 1;
function test() {
  var b = 2;
  function inner() {
    var c = 3;
    console.log(c);   
    console.log(b);
    console.log(a);
  }
  inner();
}

test();
```

#### 第一步：進入 Global 執行環境

首先進入 Global EC，並初始化 VO 以及 scope chain。前面提到 scope chain = activation object + [[Scope]]，但因為這不是一個 function，所以沒有[[Scope]] 和 AO，會直接以 VO 來用：

```javascript=
global EC {
  VO: {
    a: undefined,
    test: function
  },
  scopeChain: [globalEC.VO]
}

test.[[Scope]] = globalEC.scopeChain
```

此外也需設置 function 的 [[Scope]]，所以 test() 的[[Scope]] 就會是 globalEC.scopeChain，也就是 globalEC.VO。

#### 第二步：建立 test 執行環境

執行完 `var a = 1` 後，將 global EC 的 VO 初始為 1。

接著準備進入 test()，在進入之前會先建立 test EC 並初始化 AO 以及 scope chain ：

```javascript=
testEC: {
  AO: {
    b: undefined,
    inner: function
  }
    scopeChain: [testEC.AO, test[[Scope]]]
    => [testEC.AO, globalEC.VO]
}

inner.[[Scope]] = testEC.scopeChain
= [testEC.AO, test[[Scope]]]

======

global EC {
  VO: {
    a: 1,
    test: function
  },
  scopeChain: [globalEC.VO]
}

test.[[Scope]] = globalEC.scopeChain
= [globalEC.VO]
```

同裡，需設置 function inner() 的 [[Scope]]，可表示為 testEC.scopeChain，又等同於 [testEC.AO, test[[Scope]]]。

#### 第三步：建立 inner 執行環境

執行完 `var b = 2` 後，將 test EC 的 AO 初始為 2。

接著進入 inner() 時同樣會建立 inner EC 跟 AO 建立，然後執行完 `var c = 3` 後，將 inner EC 的 AO 初始為 3：

```javascript=
innerEC: {
  AO: {
    c: 3
  },
  scopeChain: [innerEC.AO, inner[[Scope]]]
    => [inner.AO, testEC.scopeChain]
    => [inner.AO, testEC.AO, globalEC.VO]
}

testEC: {
  AO: {
    b: 2,
    inner: function
  }
    scopeChain: [testEC.AO, test[[Scope]]]
    => [testEC.AO, globalEC.VO]
}

inner.[[Scope]] = testEC.scopeChain
= [testEC.AO, test[[Scope]]]

======

global EC {
  VO: {
    a: 1,
    test: function
  },
  scopeChain: [globalEC.VO]
}

test.[[Scope]] = globalEC.scopeChain
= [globalEC.VO]
```

#### 第四部：執行程式碼

- 執行到 `console.log(c)`
  - 在 `innerEC.AO` 裡面找到 c = 3
- 執行到 `console.log(b)`
  - 在 `innerEC.AO` 裡找不到
  - 沿著 scopeChain 往上找到 `testEC.AO` 裡面 b = 2
- 執行到 `console.log(a)`
  - 在 `innerEC.AO` 裡找不到
  - 沿著 scopeChain 往上找到 `globalEC.AO` 裡面 a = 3

```javascript=
var a = 1;
function test() {
  var b = 2;
  function inner() {
    var c = 3;
    console.log(c);  // 3
    console.log(b);  // 2
    console.log(a);  // 1
  }
  inner();
}

test();
```

如同前面所說，其實 Scope Chain 就是 VO/AO 的組合，是負責記錄「包含自己的 VO + 所有上層執行環境的 VO」的集合。

藉由編譯完成時的 EC 模型，我們可瞭解程式在執行時，是如何在 AO 或 VO 裡面找到宣告過的變數，若在該作用域找不到，就會沿著 scopeChain 不斷會往上一層找。

這其實能夠解釋之前提過的 Hoisting（提升），還有接下來要探討的 Closure（提升）是如何發生。

### Lexical Scope vs Dynamic Scope

有了基本概念後，再來看下列範例，其中 a 的 log 值會是多少呢？

```javascript=
var a = 100
function echo() {
  console.log(a) // 100 or 200?
}
  
function test() {
  var a = 200
  echo()
}
  
test()
```

結果會是 100，echo() 裡面的 a 就是 global 的 a，和 test() 裡面的 a 一點關係都沒有。

這和程式語言是如何決定「作用域」這件事有關，可分為靜態作用域和動態作用域：

- Static Scope 靜態作用域
  - 又可稱為 Lexical Scope 語法作用域、語彙範疇
  - 變數的作用域在語法解析時，就已經確定作用域，且不會改變
- Dynamic Scope 動態作用域
  - 變數的作用域在函式調用時才決定
  - 若是採用動態作用域的程式語言，那最後 log 出來的值就會是 200 而不是 100

而 JavaScript 採用的是靜態作用域，在分析程式碼的結構就可以知道作用域的長相。但需特別注意的是，JavaScript 中的 `this`，其原理和動態作用域非常類似，this 的值會在程式執行時才被動態決定。

建立一些有關作用域的觀念後，再來我們要來談談本篇核心：Closure（閉包）。

---

## Closure 閉包

在 JavaScript 中，Closure（閉包）和作用域的關係密不可分，透過 Scope Chain 的機制，我們能夠進一步理解 Closure 產生的原因。

可先來看看下方這個例子：

```javascript=
function test() {
  var a = 10;
  function inner() {
    a++;
    console.log(a)
  }
  return inner // 不加括號，只 return 這個 function
}
var func = test()
func() // 11 => 等同於 inner()
func() // 12 => 等同於 inner()
func() // 13 => 等同於 inner()
```

透過在 function 中回傳另一個 function 的寫法，就可以把 a 這個變數鎖在這個 function 裡面，隨時能夠拿出來使用。

一般而言，當 function 被執行完之後，資源就會被釋放掉，但是通過這種寫法，我們就可以把 function 內部變數的值給保存起來。

再根據 [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Closures) 說明，其實閉包就是一個特殊的物件，具有下列兩個含義：
- 它是一個 function
- 它產生了一個 context 執行環境，負責記錄上層 VO

再舉個有關 Closure 的例子：

```javascript=
function factory() {
  var brand = "BMW";

  return function car() {
    console.log("This is a " + brand + " car");
  }
}

var carMaker = factory();
carMaker();  //  "This is a BMW car
```

上述程式碼建立的 EC 模型：

```javascript=
// Global Context
global.VO = {
  factory: pointer to factory(),
  carMaker: 是 global.VO.factory 的回傳值
  scopeChain: [global.VO]
}

// Factory 執行環境
factory.VO = {
  car: pointer to car(),
  brand: 'BMW',
  scopeChain: [factory.VO, global.VO]
}

// car 執行環境
car.VO = {
  scopeChain = [car.VO, factory.VO, global.VO]
}
```

以 JS 運作過程來說， function factory 執行結束後就會從 Call Stack 移除，但是因為 VO 還會被 car VO 參考，所以不會將其移除。

這其實就是前面所提到的 Scope Chain 與 Closure 之間的關係。

### 操作 Closure 可能遇到的作用域陷阱

由以下範例，可發現執行 `arr[0]()` 時，結果會是 5：

```javascript=
var arr = [];
for (var i = 0; i < 5; i++) {
  arr[i] = function() {
      console.log(i);
  }
}

arr[0]();  // 5
```

當執行 `arr[0]()` 時，其實會長這樣：

```javascript=
arr[0] = function() {
  console.log(i);
}
```

因為 function EC 中沒有宣告變數 i，因此會往上一層作用域找，找到 global EC 的 i，又因為 for 迴圈執行結束，此時的 i = 5，所以會印出 5。

可使用下列方法改寫上述程式碼：

#### 1. 使用閉包：在 function 中 return function

```javascript=
var arr = [];
for (var i = 0; i < 5; i++) {
  arr[i] = logN(i);
}

function logN(n) {
  return function() {
      console.log(n);
  }
}

arr[0]();  // 0
```

#### 2. IIFE：立即呼叫函式

- IIFE：Immediately Invoked Function Expression，是一個在宣告的當下就會馬上被執行的函數。語法如下：

```javascript=
(function () {
  console.log('hello')
})()
// hello
```

因此範例程式碼可修改成，這樣就不用再另外宣告 function，但缺點是可讀性較差：

```javascript=
var arr = [];
for (var i = 0; i < 5; i++) {
  arr[i] = (function(n) {
    return function() {
      console.log(n);
    };
  })(i)
}
arr[0]();  // 0
```

#### 3. 使用 let 宣告：限定變數的作用域

```javascript=
var arr = [];
for (let i = 0; i < 5; i++) {
  arr[i] = function() {
    console.log(i);
  }
}

arr[0]();  // 0
```

執行 `arr[0]()` 時可以表示成：

```javascript=
var arr = [];
{
  let i = 0;
  arr[0] = function() {
    console.log(i);
  }
}

arr[0]();  // 0
```
## Closure 實際應用

那我們通常會在什麼情況下使用 Closure 呢？像是在計算量很大的時候，或是需要隱藏一些內部資訊。方法如下：

### 1. 封裝

可將一些不想外露的細節封裝在執行環境中，只露出想要 public 部分。

以下方與金源有關的程式碼為例：

```javascript=
var money = 99
function add(num) {
  money += num
}
function deduct(num) {
  if (num >= 10) {
    money -= 10;
  }
}
add(1)
deduct(100)
console.log(money)
```

在這種情況之下，任何人都能夠變動 global 中變數 money 的值。

如果改成使用閉包，就能夠把變數給隱藏起來，無法從外部更改 function 內部資料：

```javascript=
function createWallet(initMoney) {
  var money = initMoney;
  return {
    add: function(num) {
      money += num;
    },
    deduct: function(num) {
      if (num >= 10) {
        money -= 10;
      } else {
        money -= num
      }
    },
    getMoney() {
      return money;
    }
  }
}
var myWallet = createWallet(99);
myWallet.add(1);
myWallet.deduct(100);
console.log(myWallet.getMoney()); // 90
```

### 2. Callbacks 回呼

callback funtcion 就是一種常見的閉包。先前提到像 JavaScript 就是採用單執行緒與 Event Loop 機制，一次只能處理一件事情。

但以 callback 能讓我們能夠延遲函式的調用，實現非同步操作。例如呼叫一個 AJAX 的 XMLHttpRequest，通常就會使用 callback 來處理伺服器的回應，因此等待時其他程式還是能照常運作。

## 何時不該使用 Clousre？

雖然 Closure 提供非常便利的功能，但因為系統效能的因素，還是必須謹慎使用：

### 1. 過多的作用域

需注意每當需要取得一個變數時，Scope Chain 會一層一層檢索，直到找到該物件或值，因此越多層會導致需時越長，例如多個巢狀 function。

### 2. 記憶體回收

記憶體回收（Garbage Collection）機制，簡單來說，就是當物件不再被參考時，就會被記憶體回收處理。

但如果是在不正確使用閉包的情況下，就可能導致記憶體洩漏（Memory Leak），造成程式未能釋放已經不再使用的記憶體，並產生效能問題。

以下方的程式碼為例：

```javascript=
function leakyFn() {
  leak = 100
}
leakyFn()
console.log(leak)  // 100
```

若在非 strict 模式下執行，JavaScript 會自動宣告一個全域變數 `var leak`，因此就算函數執行完畢，leak 還是會繼續存留在 全域環境中，因此也就不會被回收掉。

---

## 結語

在學到 Closure（閉包）時，發現到花了很多時間在瞭解有關 Scope（作用域）的概念。也是在這一單元瞭解到，原來之前在課程學到的非同步操作，當中的 callback 其實就和閉包有關，有關 callback 的觀念真的非常重要！

此外也瞭解到，閉包在框架中很常會使用到，透過閉包的方式，就能夠避免汙染全域變數或是記憶體洩漏等問題。

一開始之所以沒辦法很快理解，或許就是沒有把這些觀念融會貫通，都是一個環節接著另一個環節，和 Scope Chain 一樣，會需要往上一層去找出需要的拼圖。

參考資料：

- [所有的函式都是閉包：談 JS 中的作用域與 Closure](https://blog.huli.tw/2018/12/08/javascript-closure/)
- [參透Javascript閉包與Scope Chain](https://andyyou.github.io/2015/04/20/understand-closures-and-scope-chain/)
- [前端中階：JS令人搞不懂的地方-Closure(閉包)](https://hugh-program-learning-diary-js.medium.com/%E5%89%8D%E7%AB%AF%E4%B8%AD%E9%9A%8E-js%E4%BB%A4%E4%BA%BA%E6%90%9E%E4%B8%8D%E6%87%82%E7%9A%84%E5%9C%B0%E6%96%B9-closure-%E9%96%89%E5%8C%85-cbb9c6a4185c)




