[![hackmd-github-sync-badge](https://hackmd.io/EBi1V78ASXGRAhbXk4arfg/badge)](https://hackmd.io/EBi1V78ASXGRAhbXk4arfg)
###### tags: `JavaScript` `Front-End`
# 【學習筆記】JavaScript 的陣列遍歷（ㄧ）：for/for...of/for...in/forEach

[TOC]

這篇筆記整理幾種 JavaScript 遍歷陣列的方法，探討其使用時機與彼此的區別：

+ `for (let i = 0; i < arr.length; i++)`
+ `arr.forEach((value, index) => { // todo... })`
+ `for (let i in arr)`
+ `for (let v of arr)`

## for：遍歷「陣列」的值

使用情境：

+ 用於執行迴圈次數「明確」的狀況
+ 例如：印出陣列 `[0, 1, 2]` 中的每一個數字

語法如下：

+ 初始值：i = 0
+ 邊界條件：i 小於 3
+ 結束時更新：i + 1

```javascript=
for(let i = 0; i < 3; i++){
    console.log(i);
}
// 0
// 1
// 2
```

接著以下方實作九九乘法表為例，使用了兩個 For Loop：

```javascript=
for (let i = 2; i < 10; i++) {
    for (let j = 1; j < 10; j++) {
        console.log(i + 'x' + j + '=' + i * j)
    }
}
```

當 `i = 2` 與 `i = 3` 時的輸出結果如下：

![](https://i.imgur.com/dPeST08.png)


## [arr.forEach](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)：遍歷「陣列」的值

使用情境：

+ 用法與 for loop 類似，差別在於 forEach 不需設定索引的邊界條件
+ 缺點是無法使用 break 中斷迴圈，或 return 返回值等等

語法：

```javascript=
array.forEach(callback(currentValue, index, array){
    // TODO
})
```

使用範例：

```javascript=
let items = ['apple', 'banana', 'lemon']

items.forEach((item, i) => {
  console.log(i, item)
})
// 0 "apple"
// 1 "banana"
// 2 "lemon"
```


## [for...in](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/for...in)：遍歷「物件」的 key

語法如下：

```javascript=
for (variable in object) {
  // 遍歷的是 key
}
```

使用時需注意：

+ 遍歷的 key，型態不是 number 而是 string
+ 會遍歷到自定義屬性名稱
+ 通常用來遍歷一般 object 屬性 

以遍歷物件為例：

```javascript=
const user = {
    name: 'Jane Doe',
    email: 'jane.doe@example.com',
    age: 30,
};

for (let key in user) {
    console.log(`${key}: ${user[key]}`);
}

// name: Jane Doe
// email: jane.doe@example.com
// age: 30
```

在上述程式碼中，我們建立一個物件名稱為 user。透過 `for..in` 語法，指定 key 為自訂變數，用來儲存物件的屬性，即可依序讀取物件中的所有屬性。

接著以遍歷陣列為例：

```javascript=
const items = ['apple', 'banana', 'cucumber'];
items.duck = 'milk';  // 自定義屬性

for (const index in items) {
  console.log(index);
}
// "0"
// "1"
// "2"
// "duck"
```

### [hasOwnProperty()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)：回傳物件是否有該屬性的布林值

使用 `for...in` 時，需注意會遍歷包含繼承屬性的陣列，可能會產生預期外的結果。這時可使用 `hasOwnProperty()` 檢查是否為非繼承屬性。

將上方程式碼改寫如下，就不會印出繼承屬性：

```javascript=
Array.prototype.duck = 'milk';  // 原型鍊繼承屬性
const items = ['apple', 'banana', 'cucumber'];

for (const index in items) {
  if (items.hasOwnProperty(index)) {
      console.log(index);
  }
}
// "0"
// "1"
// "2"
```

## [for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)：遍歷「陣列」的 value

語法如下：

```javascript=
for (variable in object) {
  // 遍歷的是 value
}
```

以遍歷陣列為例：

```javascript=
const items = ['apple', 'banana', 'cucumber'];

for (const item of items) {
  console.log(item);
}
// apple
// banana
// cucumber
```

## for...in vs for...of

+ for...in 
  + 是 ES5 標準
  + 遍歷的是鍵（key）
  + 通常用來遍歷 object
  + 會遍歷繼承的屬性，可使用 hasOwnProperty() 檢查是否為非繼承的屬性
+ for...of
  + 是 ES6 標準，可解決 for...in 產生的問題
  + 遍歷的是值（value）
  + 不能遍歷 object
  + 可搭配 break、continue、return 語法使用

以下為使用範例：

```javascript=
let arr = [3, 5, 7];
arr.world = "hello";  // 自定義屬性

for (let index in arr) {
   console.log(index);  // "0", "1", "2", "world"
}

for (let value of arr) {
   console.log(value);  // 3, 5, 7
}
```


## 參考資料

+ [[筆記] 談談 JavaScript 中 for ... in 這個 function](https://pjchender.blogspot.com/2016/06/javascript-for-in-function.html)
+ [JavaScript 的 4 種陣列遍歷方法： for VS forEach() VS for/in VS for/of](https://www.gushiciku.cn/pl/2M0c/zh-tw)
+ [For vs forEach() vs for/in vs for/of in JavaScript](http://thecodebarbarian.com/for-vs-for-each-vs-for-in-vs-for-of-in-javascript.html)
+ [JavaScript中for of和for in的差別](https://blog.typeart.cc/JavaScript%E4%B8%ADfor%20of%E5%92%8Cfor%20in%E7%9A%84%E5%B7%AE%E5%88%A5/)
+ [JavaScript ES6 for...of 迴圈](https://www.fooish.com/javascript/ES6/for-of.html)
