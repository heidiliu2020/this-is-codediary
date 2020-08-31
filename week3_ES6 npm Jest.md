###### tags: `JavaScript` `npm`
# [week 3] JavaScript 技能：ES6 + npm + Jest

> 本篇為 [JS102] 升級你的 JavaScript 技能：ES6 + npm + Jest (https://lidemy.com/p/js102-javascript-es6-npm) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 理解常用內建函式如何使用
 熟悉程式語法並知道如何解決基礎問題
 了解為什麼我們需要 unit test
 了解什麼是 unit test
 了解如何寫 unit test
 了解如何測試一個 function
```

---

## 何謂 Modules 模組（模塊）？

在開發過程中，若將各種功能放在一起，程式間可能會互相影響甚至產生 bug，日後也不易進行維護。

因此，我們可以將不同功能視為一個模組（Module），例如：金流、登入、權限、會員等等，再用主程式將所有模組串接起來，透過模組化統一進行管理。

![](https://i.imgur.com/di0LXxg.png)

## Ｍodeule 相關操作

### `require()`：引入模組

以引入 [Node.js 提供的 os](https://nodejs.org/api/os.html#os_os_platform) 這個模組為例：

```js
var os = require('os')      // 引入 'os' 這個模組，變數 os 可隨意命名
console.log(os.platform())
// 印出 win32，代表當前作業系統
```

![](https://i.imgur.com/ubLcpXY.png)

### `module.export`：輸出模組

- 語法：`module.exports = 任何資料型別（例如：數字、陣列、物件等）`

1. 以輸出 `double` 函式為例：

```js
// 建立一個要輸出模組的 myModule.js 檔案

function double(n) {
   return n * 2
}

module.exports = double;
```

```js
// 以 require 指令輸入模組到要使用的 js 檔案

var myModule = require('./myModule')    // 要加路徑，檔案類型 .js 通常會省略
// 不加路徑的話，其實也會自動從 node_modules 資料夾底下去找

console.log(myMoudle)        // 印出 [Function: double]
console.log(myMoudle(6))     // 印出 6
```

2. 也可利用 `exports` 輸出物件：

```js
// 在要輸出模組的 myModule.js 檔案

function double(n) {
   return n * 2
}

let obj = {
  double: double,
  triple: function triple(n) {
      return n * 3
  }
}

module.exports = obj
```

```js
// 以 require 指令輸入模組到要使用的 js 檔案

var myModule = require('./myModule')    

console.log(myModule)
// 印出 { double: [Function: double], triple: [Function: triple] }

console.log(myModule.double(2), myModule.triple(10))
// 印出 4, 30
```

3. 也可用另一個寫法：`exports.double = double`，把 `exports` 本身視為空物件，但這種方法比較少見：

```js
// 在要輸出模組的 myModule.js 檔案

function double(n) {
   return n * 2
}

exports.double = double
exports.triple = function(n) {
  return n * 3
}
```

```js
// 此種輸出方式，myModule 一定會是物件

var myModule = require('./myModule') 

console.log(myModule)
// 印出 { double: [Function: double], triple: [Function] }
console.log(myModule.double(3))
// 印出 6
```

參考資料：

1. [[第三週] Node.js 基礎 — module.exports 和 require](https://medium.com/@miahsuwork/%E7%AC%AC%E4%B8%89%E9%80%B1-node-js-%E5%9F%BA%E7%A4%8E-module-exports-%E5%92%8C-require-2f9f6915d9f0)

---

## NPM 線上套件庫

[NPM](https://www.npmjs.com/) 是 Node Package Manager 的簡稱。是用來管理 Node.js 套件的系統（Library），可以下載別人已經寫好的 Javascript 套件來使用。

> 也可使用由 Facebook 團隊開發的 [Yarn](https://yarnpkg.com/)，同樣能從 npm 安裝套件，優點是速度較快。
> 參考資料：[[Day-5] 用Yarn取代npm加速開發](https://ithelp.ithome.com.tw/articles/10191745)

### 基本指令

#### `npm -v`：查看 npm 版本。

通常在安裝 node.js 時就會一起安裝。

![npm -v](https://i.imgur.com/zZMyERG.png)

#### `npm init`：協助建立 Node.js 專案的描述檔。

也就是產生 package.json 這個檔案。

#### `npm install left-pad`：以 npm 安裝 left-pad 這個套件為例。

![npm install](https://i.imgur.com/4zvy68Z.png)

安裝同時會產生：

1. package-lock.json 檔案：記錄安裝套件的版本和依賴（dependencies）
2. node_modules 資料夾：裡面放安裝的套件

![](https://i.imgur.com/hOnLOZL.png)

package-lock.json 檔案內容如下，可從 `dependencies` 得知專案使用的套件：

![描述檔](https://i.imgur.com/YopHSs7.png)

### 版本控制會忽略 node_modules 資料夾

當安裝許多套件時，檔案會很大。若要將專案上傳到 GitHub 遠端，通常會忽略 node_modules 這個資料夾，也就是不需進行版本控制。

因為已經有 package.json 這個檔案，負責記錄該專案所安裝的套件。若從遠端下載專案時，只要再輸入 `npm install` 指令，就可安裝該專案所需套件。

![](https://i.imgur.com/nsRpDni.png)

---

## 設定 npm scripts

`package.json` 檔案中，我們可在 `scripts` 區塊加入各種指令。

### `"key": "要執行的內容＂`

以如何運行 `index.js` 這個專案為例：

`"start": "node index.js"`：代表以 start 為 key，輸入即可在 node 運行 index.js。

> 注意是使用雙引號。

![scripts](https://i.imgur.com/fvF1YuH.png)

### `npm run 'key'`

在終端機輸入 `npm run start` 即可透過 key 來運行該指令：

![npm run start](https://i.imgur.com/K3XhyO3.png)

---

## 如何測試程式？

測試程式的作用是「模擬外部如何使用目標程式，驗證目標程式的行為是否符合預期」。

### 利用 `console.log()` 測試

在前面幾個章節，我們通常會使用 console.log()，來測幾個範例確認是否正確。也需要考慮到邊界條件（edge case）來進行測試。

```js
// 以測試 `repeat` 函式為例：

function repeat(str, times) {
  let result = ''
  for (let i = 0; i < times; i += 1) {
    result += str
  }
  return result
}

console.log(repeat('a', 5));          // 印出 aaaaa
console.log(repeat('z!Z!Z!!', 2));    // 印出　z!Z!Z!!z!Z!Z!!
console.log(repeat('', 3));           // 印出空字串
console.log(repeat('abc', 0));        // 印出空字串
```

接著優化測試資料：直接判斷函式執行結果是否正確。

```js
// 優化測試資料：

console.log(repeat('a', 5) === 'aaaaa');
console.log(repeat('z!Z!Z!!', 2) === 'z!Z!Z!!z!Z!Z!!'); 
console.log(repeat('', 3) === ''); 
console.log(repeat('abc', 0) === '');
// 均印出 true
```

這是最簡單的測試方法，但這麼做的缺點是很難「規模化」。我們可以利用別人寫好的框架來便利測試。

### 利用現成的框架 [Jest](https://jestjs.io/) 測試

1. 用 npm 下載 Jest：輸入指令 `npm install -save-dev jest`

![](https://i.imgur.com/P2V2LzG.png)

2. 利用模組將「測試」與「要測試的 Function」分開。

- 在要測試的檔案 index.js 加上：`module.exports = '要輸出的值'`

```js
function repeat(str, times) {
  let result = ''
  for (let i = 0; i < times; i += 1) {
    result += str
  }
  return result
}

module.exports = repeat
```

- 建立 index.test.js 檔案：`touch index.test.js`，習慣用 `test.js` 取名
並在檔案中引用 index.js 輸出的值：`var repeat = require('./引入的檔案')`

```js
// 可輸入 node index.test.js 測試是否引入成功
var repeat = require('./index')

console.log(repeat('a', 5));
// 印出 aaaaa，引入成功
```

- 在 index.test.js 加入 Jest 語法：`test('描述文字', '要做的測試')`

```js
var repeat = require('./index')

test('a 重複 5 次應該要等於', function() {
  expect(repeat('a', 5)).toBe('aaaaa');
})
```

3. 更新 package.json 檔案的 `"scripts"`：加入 `"test": "jest"`

!["test": "jest"](https://i.imgur.com/85bIvDU.png)

4. 如此即可運行 `npm run test` 進行測試，看到 PASS 可知測試有成功：

![test](https://i.imgur.com/KGT6Vht.png)

之所以要用 npm 來跑 jest，而不是直接在終端機輸入 jest 指令，是因為 jest 只安裝在該專案底下，要使用時才會拿出來用。

若只想測特定檔案，可以修改 `"scripts"`：`"test": "jest index.test.js"`，後面加上檔名。

![](https://i.imgur.com/4byHuvz.png)

或是用 `npx jest index.jest.js`，同樣能夠執行測試：

![](https://i.imgur.com/VGCuYis.png)

也可以多跑幾個測式：

```js
var repeat = require('./index')

test('a 重複 5 次應該要等於aaaaa', function() {
  expect(repeat('a', 5)).toBe('aaaaa');
});

test('abc 重複 1 次應該要等於abc', function () {
  expect(repeat('abc', 1)).toBe('abc');
});

test(' "" 重複 10 次應該要等於 ""', function () {
  expect(repeat('', 10)).toBe('');
});
```

![測試結果](https://i.imgur.com/Ief4HX8.png)

也可以把測試項目放在 `describe()` 函式裡，這種寫法會更有結構一點：

```js
// 架構：

`describe('測試 XX', function(){
  test('名稱', function() {
    expect('回傳值').toBe('預期結果');
  })
})`
```

```js
var repeat = require('./index')

describe('測試 repeat', function() {
  test('a 重複 5 次應該要等於aaaaa', function () {
    expect(repeat('a', 5)).toBe('aaaaa');
  });

  test('abc 重複 1 次應該要等於abc', function () {
    expect(repeat('abc', 1)).toBe('abc');
  });

  test(' "" 重複 10 次應該要等於 ""', function () {
    expect(repeat('', 10)).toBe('');
  });
})
```

### Unit test 單元測試

單元測試指的是測試一個工作單元（a unit of work）的行為。上述範例測試單一函式，就是一種單元測試。可用來確認每個 Unit 是否正確，也能夠進行規模化測試。

---

### 補充：如何在 Windows 上執行 Jest 也能有紅綠標籤

方法：把 `package.json` 裡的 `"script"` 中的內容改為 `"test": "jest --colors"`，FAIL 和 PASS 標籤就會是紅綠標籤。

![color](https://i.imgur.com/LaX4cI3.png)

奇怪的是，在 git commit 時都沒有問題，在執行 Jest 時卻出現亂碼。不太確定是不是因為更改 locale 才解決的，總之介面很神奇的變成中文了，可喜可賀。

![UTF-8](https://i.imgur.com/4OCokvV.png)

參考資料：

1. [Colorful output in Bash terminal does not work (--colors option) ](https://github.com/facebook/jest/issues/3877)

---

### 先寫測試再寫程式：TDD

Test-driven Development（測試驅動開發），是一種開發流程，簡言之就是「先寫測試在開發」。相較於傳統的「先開發在寫測試」模式，TDD 有幾項優點：

1. 能確保測試程式的撰寫
2. 從使用方觀點切入，有助於在開發初期釐清程式介面如何設計
3. 便於日後 Debug

### 實作 TDD

可利用 Jest 模組，先建立 `test()` 架構，再來撰寫主要程式碼。步驟可參考：[TDD 開發五步驟，帶你實戰 Test-Driven Development 範例](https://tw.alphacamp.co/blog/tdd-test-driven-development-example)

#### 步驟一、選定一個目標功能，來新增測試案例

先寫好測試預期結果，並盡量列出邊界條件。在這個步驟還不會撰寫目標程式內容。這裡用 `reverse` 函式為例：

```js
// 先寫出預期結果：

var reverse = require('./index')

describe('測試 reverse', function() {
  test('123 reverse 要等於 321', function () {
    expect(reverse('123')).toBe('321');
  });

  test('!!! reverse 要等於 !!!', function () {
    expect(reverse('!!!')).toBe('!!!');
  });

  test(' "" reverse 要等於 ""', function () {
    expect(reverse('')).toBe('');
  });
})
```

#### 步驟二、執行測試，得到 Failed（紅燈）

因為還沒撰寫目標程式，結果就會是 Failed。此步驟目的是確保測試程式可執行，沒有語法錯誤。

![運行測試](https://i.imgur.com/A38CQ2C.png)

#### 步驟三、實作「最低限度」的產品程式

開始寫程式，以能夠通過測試為目標，不求將程式碼優化一步到位。完成到一個階段就可運行 jest 查看是否有錯誤：

```js
//開始寫程式

function reverse(str) {
  let result = ''
  for (let i = str.length - 1; i >= 0; i -= 1) {
    result += str[i]
  }
  return result
}

module.exports = reverse
```

#### 步驟四：再次執行測試，得到 Passed（綠燈）

在這個階段，即完成一個可運作且正確的程式版本，包含產品程式和測試程式。

![Passed](https://i.imgur.com/dJHNZ1j.png)

#### 步驟五：重構程式

最後是優化「產品程式」和「測試程式」的程式碼，因為測試程式也是專案需維護的一部份。如此即可提升程式的可讀性、可維護性、擴充性。

---

## 什麼是 ECMAScript

是一種標準和規範，Javascript 這門語言就是遵循 ECMAScript 規範實作。於 2015 年發布 ECMAScript 第六版，因此又稱 ES2015 或 ES6，在 ES6 之前的版本就稱作 ES5。

目前大部分的瀏覽器均支援 ES6，但仍有少數舊型不支援。因此我們可以透過 Babel 轉譯器，將 ES6 代碼轉換為 ES5 代碼，如此就不須擔心支援問題。

## ES6 新語法

### 宣告變數 let 與 const

- ES5：使用 var
- ES6：使用 let 與 const

兩者最大差異在於：
1. 重複宣告：const 用於宣告常數，不會被重新賦值
2. 作用域不同：
    - var：作用於整個函數範圍中（function scope）
    - let 與 const：均為區塊作用域（block scope），如此可避免污染到大括號外的變數

### Template Literals 模板字串符

Template 的意思是樣板。Template Literals 可用於字串拼接。

#### ES5

- 使用單引號（`''`）或雙引號（`""`）
- 缺點：必須用 `+` 或 `,` 來串接字串，且無法換行

#### ES6

- 使用反引號（``）
- 優點：可用於多行字串拼接，也可在反引號中放入 `${變數}`



### Destructuring 解構賦值

- 可以把陣列或物件的資料解開，並擷取成獨立的變數

### Spread Operator 展開運算子

- 使用 `...` 運算子，展開陣列或物件

### Rest Parameters 其餘參數

- 使用 ... 運算子，集合剩餘的元素變成陣列，就可以在不確定陣列長度的情況下，傳入參數。

### Default Parameters 設定參數預設值

- 可以幫參數加入預設值

### 箭頭函式

- Arrow Function，縮寫為 function
- 優點：簡化程式碼，幫助閱讀

### Import & Export 引入與輸出

- 引入與輸出 module，類似 `require` 與 `module.exports` 的用法

### Babel 簡介

- 是一種 JavaScript 轉譯器，可將 ES6 新語法轉換為 ES5 舊語法
- 安裝指令：`npm install babel-loader @babel/core @babel/preset-env --save-dev`

參考資料：
1. [從博物館寄物櫃理解變數儲存模型](https://medium.com/@hulitw/variable-and-frontdesk-a53a0440af3c)
2. [[第五週] JavaScript — ES6 與 Babel](https://medium.com/@miahsuwork/%E7%AC%AC%E5%9B%9B%E9%80%B1-javascript-es6-%E8%88%87-babel-5b5e25450767)