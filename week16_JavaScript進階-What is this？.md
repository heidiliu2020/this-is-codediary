###### tags: `JavaScript` `物件導向` `this`
# [week 16] JavaScript 進階 - What is this？

> 本篇為 [[JS201] 進階 JavaScript：那些你一直搞不懂的地方](https://lidemy.com/p/js201-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 你知道大部分情況下 this 的值是什麼
 P2 你知道物件導向的基本概念（類別、實體、繼承、封裝）
```

---

## 從物件導向到 this

在上一篇[JavaScript 進階 - 物件導向 & Prototype](https://hackmd.io/@Heidi-Liu/note-js201-oop-prototype) 筆記中，其實我們就有提到 this 相關概念。this 的主要用途就在於物件導向，用來指哪個 instance 在呼叫這個 function，透過 this 我們能夠進一步操作。

那如果是在和物件導向無關的情況下，呼叫 this 會代表什麼？

### Global context 下的 this

如果在全域環境下呼叫 this，根據不同執行環境（瀏覽器或 node.js），預設值可能會是 global 或 window：

#### 在 node.js 運行

```javascript=
function test() {
  console.log(this);            // Object [global] { ...}
  console.log(this === global)  // true
}
test();
```

![](https://i.imgur.com/9yFrDnZ.png)

#### 在瀏覽器運行

```javascript=
function test() {
  console.log(this);            // Window { ...}
  console.log(this === window)  // true
}
test();
```

![](https://i.imgur.com/wVCdilg.png)

### use strict 嚴格模式

在不需要的地方呼叫 this，卻還是會有預設值 global 或 window。這時候，只要設定 `'use strict'`（嚴格模式）就能避免，this 的預設值會是 undefined：

```javascript=
'use strict';
function test() {
  console.log(this);
}
test();  // undefined
```
### DOM 元素中的 this

此外，對 DOM 元素進行事件監聽時，this 就代表當下操作的元素，其實非常直覺。例如監聽按鈕的點擊事件，那 this 就會是那個按鈕：

```javascript=
document.querySelector('.btn').addEventListener('click', function() {
  console.log(this)   // this: 觸發這個 funciont 的物件
})
```

因此我們可以說，除了在物件導向跟 DOM 之外，this 是沒有意義的。

### Function context 下的 this

在 function 裡面時，this 的值是什麼取決於該 function 是怎麼被呼叫的。

也就是說，function 沒有被呼叫的時候，預設值就會是 window, global 或 undefined。一旦某個 function 被呼叫時，裡面的 this 就可以理解成，是指向誰在 call 這個 function：

```javascript=
var obj = {
  foo: function(){
    console.log(this)
  }
}
var bar = obj.foo

obj.foo() // foo(): obj 在 call，obj 就是 this 
bar()   // bar(): 沒人 call，window 就是 this
```

其中，`bar()` 這種呼叫函式的方法，其實是 `.call()` 的語法糖，要更詳細了解呼叫 function 背後在做的事，就要深入理解 `.call()`、`.apply()`、`bind()` 的行為。

## 另外兩種呼叫 function 的方法：call 與 apply

### `.call()`

第一個值傳入什麼 this 就是什麼。

```javascript=
'use strict';
function test() {
  console.log(this);
}
test.call(123);     // 123
test.call({});      // {}
test.call([1]);     // [ 1 ]
test.call('abc');   // 'abc'
```

### `.apply()`

和 `.call()` 一樣，第一個值傳入什麼 this 就是什麼，差異在於第二個參數會接一個 array。

可由下方範例來比較三種方式呼叫 function 的差異：

```javascript=
'use strict';  // 嚴格模式
function test(a, b, c) {
  console.log(this);
  console.log(a, b, c);
}

test(1, 2, 3)
// undefined
// 1 2 3
test.call(123, 1, 2, 3);
// 123
// 1 2 3
test.apply(123, [1, 2, 3]);
// 123
// 1 2 3
```

透過 `.call` 和 `.apply` 的第一個參數，我們可以設定 function 中 this 的值。兩者差別在於，一個可以傳入無限的參數，另外一個只能傳入兩個參數；一個要用 array 帶入參數，另一個則否。

此外，我們也可以把一般呼叫 function 的方式都轉成 call 的形式：

```javascript=
const obj = {
  value: 1,
  hello: function() {
    console.log(this.value)
  }
}

obj.hello()  // 1
obj.hello.call(obj)  // 1
```

再舉一個例子，透過 call 來呼叫 function，我們就能夠指定 this 的值：

```javascript=
function hello() {
  console.log(this)
}

var a = {
  value: 1, hello
}

var b = {
  value: 2, hello
}

hello()  // global
a.hello()  // a { value: 1, hello}
b.hello.call(a)   // a { value: 1, hello}
```

---

## 如何判斷 this 的值

因為 this 是針對物件導向設計的，從以下範例，可知道 this 就是 obj 物件本身：

```javascript=
'use strict';
var obj = {
  a: 10,
  test: function(a, b, c) {
    console.log(this);  // obj
  }
}
obj.test();
// { a: 10, test: [Function: test] } === obj
```

重點就是，this 的值和程式碼在哪無關，而是和怎麼呼叫有關係：

```javascript=
'use strict';
var obj = {
  a: 10,
  test: function (a, b, c) {
    console.log(this);    // obj
  }
}
obj.test();  // { a: 10, test: [Function: test] }
var func = obj.test;  // 使 func 等同於 obj.test()
func()       // undefined
```

結果卻會得到不同結果，這是因為 obj 呼叫 function，所以 this 會指向 obj；另一方面，this 所在的 instance 中，沒有被 func 呼叫，所以會得到 undefined。

我們可以透過 `.call()` 來看看 this 指的是什麼：

```javascript=
'use strict';
var obj = {
  a: 10,
  inner: {
    test: function(a, b, c) {
      console.log(this);
    }
  }
}
obj.inner.test();
// { test: [Function: test] }

// 改用 .call() 呼叫
obj.inner.test.call(obj);
// { a: 10, inner: { test: [Function: test] } }
obj.inner.test.call(obj.inner);
// { test: [Function: test] }
```

### .bind()：強制綁定 this

除了 `.call()` 和 `.apply()`，我們還可以利用 `.bind()` 來強制綁定 this 的值，但需注意的是，一旦綁定之後就不能再用別的方法進行更改。

將前面提到的例子修改成 .bind()，這裡需注意 .bind() 會是直接回傳一個 function：

```javascript=
'use strict';
var obj = {
  a: 10,
  test: function () {
    console.log(this);
  }
}

// 第一種寫法：直接呼叫
obj.test.bind(obj)();
// { a: 10, test: [Function: test] }

// 第二種寫法：先宣告，再呼叫
const bindTest = obj.test.bind(obj);
bindTest();  
// { a: 10, test: [Function: test] }
const bindTestA = obj.test.bind(obj.test);
bindTestA();
// [Function: test]
const bindTestB = obj.test.bind("test B");
bindTestB();
// test B
const bindTestC = obj.test.bind("test C");
bindTestC.call('CCC');
// test C
```

利用 `.bind()` 就可以綁定 this 的值，也就是鎖定想要呼叫的區域，即便後面再 `.call()` 指定 this 的值也無法改變。

### 牛刀小試

以下範例預設在嚴格模式進行：

```javascript=
'use strict';

function log() {
  console.log(this);
}
var a = { a: 1, log: log };
var b = { a: 2, log: log };

log();     
a.log();    
b.log.apply(a);
```

思考步驟：
```javascript=
1. log();
// undefined
// 在嚴格模式下，如果在非物件導向的情況呼叫 this，其值會是 undeined 

2. a.log();  
// { a: 1, log: [Function: log] }
// 等於 a.log.call(a)，所以 this 就是 a 本身

3. b.log.apply(a);
// { a: 1, log: [Function: log] }
// .apply() 會把 this 的值蓋過，所以 this 也會是 a 本身
```

### arrow function 的 this

arrow function 本身其實沒有 this，在 arrow function 裡面的 this，其實就和 arrow function 外部的 this 是同一個東西。

先以一般宣告 function 為例：

```javascript=
class Test {
  run() {
    console.log('run this', this);
    setTimeout(function() {
      console.log(this);
    }, 100) 
  }
}
const t = new Test();
t.run();
```

在瀏覽器上的運行結果，會發現第二個 this 等於全域的 Window：

![](https://i.imgur.com/27kKU6Q.png)

也就是說，在 100 毫秒後才呼叫，其實作用就等同於在全域呼叫這個 function：

```javascript=
function() {
  console.log(this);
}
```

但如果改成箭頭函式，結果就會不同了：

```javascript=
class Test {
  run() {
    console.log('run this', this);
    setTimeout( () => {
      console.log(this);
    }, 100) 
  }
}
const t= new Test();
t.run();
```

在瀏覽器上的運行結果，此時兩個 this 會變相同：

![](https://i.imgur.com/thp7RZD.png)

這就是箭頭函式的特性，和箭頭函式中的 this 怎麼呼叫沒關係。而是類似 Scope（作用域）的機制，看程式碼定義在哪裡，就會利用定義的 block 來呈現這個 this 的值。

---

## 結語

瞭解到物件導向的相關概念後，接著要理解 this 是什麼就沒那麼困難了。或許是因為在實際學 JacaScript 以前，就預設 this 是很難是高手在用的東西，透過慢慢理解物件導向與 this 的關聯，以及如何判斷 this 的值，似乎也感覺到自己的進化，對於未知的恐懼總是需要克服的。

關於 this 的重點，就是記得 this 的值和程式碼在哪無關，而是和怎麼呼叫有關係。

總結前面提到的觀念，其實 this 大致可分成四種綁定方式：

- 默認綁定

在和物件導向無關的情況下，this 會被指定為全域物件。又依照執行環境不同，其值會是 global 或 window，而在嚴格模式下會是 undefined：

```javascript=
function test() {
  console.log(this);     // Window 
}
test();
```

- 隱式綁定

若在 function 中， this 有被某物件指定為屬性並呼叫，this 就是呼叫 function 的物件。以下方範例來說 this 就是 obj：

```javascript=
function func() {
  console.log(this.a);
}

var obj = {
  a: 4,
  test: func
};

obj.test();  // 4
```

- 顯示綁定

若是透過 `.call()`、`.apply()` 或 `.bind()` 方式指定 this，this 就會是傳入的參數：

```javascript=
var obj = {
  a: 10,
  test: function () {
    console.log(this);
  }
}

obj.test.call(obj)
obj.test.apply(obj)

// 第一種寫法：直接呼叫 function
obj.test.bind(obj)();
// 第二種寫法：先宣告，再呼叫
const bindTest = obj.test.bind(obj);
bindTest();  

// 均印出: { a: 10, test: [Function: test] }
```

- new 綁定

透過建構函式 new 出一個 instance，this 就會是 instance 物件本身：

```javascript=
class Dog {
  constructor(name) {
    this.name = name;
    console.log(this);       // Dog {name: "dog A"}
    console.log(this.name);  // dog A
  }
}

var a = new Dog('dog A');
```

- 例外：箭頭函式中的 this 是看程式碼定義在哪，和怎麼呼叫沒關係。

參考資料：
- [淺談 JavaScript 頭號難題 this：絕對不完整，但保證好懂](https://blog.techbridge.cc/2019/02/23/javascript-this/)
- [前端中階：JS令人搞不懂的地方-this](https://hugh-program-learning-diary-js.medium.com/%E5%89%8D%E7%AB%AF%E4%B8%AD%E9%9A%8E-js%E4%BB%A4%E4%BA%BA%E6%90%9E%E4%B8%8D%E6%87%82%E7%9A%84%E5%9C%B0%E6%96%B9-this-b9b9c6960c03)
- [JavaScript: Object-oriented JavaScript, Prototype Chain & This](https://nicolakacha.coderbridge.io/2020/10/01/oop-prototype-this/)
- [關於this綁定的四種方式](https://www.itread01.com/content/1544116971.html)
