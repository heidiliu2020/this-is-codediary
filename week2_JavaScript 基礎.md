###### tags: `JavaScript`

# [week 2] JavaScript 基礎 - 基本語法、值的型別、變數、迴圈、函數

> 本篇為 [[JS101] 用 JavaScript 一步步打造程式基礎](https://lidemy.com/p/js101-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 你能靈活運用變數、迴圈、判斷式等等基本概念來解題
 P1 你能一行行的說出現在程式在做什麼
 P1 你知道「回傳」跟「輸出」的差異
 P1 你可以把用文字寫好的演算法轉成程式碼
```

---

## JavaScript 是什麼？

JavaScript 是一種物件導向（Object-oriented programming）的腳本語言（Script language），主要用來改進 Web 瀏覽器的客戶端體驗。

腳本語言是一種直譯語言，因不需進行編譯，在撰寫和除錯上較為方便；但缺點是執行效率比不上編譯語言，且無法單獨執行，必須仰賴運行環境。例如：HTML 網頁中的 JavaScript 需要瀏覽器支援才能執行。

直到 Node.js 出現後，提供了 JavaScript 在瀏覽器以外的運行環境。目前實務開發中，通常使用瀏覽器的開發者工具來進行 debug（除錯）。

參考資料：

1. [JavaScript 基本認識 - JavaScript 入門學習筆記](http://test.domojyun.net/MEMO/JavaScript/)
2. [編譯語言 VS 直譯語言- Po-Ching Liu - Medium](https://medium.com/@totoroLiu/%E7%B7%A8%E8%AD%AF%E8%AA%9E%E8%A8%80-vs-%E7%9B%B4%E8%AD%AF%E8%AA%9E%E8%A8%80-5f34e6bae051)
3. [[心得分享] JS = 專為瀏覽器而生的 script (腳本語言)](https://medium.com/codememo/%E5%BF%83%E5%BE%97%E5%88%86%E4%BA%AB-js-%E5%B0%88%E7%82%BA%E7%80%8F%E8%A6%BD%E5%99%A8%E8%80%8C%E7%94%9F%E7%9A%84-script-%E8%85%B3%E6%9C%AC%E8%AA%9E%E8%A8%80-60a628988710)
4. [JavaScript+jQuery Mobile+Node.js跨平台網頁設計範例教本(電子書)](https://books.google.com.tw/books?id=8J_3BQAAQBAJ&pg=SA1-PA5&lpg=SA1-PA5&dq=%E8%85%B3%E6%9C%AC%E8%AA%9E%E8%A8%80%E6%98%AF%E4%B8%80%E7%A8%AE%E7%9B%B4%E8%AD%AF%E8%AA%9E%E8%A8%80%EF%BC%8C&source=bl&ots=oHnBL1CGny&sig=ACfU3U0hgWb-mnejRGX2ZhsClobxafUnqA&hl=zh-TW&sa=X&ved=2ahUKEwiD1Z7Q8I_qAhXMw4sBHU3IDo0Q6AEwBXoECAkQAQ#v=onepage&q=%E8%85%B3%E6%9C%AC%E8%AA%9E%E8%A8%80%E6%98%AF%E4%B8%80%E7%A8%AE%E7%9B%B4%E8%AD%AF%E8%AA%9E%E8%A8%80%EF%BC%8C&f=false)

## Node.js 環境建置

> 進入[官網](https://nodejs.org/en/)會看到下列文字：
> Node.js® is a JavaScript ==runtime== built on Chrome's V8 JavaScript engine.
> （Node.js 一個能執行 JavaScript 的==運行環境==，以 Google Chrome V8 引擎為核心。）

安裝完成後，就可以在 Command Line 輸入指令：

### `node -v`：查看目前 Node.js 版本號

出現版本號就代表安裝成功。

![](https://i.imgur.com/IXXGrb0.png)


### `node`：直接在 CML 開啟 Node 環境

可在終端機輸入指令。按 Crtl+C 或輸入就`.exit` 即可退出。

![](https://i.imgur.com/L3DJaob.png)

---

## 如何執行 JavaScript 文件？

### 在瀏覽器執行

1. 寫在 HTML 文件中的 `<script >` 標籤內

![HTML JS](https://i.imgur.com/r1gMPnm.png)

2. 用瀏覽器開啟該檔案，點選右鍵選單的檢查，進入開發者工具介面
3. 可在 Console 主控台檢視或直接撰寫。通常用來測試代碼的可行性、或直接 debug 抓錯

![Google JS](https://i.imgur.com/1U5LzRD.png)

### 在 Command Line 執行

1. `vim index.js`：輸入 vim 指令建立檔案 index.js，並且編輯內容

> 也可使用 VSCode、Sublime 等程式碼編輯器來撰寫程式碼

![vim 介面](https://i.imgur.com/4392JMK.png)

2. `node index.js`：在 CML 執行檔案 index.js

![node 執行](https://i.imgur.com/zkJPsbj.png)

---

## 基本語法

### console.log()：將值輸出到瀏覽器控制台

若要輸出字串，需用 '單引號' 或 "雙引號" 括起來。

`console.log(’Hello World’)　　//　輸出值：Hello World`

### 算術運算

`+`：加
`-`：減
`*`：乘
`/`：除
`%`：取餘數（例如 10 % 3，結果是 1）

### 邏輯運算

- 邏輯運算子常在 if 判斷式中和布林值（true or false）一起使用
- 在 JavaScript 中會被判定為 false 的值為：`0`、`""`、`null`、`false`、`undefined`、`NaN`

#### `||`：or

只要其中一個是 true 就會返回 true，除非全部為 false。意即只要其中一個條件滿足就成立。

![or](https://i.imgur.com/v0j07Ua.png)

#### `&&`：and 

全部為 true 才會是 true，否則均返回 false。意即全部條件都必須成立。

![and](https://i.imgur.com/dbhRafe.png)

#### `!`：not

做反向。

![!](https://i.imgur.com/2scURgc.png)

### `||` 與 `&&` 的短路性質

使用最短的路徑來求值，又稱[短路求值](https://zh.wikipedia.org/wiki/%E7%9F%AD%E8%B7%AF%E6%B1%82%E5%80%BC)。只有當第一個運算數的值無法確定邏輯運算的結果時，才對第二個運算數進行求值。例如：

```
1. 當 or 的第一個運算數為 true 時，最後結果必定為 true
2. 當 and 的第一個運算數的值為 false 時，其結果必定為 false
```

在這種情況下，就不需要知道第二個運算數的具體值。也就是短路性質。

範例：

```
console.log(3 || 10)　　　　//　output 3
console.log(false || 10)　　//　output 10
console.log(3 && 10)　　　　　//　output 10
console.log(false && 3)　　//　output false
```

`var obj = obj || { };　　//　如果 obj 存在就維持原樣，如果不存在就給予空物件`

`var student = name || "小明";　　//　如果沒有 name 就用預設為小明。用 || 來設定變數預設值`

> 更多短路邏輯的運用可參考這篇：[想知道&&與&及||與|之間的區別嗎？ | 程式前沿](https://sweeteason.pixnet.net/blog/post/43022921-javascript-%E5%9F%BA%E7%A4%8E%E6%89%93%E5%BA%95%E7%B3%BB%E5%88%97-%28%E4%B8%89%29---%E9%82%8F%E8%BC%AF%E9%81%8B%E7%AE%97%E5%AD%90%EF%BC%8C%E8%88%87)

### 位移運算子：`<<` 與 `>>`

首先來複習二進位制：


0100 = 2^3*0 + 2^2*1 + 2^1*0 + 2^0*0 = 2^2 = 4
1000 = 2^3*1 + 2^2*0 + 2^1*0 + 2^0*0 = 2^3 = 8

#### <<：將位元往左移一位，可作為乘以 2。

![<< 左移](https://i.imgur.com/7f9YfSO.png)

#### >>：將位元往右移一位，可作為除以 2。若無法整除則會直接捨去。

![>> 右移](https://i.imgur.com/RP4O8zV.png)

- 由於電腦使用的是二進位系統，位元運算的速度通常會快於乘法和除法運算。

### 位元運算

and
or
xor
not
(待補)

---

### 遞增、遞減運算子：`++` 與 `--`

```
var a = 0　　//　等號是賦值
a = a + 1　　//　可簡化為　a += 1 或 a++
a = a - 1　　//　可簡化為　a -= 1 或 a--
```

#### 遞增（`++`，increment）：運算前或運算後「遞增」
#### 遞減（`--`，decrement）：運算前或運算後「遞減」

> 其中以 ++ 運算子為例：

++ 運算子的回傳值，取決於相對於運算元的位置。

1. 先遞增（`++a`）：用在運算元之前，執行遞增，然後回傳遞增後的值。
2. 後遞增（`a++`）：用在運算元後方，執行遞增，然後回傳未遞增前的值。


```
var i = 1, j = ++i　　//　i 與 j 兩者皆為 2
var i = 1, j = i++　　//　i 為 2, j 是 1　
```

> 若以邏輯運算為例：

1. 先遞增
```
var a = 0

console.log(++a && 30)　　　//　印出 30，此時 a 為 1
console.log('a:' , a)　　　//　 印出 a:1

//　先跑 a+=1，再 console.log(a && 30)
```
2. 後遞增

```
var a = 0

console.log(a++ && 30)　　　//　印出 0，此時 a 為 0
console.log('a:' , a)　　　//　 印出 a:1

//　先跑 console.log(a && 30)，再 a+=1
```

---

## 值的型態

### JavaScript 的資料型態可分為：

- 原始型態（primitive types）

1. boolean（真偽值）：ture 和 false
2. number（數字）：例如 1、3.14159、NaN（無效的數字）
3. string（字串）：例如 `'Hello World'`
4. null：沒有值存在（no value）
5. undefined：值不存在（absence） 

- 其他都屬於物件型態（object types）
1. array（陣列）：例如 [1, 2, 3]
2. function（函式）
3. date...etc

### `typeof <value>`：用來判斷參數型態

```
console.log('typeof true', typeof true)
//輸出 typeof true boolean
```

![](https://i.imgur.com/rT6DR1N.png)

在 [MDN 網站](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/typeof) 列出 typeof 的可能回傳值：

![](https://4.bp.blogspot.com/-dBndQxibqJ8/V3xngW8lK5I/AAAAAAAAnDU/L5PVWe-8j-gL3vzcA4xMznzsgqq4AcnkACLcB/s1600/1.png)

> null 使用 typeof 運算子，回傳的結果會是字串 "object"，這指出 null 可被認為是象徵「無物件」（no object）的一種特殊物件值。（參考資料：[犀牛書](https://www.tenlong.com.tw/products/9789862764411)）
> 
> 這其實是 JavaScript 最初發現的一個錯誤，然後被 ECMAScript 沿用了。現在，null 被認為是物件的佔位符，從而解釋了這一矛盾
> （參考資料：[你懂JavaScript 嗎？#4 型別（Types） ](https://ithelp.ithome.com.tw/articles/10200841)）

---

## 變數（Variable）

- 用來暫時儲存資料的地方
- 想像成裝東西的箱子，`宣告變數`是將這個箱子取名，`加上等號賦值`是在裡面裝東西

```
其他情形：

1. 當宣告變數，卻沒有給這個變數賦值，用 console.log 會印出 undefined
2. 若想印出一個沒有宣告的文字，會出現錯誤訊息 not defined
```

### 宣告變數

- 不可用數字開頭
- 不可取名為[保留字詞](http://www.w3bai.com/zh-TW/js/js_reserved.html)，例如 var、function、for 
- 變數盡量用語譯化的方式命名，例如 peopleCount、total
- 變數的取名規則需統一，可分為下列兩種：

1. 蛇式命名（snake_case）:名稱中間的標點以底線連接
`var this_is_a_box`
3. 駝峰式命名（camelCase）:除了第一個單詞外，後面的單詞首字母均為大寫
`var thisIsABox`

### 變數的儲存模型

前面提到變數是像箱子的儲存模型，參考 Huli 寫的[這篇文章](https://medium.com/@hulitw/variable-and-frontdesk-a53a0440af3c)，舉以下範例來說明其特性：

```
// 範例一

var A = [1, 2, 3]
var B = [1, 2, 3]
var C = A
console.log(A == B)　　　// 回傳 false
console.log(C == B)　　　// 回傳 false
console.log(C == A)　　　// 回傳 true
```

1. 即使代表的東西相同，但 A 和 B 其實存放在不同的格子，而 A 和 C 相同。
2. `==` 與 `===` 是去看「格子的內容」是否相等，而不是檢查「所代表的東西」是否相等。

```
// 範例二

var F = [1, 2, 3]
var G = F

// 往裡面放東西
F.push(4)　　　　　　　　
console.log(F, G)　　　// 回傳 [1, 2, 3, 4] [1, 2, 3, 4]

// 改放全新的東西
F = [1, 3, 5]　　　　　
console.log(F, G)　　　// 回傳 [1, 3, 5] [1, 2, 3, 4]
```

1. 「往裡面放東西」與「改放全新的東西」是兩件完全不同的事情。
2. 一般的變數存資訊，物件存記憶體位置。
- 一般的變數：變數裡面存的內容就真的是那個資訊，例如：數字、字串
- 物件：變數裡面存的內容其實是「指引」，指引存的是記憶體位置，例如：陣列或物件

### 變數的運算

- 注意值的型態：字串和數字相加時，會變成字串相加

```
var a = '10'https://www.bilibili.com/video/BV1Hz411i7ph/
var b = 20
console.log(a + b)　　　// 印出 1020
```

解決方法：

1. `console.log(Number(a) + b)`：用 `Number()` 將字串轉成數字
2. `console.log(parseInt(a, 10) + b)`：用 `parseInt()` 將字串轉換成整數，10 代表預設的十進位 

- 注意[浮動數誤差](http://blog.dcview.com/article.php?a=VmhQNVY%2BCzo%3D)：電腦在儲存小數值可能會產生誤差

```
var a = 0.1 + 0.2
console.log(a == 0.3)　　　// 印出 false
console.log(a)            // 印出 0.30000000000000004

```

### 萬年經典題：`==` 與 `===`

`=`：代表賦值
`==` 和 `===`：均用來判斷是否相等

```
var a = 10 == 10　　// 會從右執行到左，10 == 10 true
console.log(a)　　　// 所以 a 印出 true
```

差別在於 `===` 會判斷「型態」：

```
console.log(0 == ’0’)  // true
console.log(0 === ’0’)  // false，因為數字和字串型態不同
```

> 永遠都用三個等號，如此最能夠避免型態不同而發生錯誤。

---

## 陣列（Array）

> 在寫程式時遇到重複的動作，一定有方法能夠優化。

- 通常用來存放性質相近的資料
- 想像成一個列表物件，裡面含有幾個數值

```
// 陣列基本操作

box[1, 2, 3]
// box 代表陣列名稱
// [] 內的值代表索引值（index），陣列中索引是從 0 開始

console.log(box.length)　　　　　　　　// 印出 3，代表陣列長度
console.log(box[1])　　　　　　　　// 索引為 1 時，印出 2
```

```
// 範例練習

var score = [20, 5, 100]
console.log(score, score.length)　　 　//　印出 [20, 5, 100] 3
console.log(score[score.length - 1])　//　印出 100，陣列長度減一代表最後一個數的索引
score.push(60)　　　　　　　　　　　　　//　在陣列最後新增一個數
score[score.length] = 80　　　　　　　//　在陣列最後新增一個數
console.log(score.length)　　　　　　//　印出 5
```

## 物件（Object）

- 物件是一批相關的數據或功能
- 通常包含幾個變數及函式，當它們包含在物件中時被稱做屬性（properties）或函式（methods）

```
// 試著建立一個物件

var heidi = {
    name: 'heidi',
    scores: [20, 60, 100],
    address: 'Japan',
}

console.log(heidi, typeof heidi) // 印出物件屬性、型別
console.log(heidi.address)　　　　// 用點（.）取出物件屬性

var key = 'name'
console.log(heidi[key])　　　　　　// 這裡 heidi[key] 等於 heidi.name
```
![](https://i.imgur.com/XKReoRy.png)

---

### 從 Object 的等號真正的理解變數

由 `console.log(1 === 1)`，會回傳 true 這個例子，來判斷下列情形：

```
console.log([] === [])
console.log([1] === [1])
console.log({} === {})
console.log({a: 1} === {a: 1})
```

結果卻是：

![](https://i.imgur.com/x9CYSuk.png)

變數是一個箱子，在放入數字的情況下：

```
var a = 30
console.log(a === 30)    // 印出 true，兩者相等
```

但如果在變數裡放入物件，結果卻會如下：

```
var obj = {
    a:1
}
console.log(obj === {a:1})    // 印出 false，兩者不相等
```

可以想像成「記憶體位置」：儘管兩個箱子儲存的數值相同，但因記憶體位置不同，指向的元素不同，所以不會相等。

![](https://i.imgur.com/ZAY1PrG.png)

如果換成下列情形：

```
var obj = {
    a:1
}
var obj2 = obj
console.log(obj === obj2)        // 印出 true
```

兩者理所當然會相等，但若將 `obj2.a = 2`：更改 obj2 物件中 a 的值，會連 obj 的值也一起更動：

```
var obj = {
    a:1
}
var obj2 = obj
obj2.a = 2
console.log('obj', obj2)         // obj { a: 2 }
console.log('obj2', obj2)        // obj2 { a: 2 }
console.log(obj === obj2)        // 印出 true，兩者相等
```

會發現 obj 的值也一起被更改了。這是因為 obj 和 obj2 指向同一個記憶體位置，指向同一個物件。

![](https://i.imgur.com/cgfJMmd.png)

但如果 `obj2 = {b:1}`：obj2 等於一個新的物件，會指向一個新的記憶體位置：

```
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

這是因為「往裡面放東西」與「改放全新的東西」是兩件完全不同的事情。後者會指向一個新的記憶體，可參考下圖理解：

![](https://i.imgur.com/JkzALWA.png)

---

## 判斷式

判斷式在 JavaScript 中用來==控制流程==。當指定的條件成立時，就會執行後續的指令。判斷式的語法有兩種：`if...else` 和 `switch`。

### `if-else` statement

- if 後面的小括號內：條件式，由比較運算子或邏輯運算子組成
- 當條件成立時：會執行 if 大括號內的程式碼：
- 當條件不成立，但也想執行特定動作：在 if 大括號外面使用 else

```
// 基本架構

if (條件){
  如果條件成立，執行 A;
} else {
  如果條件不成立，執行 B;
}
```

```
// 練習：判斷是否為 5 的倍數

var number = 10
if (number % 5 === 0) {
    console.log('是5的倍數')
} else {
    console.log('不是5的倍數')
}
// 印出 是5的倍數
```

### if-else if statement

- 當判斷情境更複雜時，搭配 `else if`，用來新增條件判斷
- `else if` 可以有很多個

```
// 基本架構

if (條件一) {
  如果條件一成立，執行 A;
} else if (條件二) {
  如果條件一不成立；但條件二成立，執行 B;
} else {
  如果條件一跟二都不成立，執行 C;
}
```

```
// 練習：判斷是否及格

var score = 70

if (score == 100) {
    console.log('you are no1!')
} else if (score >= 60){
    console.log('pass')
} else {
    console.log('fail')
}
// 印出 pass
```

```
var kg = 60
var m = 1.6
var BMI = kg / (m**2)　　　// 指數

console.log('BMI:', BMI.toFixed(2))
// 印出 BMI: 23.44，函數 toFixed() 可取到小數點下的指定位數

if (BMI < 18.5) {
    console.log('體重過輕')
} else if (BMI < 24) {
    console.log('正常範圍')
} else if (BMI < 27) {
    console.log('過重')
} else if (BMI < 30) {
    console.log('輕度肥胖')
} else if (BMI < 35) {
    console.log('中度肥胖')
} else {
    console.log('重度肥胖')   
}
```

### switch case

- 通常用於「有很多條件」要判斷的情況，適合用來處理只有「整數」或「字元」類型的資料
- switch-case 判斷式沒有辦法處理「數值範圍」的問題
- 每個 `case 條件:` 後方都會加上 `break`：用途是停止執行後面的程式碼，否則 `switch` 會從匹配的 `case` 標籤開始執行到尾端

```
// 基本架構

switch(參數){
case 條件一:　　　　　　　　// 如果 n===條件一 就從這裡開始
  條件一成立時執行區塊一;
  break;            　    // 在這裡停止執行
  
case 條件二:
  條件二成立時執行區塊二;
  break;
  
default:　　　　　　　　　　// 如果都沒有找到相同值
  條件都不成立時執行區塊三;
  break;
}
```

#### switch case vs if else 

- 效能差異：當比對的參數多時，switch 的可讀性和效能較佳
- 判別差異：switch 在判斷上採取嚴謹模式（同等性由 `===` 運算子判斷），亦即型別也要相同

參考資料：
1. [switch...case 和 if...else效率比較和優化](https://www.itread01.com/content/1549602198.html)
2. [JavaScript 基礎知識-switch & if else 的判別差異](https://ithelp.ithome.com.tw/articles/10211561)

---

### 三元運算子（ternary）

- 也可稱作 Conditional Expression（條件表達式）
- 其實就是 if-else 的簡單寫法，適合巢狀結構（建議最多一層）

```
// 語法：

condition ? A : B　　　　// 條件 ? 符合條件結果 : 不符合條件結果

// 範例：

console.log(10 > 5 ? 'bigger' : 'smaller')　　// 印出 bigger
```

以判斷是否及格為例：

```
// if-else 寫法：

var score = 60
var message = ''

if (score >= 60) {
    message = 'pass'
} else {
    message = 'fail'
}

// 三元運算子寫法：

var score = 60
var message = score >= 60 ? 'pass' : 'fail'
// 兩種寫法均回傳 pass
``` 

---

## 迴圈（loop）

- 迴圈是程式流程控制的一環，用來重複執行相似的工作
- 必須設定==終止條件==，否則執行時將進入無窮迴圈（可按 `Ctrl+C` 跳出）

### do…while 迴圈

- 先執行後才判斷條件，代表迴圈主體至少會被執行一次

```
// 語法：

do {
  statement　　　　　　　// 先執行一次
} while (condition)　　// 若符合條件，會再進行下一次迴圈

// 範例：

var i = 1

do {
    console.log(i)       // 印出 1 到 100 
    i++
} while (i<=100)         // 直到 i = 101 時跳出迴圈

console.log('i=', i)     // 印出 i = 101
```

### while 迴圈

- 其實就是 do…while 迴圈的變形版本。差別在於 do…while 會至少被執行一次，才進行條件判斷
- 由於迴圈主體需至少執行一次的情況並不常見，通常還是會使用 while 迴圈

```
// 語法：

while (condition) {
    statement
}

// 將上方 do…while 範例改寫，會得到相同輸出結果：

var i = 1

while (i <= 100) {　　　// 直到 i = 101 時跳出迴圈
    console.log(i)　　　// 印出 1 到 100
    i++
}

console.log('i=', i)　// 印出 i = 101
```

### for 迴圈

- 類似 while 迴圈，但 for 迴圈通常用於「已知重複次數」的情況，也就是設定初始值和終止條件
- [for 迴圈基本架構](https://www.wibibi.com/info.php?tid=182)，需要三個運算式作為參數：initialize（初始值）、condition（條件判斷）、increment（遞增迴圈變數）
- 三個運算式可省略任何一個，但中間的兩個分號必須存在。例如：`for (;;)` 會是無窮迴圈

```
// 語法：

for (初始值; 終止條件; 遞增迴圈變數){
    要執行的程式碼
}

// 也就是：

for ( 變數 = 初始值 ; 變數 < 限制值 ; 變數 + 步進值 ) {
　要執行的程式碼
}
```

```
// 範例：

for (var i = 1; i<=100; i++) {
    console.log(i)
}
// 印出 1 到 100
```

```
// 可和 while 迴圈進行對照：

var i = 1　　　　　　// 初始值
while(i <= 100) {　　 // 終止條件
    console.log(i)
    i++            // i 每一圈要做的事情
}
// 同樣印出 1 到 100
```

### break 與 continue

- 用來改變迴圈的執行流程
- 只能用在迴圈或 switch 述句中

#### `break`（中斷）：中斷整個迴圈語句，也就是「跳出」迴圈區塊

```
// 範例：

var i = 1

do {
    console.log(i)　　　 // 印出 1 到 6
    if (i === 6) {
        break　　　　  　// 當 i 等於 6 時跳出迴圈
    }
    i++
} while (i <= 10)

console.log('i=', i)　　// 印出 i = 6
```

執行結果如下圖：

![break](https://i.imgur.com/V9mi4KK.png)


#### `continue`（繼續）：繼續下一次迴圈語句，會忽略在 continue 之後的語句，直接跳到下一次的迴圈開頭

```
// 範例：

var i = 1

do {
    console.log(i)　　　// 印出 1 到 10
    i++
// 當 i 為奇數時，直接跳到　while 檢查條件，進入下一個迴圈
    if (i % 2 === 1) {　　　　
        continue
    }
    console.log('i++', i)　　// 只會印出偶數的 i
} while (i <= 10)

console.log('i=', i)　　// 印出 i = 11
```
執行結果如下圖：

![continue](https://i.imgur.com/FoASLD1.png)

--- 

## 函式（Function）

以前學的國中數學 `y = f(x)` 其實就是一種函式：

```
y = f(x)
y：回傳值 / f(x)：函式 / x：參數
```
```
// 範例：

y = f(x) = 2x
f(1) = 2*1 = 2

// 也可以是多項參數：

y = f(a, b, c) = a+2b+3c
f(1, 1, 1) = 1+2+3 = 6

```

### 最基本的函式結構

在 JavaScript 的函式架構：

```
function 函數名稱(參數) {
   return 要回傳的內容
}
```
```
// 範例：

function abc(a, b, c) {
    return a + 2*b + 3*c
}

console.log(abc(1, 1, 1))　　　　// 印出 6
```

範例：在陣列產生 n 個元素的函式

```
function generateArray(n){
    var result = [] 　　　　　　　　// 宣告一個變數為空陣列
    for(var i = 1; i<=n; i++){
        result.push(i)　　　　　　// 把數字放進陣列
    }
    return result         　　   // 記得回傳結果
}

// generateArray(3) => [1, 2, 3]
// generateArray(10) => [1, 2, 3, ... , 10]

console.log(generateArray(0))　　// 印出 [] 空陣列
console.log(generateArray)
// 函式後面需加上括號才能呼叫，否則會印出 [Function: generateArray]
```

也可以不寫 return，寫法如下：

```
function log(n) {
    for(var i = 1; i <= n; i++) {
    console.log(i)　　　　// 直接在函式裡面印出結果
    }
}
log(10)　　　　　// 回傳 1 到 10
```

範例：印出 1~100 的偶數

> 小技巧：不知如何解題時，可先從寫下 Function 架構開始

```
// 先寫出函式架構：

function print1to100() {

}
print1to100()
```

要產生 1~100 的數字，可能需要迴圈：

```
function print1to100() {
    for (var i=1; i<=100; i++)
    logEven(i)  　　　　　　　　　　// log 偶數
}
```

需要再寫一個函式，來判斷 i 是否為偶數：

```
function logEven(number) {
    if (number%2 === 0)　　　　　// 若為偶數，就印出數字
    console.log(number)
}
function print1to100() {
    for (var i=1; i<=100; i++)
    logEven(i)  　　　　　　　　　
}

print1to100()　　　　　　　　　// 印出 1~100 的偶數
```

### 宣告函式的不同種方式

- 在語法上大致分為 4 種方式，可參考這篇介紹：[函數定義 (Function Definition) 的 100 種寫法](https://ithelp.ithome.com.tw/articles/10207740)
- 其中最常使用的為宣告式，以及匿名表達式。

#### 第一種：宣告式（Function Declarations）

- 是最常見的標準寫法。
- 使用 `function` 關鍵字作函數的宣告和定義。

```
function hello() {
   console.log('hello')
}

hello()　　　　　// 執行函式，回傳 hello
```

#### 第二種：匿名函數（anonymous function），或稱匿名表達式

- 前面提到函式也是一種資料型態。因此可先宣告一個變數，再定義一個函數內容放到該變數裡。
- 此方式定義的函數，實際上是匿名函數，只是將函數存在某個變數裡。

```
var hello = function (a, b) {
    return a + b
}

console.log(hello(3, 6))　　　　　// 印出 9
```

### console.log() 括號內可傳入函式

我們在先前的範例，console.log() 都只傳入數字、字串等，但其實也可傳入函式，如以下範例：

```
function transform(arr, transformFunction) { 
    var result = []
    for(var i=0; i<arr.length; i++) {
        result.push(transformFunction(arr[i]))
    }
    return result
}

function double(x) {　　　　　　
    return x*2
}

// transform([1, 2, 3], double) => [2, 4, 6]

console.log (
    transform([1, 2, 3] ,double)　　　
)
// 傳入 transform 函式，並且在 transform 函式中傳入 double 函式
// 印出 [2, 4, 6]
```

上述範例中，在 transform 函式裡面，double 函式取代了參數 transformFunction，也就是執行 double 函式內部的運算。

也可直接把整組函式丟到 console.log() 括號內，就不需再額外命名。好處是可直接修改函式定義，如以下範例：

![function 傳入 log](https://i.imgur.com/IatovLR.png)

---

### 引數（Argument）與參數（Parameter）

參數：方法定義中的變數
引數：在呼叫方法時真正傳入的值

```
// 範例：
function add(a, b) {        // 參數就是 a 和 b
    console.log(arguments)    // 可印出引數
    return a+b
}

add(2, 5)                   // 引數是 2 和　5
```

結果如下圖，印出 `{ '0': 2, '1': 5 }`。

![](https://i.imgur.com/9BaESkL.png)

#### Argument 物件

由上述範例可知，`arguments` 物件其實是種「類陣列物件」。

- arguments[0]：代表第一個引數
- arguments[1]：代表第二個引數

並且具有 length 屬性。

```
function add(a, b) {
 console.log(arguments.length)    // 印出 2
 return a+b
}
add(2, 5)
// 一共傳了兩個引數
```

> 參考[MDN - Arguments 物件](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/arguments)，由於 arguments 帶其他屬性，可能因此需要用物件的方式來儲存資訊。

### function 傳參數的運作機制（待補）

```
function swap(a, b) {
在函式當傳入值時就會這樣跑
  var a = number1
  var b = number2
  所以只是把 number1 跟 number2 給複製了一份
  number1 number2 a b 是各自獨立的變數

 var temp = a
 a = b
 b = temp
 console.log("a: b:", a, b)
}
var number1 = 10
var number2 = 20
console.log(number1, number2)
swap(number1, number2)
console.log(number1, number2)
number1 . number2 . a . b 是各自獨立的變數
```

```
function addValue(obj) {
這裡的操作如下：
  var obj = a {}
  這是 Obj 的特性，如同前篇介紹的，物件是存取記憶體位置
  在這邊的操作等於是命名另外一個物件 obj = a，
  所以現在有 obj 跟 a 都指向同個記憶體位置

  obj.number++ // 在這邊等於把該記憶體位置儲存的內容+1 
 return 1
}
var a = {
 number: 10
}
addValue(a)
console.log(a)
//{ number: 11 }
```

例外情況是數字.字串.布林，這三者較簡單，所以在操作的時候會變成直接複製。而較複雜的部份因為使用指向的方式，所以只要有一個地方修改，就等修改其他指向該記憶體位置的操作。

```
function addValue(obj) {
 obj = {
 number: 100
 }
 return 1
}
var a = {
 number: 10
}
addValue(a)
console.log(a)
//{ number: 10 } 
```

在這邊的操作是把 obj 指向了另外一個新的記憶體，等於 obj 指向了另外一個記憶體位置

這種情況以專有名詞稱作：

pass by value
pass by reference. （JavaScript 中沒有）
pass by sharing

> 可以參考：[深入探討 JavaScript 中的參數傳遞：call by value 還是 reference？](https://blog.techbridge.cc/2018/06/23/javascript-call-by-value-or-reference/)

---

## return 的作用與使用時機

首先可以把 function 分成兩類：

### 第一種：不需要知道結果

也就是只需要呼叫 function，但不需知道執行結果的情況。

```
// 範例：
function sayhello() {
    console.log('hello')
    // 若沒有回傳，會預設 return undefined
}

sayhello()
// 印出 hello
```

```
// 也可以傳參數進去：
function sayhello(name) {
    console.log('hello', name)
}

sayhello('nick')
// 印出 hello nick
```

也可以回傳些什麼，如以下範例，但 return 並不會影響結果。

```
function sayhello(name) {
    console.log('hello', name)
    return “i am a”         // return 的值會是 a
}

var a = sayhello(‘nick’)    // 印出 hello nick
console.log(a)              // 印出 i am a
```

### 第二種：需要回傳值

例如需要函式進行運算，然後回傳結果。

```
function double(x) {
    return x * 2
}

var result = double(3)
console.log(result)
// 印出 6
```

> 注意：在 function 一旦執行 return 就會跳出，return 以下的任何程式碼都不會再執行。

```
舉例
function double(x) {
    return 123            // 執行 return 就會跳出
    console.log(abc)
    return x * 2
}

var result = double(3)
console.log(result)　　　　// 只會印出回傳值 123
```

## 常用內建函式

### Number 類型的內建函式

> 可參考：
> [Number - JavaScript - MDN - Mozilla](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Number)
> [Math - JavaScript - MDN - Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)

- `Number()`：將字串轉數字

```
var a = 10
var b = '20'

console.log(a + Number(b))    // 印出 30
```

- `parseInt()`：將字串轉整數。預設為十進位，例如：`parseInt(a, 10)`

```
var a = 10
var b = '20.35'

console.log(parseInt(b))    // 印出 20
```

- `parseFloat()`：將字串轉浮點數。也就是有小數點

```
var a = 10
var b = '20.35'

console.log(parseFloat(b))    // 印出 20.35
```

- `toFixed()`：取到小數點後第幾位。括號內不輸入就會取整數。可與 `parseFloat()` 搭配使用

```
var a = 10
var b = '20.357'

console.log(parseFloat(b).toFixed(2))    // 印出 20.36，前一位會四捨五入
```

- `.toString()`：數字轉字串

或是將數字加空字串（`''`），因為「`數字 + 字串 = 字串`」。

```
var a = 2

// 第一種方法
a.toString() 

// 第二種方法：數字 + 空字串 = 字串
(a+'')
```

- `Number.MAX_VALUE`, `Number.MIN_VALUE`：得知在 JavaScript 可儲存的最大、最小值，若超出這個值，計算就會不精準

```
console.log(Number.MAX_VALUE)
// 印出 1.7976931348623157e+308
console.log(Number.MIN_VALUE)
// 印出 5e-324
```

- `Math.PI`：圓周率。常數通常用大寫表示
- `Math.ceil()`：無條件進位，取大於這個數的最小整數

```
console.log(Math.ceil(3.14))
// 印出 4
```

- `Math.floor()`：無條件捨去，取小於這個數的最大整數

```
console.log(Math.floor(10.9))
// 印出 10
```

- `Math.round()`：四捨五入

```
console.log(Math.round(10.5))
// 印出 11
```

- `Math.sqrt()`：開根號

```
console.log(Math.sqrt(9))
// 印出 3
```

- `Math.pow()`：次方

```
console.log(Math.pow(2, 10))
// 印出 1024，也就是 2 的十次方
```

- `Math.random()`：產生從 0~1 隨機數（不包含 1）

```
console.log(Math.random())
// 產生 0~1 隨機數
console.log(Math.floor(Math.random()*10 + 1))
// 乘以十可產生 0~10 的隨機數（不包含 10）
// 無條件捨去，可產生 1~10 的整數
```

### String 類型的內建函式

> 可參考：[String - JavaScript - MDN - Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)

- `toUpperCase`, `toLowerCase()`：將字串轉換大、小寫

```
var a = 'abc'.toUpperCase()
console.log(a)　　　　　　　　　// 印出 ABC

var B = 'ABC!!!'.toLowerCase()
console.log(B)　　　　　　　　　// 印出 abc!!!
```

或是參考 [ASCII code](https://zh.wikipedia.org/wiki/ASCII) 進行轉換：

- `.charCodeAt()`：取得字串特定位置的字元 ASCII 編碼

```
console.log("ABC".charCodeAt(0))
// 印出 65（A 的編碼）
console.log("cba".charCodeAt(3))
// 印出 97（a 的編碼）

// 由此可知大、小寫字母的索引相差 32
```

- `String.fromCharCode()`：將 ASCII 編碼的數字轉換成字元

```
var str = String.fromCharCode(65)
console.log(str)
// 印出 A

// 將上述兩種函式組合應用：

var str = String.fromCharCode("Abc".charCodeAt(0) + 32)
console.log(str)
// 印出 a
```

利用 ASCII code 進行字串比大小，可判斷該字元為大小寫、是否在條件範圍內：

```
var char ='J'
console.log(char >= 'A' && char <= 'Z')
// 印出 true，判斷為大寫

var char ='g'
console.log(char >= 'A' && char <= 'Z')
// 印出 false，判斷為小寫

```

- `indexOf()`：可回傳「指定字串」在字串中第一次出現的位置。若找不到則回傳 -1

```
var str = 'hey hello world'
var index = str.indexOf('hello')
console.log(index)                        // 印出 4，hello 第一次出現在 index = 4

var index = str.indexOf('hello!!')
console.log(index)                        // 印出 -1，代表字串不存在
```

- `replace()`：取代字串。只能換第一個指定字串。要全換的話可使用[正規表達式](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Regular_Expressions)來達成

```
// replace()換第一個：
var str = 'hey hey hello world'.replace('hey', '!!!')
console.log(str)          
// 印出 !!! hey hello world

// 正規表達式可全換：
var str = 'hey hey hello world'.replace(/hey/g, '!!!')
console.log(str)  
// 印出 !!! !!! hello world，/hey/g 的 g 代表 global
``` 

- `split()`：透過「指定分隔符」來分開字串，回傳值為陣列

```
var str = 'data1,data2,data3'
var arr = str.split(',')                // 取出分號
console.log(arr)
// 印出 ['data1', 'data2', 'data3']

let input = ['1 * 3']
let temp = input[0].split(' ')          // 取出空格
console.log(temp)
// 印出 [1, *, 3]
```

- `trim()`：移除目前字串開頭和結尾的所有空格

```
var str = '   data1,data2,data3   '
console.log(str.trim())
// 印出 data1,data2,data3
```

- `string.length`：可回傳字串長度。此指令不是函式

```
var str = 'hello world'
console.log(str.length)
// 印出 11
```

可應用在迴圈：

```
var str = 'hello!'

for (var i=0; i<str.length; i++){
  console.log(str[i])            // 可在每行印出第 i 個字元
}
```

![](https://i.imgur.com/16SoONi.png)

### Array 類型的內建函式

> 可參考：[Array - JavaScript - MDN - Mozilla](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array)

- `join.()`：將陣列中所有元素連接成一個字串，預設分隔符是逗號（在 Windows 系統）

```
var arr = [1, 2, 3]
console.log(arr.join())        // 印出 1,2,3
console.log(arr.join('!'))       // 印出 1!2!3
console.log(arr.join(''))       // 印出 123
```

- `map()`：把陣列中的每個元素帶入指定函式，然後建立一個新的陣列

```
var arr = [1, 2, 3]

function negtive(x) {
  return x*-1
}

console.log(arr.map(negtive))    // 印出 [-1, -2 ,-3]

// 或是用匿名函式的寫法，直接放入函式定義：
var arr = [1, 2, 3]

console.log(
  arr
  .map(function (x) {
     return x * 2
  })
)                                // 印出 [2, 4, 6]
```

- `filter()`：概念和 `map()` 類似，是根據指定的測試函數，從一個陣列中過濾出符合條件的元素，並建立新的陣列

```
var arr = [1, 2, -3, 5, -7]

console.log(
  arr
  .filter(function (x) {
    return x > 0              // 只留下 x 大於 0
  })
)
// 印出 [1, 2, 5]

var arr = [1, 3, 5, 7]
arr.filter(x => x > 4)        // 只留下 x 大於 4 
// 印出 [5, 7]

```

- `slice()`：可截取出陣列某部份元素，會建立一個新的陣列

```
var arr = [0, 1, 2, 3, 4, 5]
console.log(arr.slice(2, 4))        // 印出 [2, 3]，不包含結尾元素
```

- `splice()`：可用來刪除與新增元素，會改變原本的陣列

```
var months = ['Jan', 'March', 'April', 'June']
months.splice(1, 0, 'Feb')
// 在 index 1 新增 'Feb'
console.log(months)
// 印出 ['Jan', 'Feb', 'March', 'April', 'June']

months.splice(4, 1, 'May')
// 把 index 4 的 'June' 換成 'May'
console.log(months)
// 印出 ['Jan', 'Feb', 'March', 'April']
```

- `sort()`：依照字母順序排列陣列中的所有元素，會改變原本的陣列

```
var arr = [1 ,30 ,4 ,21]

// 要由小排到大
arr.sort(function(a,b) {        // 想像順序是 a b
  if (a===b) return 0
  if (b>a) return -1            // 回傳 -1，代表不互換
  return 1                      // 回傳 1（任何正數），代表 a b 互換位置
})

console.log(arr)                // 印出 [1, 4, 21, 30]

// 可用三元運算子簡化：
arr.sort(function(a, b) {  
    if (a===b) return 0  
    return a > b ? 1:-1
})

// 也可再簡化：
arr.sort(function(a, b) {  
    return a - b                 
})
```

---

## Immutable（不可變）

除了物件以外，其他基本型別（primitive types）具有不可變的特性。

```
var a = "hello"
a = "yo"

a: "hello" 0x01    // 不會改變原本 hello 的值
a: "yo" 0x02       // 而是建立新的記憶體空間儲存 yo
```

以內建函式 `toUpperCase()` 為例，如以下寫法：

```
var a = 'hello'
a = a.toUpperCase()        // a 指向了新的記憶體位置
console.log(a)
// 印出 HELLO
```

由於字串具有不可變的特性，不論呼叫什麼函式均無法改變 a 的值，如以下範例：

```
var a = 'hello'
a.toUpperCase()            // 若直接對 a 字串呼叫函式，不會有任何改變
console.log(a)
// 仍印出 hello，只能如上個範例，回傳一個新的值改變字串
```

而物件型別，例如 Array，呼叫某些函式的時候，有可能會更動到原本記憶體位置儲存的東西：

```
var arr = [2, 4, 6]
arr.push(8)                 // 更動到原本的陣列
console.log(arr)
// 印出 [2, 4, 6, 8]

// 若改成下列錯誤寫法：
var arr = [2, 4, 6]
arr = arr.push(8)        
// 會做兩件事情：新增元素到陣列、回傳陣列長度（arr.length）
console.log(arr)
// 印出 4，也就是新的陣列長度
```

---

參考資料：
1. [JavaScript 初心者筆記: 判斷式 - if...else / switch 篇](https://ithelp.ithome.com.tw/articles/10215301)
2. [Java中for、while、do while三種迴圈語句的區別介紹](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/316385/)
3. [迴圈· 從ES6開始的JavaScript學習生活](https://eyesofkids.gitbooks.io/javascript-start-from-es6/content/part3/loop.html)
4. [JavaScript 基礎：函式的基本介紹- Hugh's Program learning](https://medium.com/@hugh_Program_learning_diary_Js/javascript-%E5%9F%BA%E7%A4%8E-%E5%87%BD%E5%BC%8F%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BB%8B%E7%B4%B9-e647b4e45af1)