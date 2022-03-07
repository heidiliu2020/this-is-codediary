###### tags: `JavaScript` `Regex`
# 【學習筆記】JavaScript - Regex 正則表達式 

[TOC]

## Why we need Regular Expression?

正則表達式（Regular Expression），常簡寫為 RegEx、RegExp 或 RE，代表描述一種字串匹配的模式（pattern）。在程式語言中，通常用來搜尋、比對、替換符合某個模式的文字。

在 JavaScript 中，Regex 常見使用情境如下：

1. 尋找符合條件的字串
2. 取代符合條件的字串
3. 驗證使用者輸入欄位

舉個簡單的例子：「如何判斷某段資料內，是否包含字元 `H`？」

可能就有以下三種解法：

```javascript=
let str = 'Hello World';

// 法一：跑迴圈
for (let i = 0; i < str.length - 1; i++) {
  if (str[i] === 'H') {
    return true; 
  }
}

// 法二：使用 indexOf
if (str.indexOf('H') !== 1) {
  return true;
}

// 法三：正則表達式
if (new RegExp('H').test(str)) {
  return true;
}
```

## How to use?

撰寫正規表達式，可透過以下兩種方式：

+ 正規表達式字面值（literal）：使用兩個斜線 `/ /`
  + 在 script 載入時被編譯，效能較佳

```javascript=
let reStr = /abc/
```

+ 建構式：直接 new 一個 RegExp 物件 `new RegExp()`
  + 適合需動態產生 pattern 時使用

```javascript=
let reStr = new RegExp('abc')       // 查詢第一次匹配項目
```

## 特殊字元

在正規表達式中，某些特殊字元或符號屬於保留字：

![](https://i.imgur.com/CIlAXQ8.png)

以下為使用範例：

```javascript=
const str = 'The History of Hello World History'

str.match(/History/)    // ['History', index: 4, ...
// ^：匹配輸入的開頭
str.match(/^History/)   // null
// $：匹配輸入的結尾
str.match(/History$/)   // ['History', index: 27...

// 使用反斜線 '\' 來跳脫特殊字元
const reg = /\$6/
reg.test('$666')         // ture
```

### 匹配次數：`*`、`+`、`?`、`{}`

預設情況下，一個字元只會匹配一次，搭配 `*`、`+`、`?`、`{}` 字元可指定匹配次數：

+ `*`：任意次數，等同於 `{0,}`
+ `+`：至少一次（後面要跟著），等同於 `{1,}`
+ `?`：零或一次（有或沒有），等同於 `{0,1}`
+ `{m}`：m 次
+ `{m, n}`：從 m 到 n 次

```javascript=
const str = 'banana'

const regex = /ban/       // 符合 ban
const regex = /ba*n/      // ba, ban, baaan 均符合
const regex = /k?a/       // k 可有可無
const regex = /a+n/       // 必須包含 an
const regex = /an{2}/     // an 要包含 2 次
const regex = /an{2,4}/   // an 介於 2 到 4 次
const regex = /an{2,}/    // 2 次以上 an 均符合
```

### 標記 Flag：`i`、`g`、`m`

而 `i`、`g`、`m` 這類標記（flag），不會寫在正則表達式裡，格式與範例如下：

```javascript=
/pattern/flags

 let reStr = /abc/i                   // ignore：不區分大小寫
 let reStr = new RegExp('abc', 'g')   // global：全局匹配   
```

可參考下圖，在 [Regexr](https://regexr.com/) 網站的測試結果：

![](https://i.imgur.com/VKVMHff.png)

<br>

## 常用語法

在 JavaScript 中，常與 RegExp 物件搭配使用的函式如下：

+ 檢測是否包含指定字串：`test()`、`search()`
+ 尋找並回傳指定字串：`exec()`、`match()`
+ 取代指定字串：`replace()`

### [RegExp.prototype.test()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test)：檢測字串是否包含，回傳 true/false

+ 類似 String.prototype.search() 方法，差別在於回傳值不同

```javascript=
const paragraph = 'Hello World';
const regex1 = /^He/g;
const regex2 = /Woooo/g;

console.log(regex1.test(paragraph))    // true
console.log(regex2.test(paragraph))    // false
```

### [String.prototype.search()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/search)：檢測字串是否包含，有的話回傳 index，否則回傳 -1

```javascript=
const paragraph = 'Hello World';
const regex1 = /^He/g;
const regex2 = /Woooo/g;

console.log(paragraph.search(regex1))    // 0
console.log(paragraph.search(regex2))    // -1
```

### [RegExp.prototype.exec()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)：尋找並取出內容，有的話以陣列回傳，否則回傳 null

+ 類似 String.prototype.match() 方法，差別在於「全域性匹配」時結果不同

```javascript=
const paragraph = 'Hello, I am fine. Thank you.';
const regex = /[A-Z]/;
const globalReg = /[A-Z]/g;

console.log(regex.exec(paragraph));       // ["H"]
console.log(globalReg.exec(paragraph));   // ["H"]
```


### [String.prototype.match()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match)：尋找並取出內容，有的話以陣列回傳，否則回傳 null

```javascript=
const paragraph = 'Hello, I am fine. Thank you.';
const regex = /[A-Z]/;
const globalReg = /[A-Z]/g;

console.log(paragraph.match(regex));      // ["H"]
console.log(paragraph.match(globalReg));  // ["H", "I", "T"]
```

### [String.prototype.replace()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)：尋找並取代字串中匹配的部分

```javascript=
const str = 'banana'

console.log(str.replace(/na/, 'NA'));   // baNAna
console.log(str.replace(/n./, 'NA'));   // baNAna  '.' 代表任意字元
console.log(str.replace(/na/g, 'NA'));  // baNANA
```

## 常用匹配規則

詳細規則可參考這篇：[正則表示式 – 匹配規則](https://www.itread01.com/study/regexp-rule.html)

```javascript=
[a-z]           // 匹配所有的小寫字母 
[A-Z]           // 匹配所有的大寫字母 
[a-zA-Z]        // 匹配所有的字母 
[0-9]           // 匹配所有的數字 
[0-9\.\-]       // 匹配所有的數字，句號和減號 

^[a-z][0-9]$    // 匹配一個開頭為小寫字母，和一位數字組成的字串：e7
^[A-Z][0-9]{9}  // 身分證字號(大寫英文 + 9 位數字)：A123456789
[0-9]{10}       // 手機號碼(10 位數字)：0912345678
/\d{6}/         // 匹配 6 次任意數字：123456
```

## 參考資料

+ [MDN - 正規表達式- JavaScript](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Regular_Expressions)
+ [十五分鐘認識正規表達式，解決所有文字難題](https://5xruby.tw/posts/15min-regular-expression)
+ [[Javascript] 初探Regex 正規表達式](https://moojing.medium.com/javascript-%E5%88%9D%E6%8E%A2regex-%E6%AD%A3%E8%A6%8F%E8%A1%A8%E9%81%94%E5%BC%8F-1da2f4d94795)
+ [[JS] 正則表達式（Regular Expression, regex](https://pjchender.dev/javascript/js-regex/)
+ [測試網站：Regexr](https://regexr.com/)
