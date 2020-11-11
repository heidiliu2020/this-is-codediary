###### tags: `JavaScript` `Variable`
# [week 16] JavaScript 進階 - 關於變數與資料型態

> 本篇為 [[JS201] 進階 JavaScript：那些你一直搞不懂的地方](https://lidemy.com/p/js201-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 瞭解 JavaScript 有哪些的資料型態
 原始型態與物件型態在變數宣告賦值上的差異
```

## JavaScript 資料型態

在第二週 [[JS101] 用 JavaScript 一步步打造程式基礎](https://lidemy.com/p/js101-javascript) 學習JavaScript 基礎時，我們就曾提及關於值的型態，以及該如何判斷資料型態。

而資料型態的不同，可能會造成一些操作結果與想像不符，這部分我們後面會進行討論。

關於值的型態，大致可分為原始型態和物件型態兩種：

#### 原始型態（Primitive types）

1. boolean（真偽值）：ture 和 false
2. number（數字）：例如 1、3.14159、NaN（無效的數字）
3. string（字串）：例如 `'Hello World'`
4. symbol（ES6）：例如 Sym
5. null：沒有值存在（no value）
6. undefined：值不存在（absence） 

#### 其他都屬於物件型態（Object types）

1. object（物件）：例如 {name: heidi, number: 99}
2. array（陣列）：例如 [1, 2, 3]
3. function（函式）
4. date...etc

### Immutable 與 Mutable

其中原始型態具有 Immutable（不可變動）的特性，相對於物件型態是 Mutable（可變的）。這裡指的不可變動不是「賦值」，而是不能改變原本的記憶體位置。

也就是說，若對其有任何變更（例如：新增、修改、刪除），就會回傳一個新值。以下列程式碼為範例：

- 原始型態：不改變原本的值

```javascript=
var str = 'hello'
var newStr = str.toUpperCase()
console.log(str, newStr)
// 印出 hello HELLO
```

- 物件型態：改變原本的值

```javascript=
var arr = [1]
arr.push(2)
console.log(arr)
// 印出 [1, 2]
```

## `typeof <value>`：用來判斷變數型態
　
我們可使用 `typeof` 來判斷變數的資料型態，輸入結果會回傳一個字串，語法範例如下：
 
```javascript=
console.log('typeof true', typeof true)
//輸出 typeof true boolean
```

結果得到 true 的資料型態是 boolean。

接著我們再看看其他範例結果：

![](https://i.imgur.com/rT6DR1N.png)

由結果可知，array  和 null 也屬於 object 型態，但前面不是說 null 的屬於原始型態嗎？這其實是 JavaScript 的歷史 bug，詳細內容可查閱下方參考資料：

> null 使用 typeof 運算子，回傳的結果會是字串 "object"，這指出 null 可被認為是象徵「無物件」（no object）的一種特殊物件值。（參考資料：[犀牛書](https://www.tenlong.com.tw/products/9789862764411)）

> 這其實是 JavaScript 最初發現的一個錯誤，然後被 ECMAScript 沿用至今。現在，null 被認為是物件的佔位符，從而解釋了這一矛盾。
> （參考資料：[你懂JavaScript 嗎？#4 型別（Types）](https://ithelp.ithome.com.tw/articles/10200841)）

以下是在 [MDN 網站](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/typeof) 列出 typeof 的可能回傳值：

![](https://4.bp.blogspot.com/-dBndQxibqJ8/V3xngW8lK5I/AAAAAAAAnDU/L5PVWe-8j-gL3vzcA4xMznzsgqq4AcnkACLcB/s1600/1.png)

### 利用 `typeof` 確認變數是否有使用到

我們還可以利用 `typeof` 來確認某個變數是否有使用到（是否有被宣告），以下列程式碼為例：

```javascript=
var a
console.log(typeof a)
// 宣告 a 但還沒賦值，所以結果是 undefind
```

若沒有先宣告變數 a，直接使用 `typeof` 檢查也會得到相同結果：

```javascript=
console.log(typeof a)
// undefind
```

若應用在判斷句，在有宣告變數 a 的情況：

```javascript=
var a = 10
if (typeof a !== 'undefined') {
  console.log(a)
}
// a = 10，所以印出 10
```

在不宣告變數 a 的情況下，直接利用 `typeof` 進行判斷：

```javascript=
if (typeof a !== 'undefined') {
  console.log(a)
}
// 因為 a 是 undefined ，不符合判斷句，不會印出任何東西
```

若直接判斷變數 a 是否等於 undefined，就會出現未定義 a 的錯誤：

```javascript=
if (a !== 'undefined') {
  console.log(a)
}
// 因為 a 不存在，會印出錯誤訊息：ReferenceError: a is not defined
```

因此，若使用 typeof() 來判斷 a 是否為 undefined，就能夠避免出現錯誤，導致程式中斷。

## `Array.isArray()`：判斷變數是否為陣列

若想檢查是否為陣列，可使用函式 `Array.isArray()`，檢查傳入的值是否為一個 Array，範例如下：

```javascript=
var a = [1, 2, 3];
console.log(Array.isArray(a))
// true
console.log(Array.isArray([]))
// true
```

但使用時須注意，一些較舊的瀏覽器並不支援 `Array.isArray()` 這個語法，因此更推薦的方法如下。

## `Object.prototype.toString`：用來判斷型態

`Object.prototype.toString` 是另一種判斷型態的方式，結果也會比 `typeof` 還要準確，尤其物件型態會顯示更詳細的類別。

語法範例如下：

```javascript=
console.log(Object.prototype.toString.call(null))
console.log(Object.prototype.toString.call([]))
console.log(Object.prototype.toString.call(1))
console.log(Object.prototype.toString.call(new Date))

// [object Null]
// [object Array]
// [object Number]
// [object Date]
```

## 等號賦值與記憶體位置

在課程第二週 JS101 的「[從 Object 的等號真正的理解變數](https://github.com/heidiliu2020/this-is-codediary/blob/master/week2_JavaScript%20%E5%9F%BA%E7%A4%8E.md#%E5%BE%9E-object-%E7%9A%84%E7%AD%89%E8%99%9F%E7%9C%9F%E6%AD%A3%E7%9A%84%E7%90%86%E8%A7%A3%E8%AE%8A%E6%95%B8)」中我們也曾提到相關概念。

若將變數視為一個箱子，在放入數字的情況下，兩者會相等：

```javascript=
var a = 30
console.log(a === 30)
// 印出 true，兩者相等
```

但如果在變數 obj 裡放入物件，結果卻是不相等：

```javascript=
var obj = {
  a:1
}
console.log(obj === {a:1})
// 印出 false，兩者不相等
```

可想像成是「記憶體位置不同」導致的結果。儘管兩個箱子儲存的數值相同，但因記憶體位置不同，指向的元素不同，所以不會相等。

如下方示意圖：

![](https://i.imgur.com/ZAY1PrG.png)

### 關於 `=` 等號賦值

如果換成下列情形，也就是將 obj 賦值給 obj2 時：

```javascript=
var obj = {
    a:1
}
var obj2 = obj

console.log(obj === obj2)        // 印出 true
```

兩者理所當然會相等，此時若以 `obj2.a = 2` 更改 obj2 物件中 a 的值，會連同 obj 的值也一起更動：

```javascript=
var obj = {
    a:1
}
var obj2 = obj          // 賦值
obj2.a = 2

console.log('obj', obj2)       // obj { a: 2 }
console.log('obj2', obj2)      // obj2 { a: 2 }
console.log(obj === obj2)      // 印出 true，兩者相等
```

之所以 obj 的值也一起被更改，是因為 obj 和 obj2 指向了相同記憶體位置（0x01），也就是指向同一個物件：

![](https://i.imgur.com/cgfJMmd.png)

但如果以 `obj2 = {b:1}` 將 obj2 賦值一個新的物件，此時就會指向一個新的記憶體位置。以下方程式碼為例：

```javascript=
var obj = {
  a:1
}
var obj2 = obj
obj2.a = 2
obj2 = {b:1}

console.log('obj', obj2)         // obj { a: 2 }
console.log('obj2', obj2)        // obj2 { b: 1 }
console.log(obj === obj2)        // 印出 false，兩者不相等
```

會發現 obj2 和 obj 不相等，這是因為「往裡面放東西」與「改放全新的東西」是兩件完全不同的事情。後者會指向一個新的記憶體，可參考下圖理解：

![](https://i.imgur.com/JkzALWA.png)

若以陣列為例，會得到相同的結果，以下列程式碼為例：

```javascript=
var arr = []
var arr2 = arr

console.log(arr, arr2)
// 印出 [] []
arr2 = ['arr2']          // 賦值，指向新的記憶體位置
console.log(arr, arr2)
// 印出 [] ['arr2']
```

賦值後的 arr2 會指向新的記憶體位置，因此兩者的值會不相同，可想像成 `arr: 0x10` 和 `arr: 0x20`。 

### `==` 與 `===` 的差別

- `=`：代表賦值
- `==` 和 `===`：均用來判斷是否相等，差別在於是否判斷值的型態。原因是 `==` 判斷過程會進行型態轉換

> 結論：盡量使用三個等號進行判斷，如此最能夠避免因型態不同而發生錯誤。

以下列程式碼為例：

```javascript=
console.log(0 == '0')
// true
console.log(0 === '0')
// false，因為數字和字串型態不同
```

再以陣列作為範例：

```javascript=
var arr = [2]
var arr2 = [2]
console.log(arr === arr2)
// false
```

之所以兩者不會相等，和前面提到的「記憶體位置不同」有關，可想像成：

```
0x01: [2]
0x02: [2]

arr: 0x01
arr2: 0x02
```

也因此，不管裡面放相同參數或均為空陣列，兩者都不會相等，一定要加上 `arr2 = arr` 才會使等號成立。

同理，當我們比較空陣列或空物件時，結果也不會相等，因為比較的是兩者的記憶體位置：

```javascript=
console.log([] === [])
// false
console.log({} === {})
// false
```

### 特例：NaN

- NaN：Not a Number（無效的數字），型態為 Number

在什麼樣的情況下會產生 NaN 呢？以「將字串轉換成 Number」為例，因無法轉換所以會得到 NaN：

```javascript=
var a = Number('hello')
console.log(a)
// NaN
console.log(typeof a)
// number
```

這時如果再以等號進行判斷，結果會是：

```javascript=
var a = Number('hello')
console.log(a === a)
// false
```

> 什麼～～～～～？！同一個變數結果竟然會不相等？！（震驚ing）

為何會發生自己不等於自己的情況呢？這是因為 `NaN === NaN` 判斷結果不相等造成，屬於特殊案例。

至於要如何檢視變數是否為 NaN，可使用函式 `isNaN()`：

```javascript=
var a = Number('hello')
console.log(isNaN(a))
// true
```

- 參考資料：[JS Comparison Table](https://dorey.github.io/JavaScript-Equality-Table/)

### let 與 const

接著談到宣告變數的方式，除了習慣使用的 var（variable 變數），在 ES6  還引入了 let 和 const（constant 常數） 兩種宣告方式。其中 let 和 let 的差別在於作用域不同，在之後的 Scope 章節會再詳細說明。

#### let

- 宣告時就要給初始值
- 宣告後就不能再改變

但我們可以去改變物件 obj 裡面的值，如以下範例：

```javascript=
const obj = {
  number: 1
}

obj.number = 2
```

如果直接賦予 obj 新的值，就會出現錯誤：

```javascript=
const obj = {
  number: 1
}

obj = {number: 2}
// TypeError: Assignment to constant variable.
```

這和前面提到的記憶體位置觀念相同，const 說的不能改變，其實是不能改變「該記憶體位置」。obj 是存記憶體位置，number 則是存 value。也因此賦值給 obj 就代表改變記憶體位置。

```javascript=
0x10: {
number: 1
}
0x20: {
number: 2
}

obj: 0x20
// 常數 obj 的記憶體位置被改變，所以出現錯誤
```

參考資料：

- [深入探討 JavaScript 中的參數傳遞：call by value 還是 reference？](https://blog.huli.tw/2018/06/23/javascript-call-by-value-or-reference/)
