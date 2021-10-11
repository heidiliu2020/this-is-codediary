###### tags: `TypeScipt`
# 【學習筆記】TypeScript 基礎入門：從型別談起

[TOC]

本篇為以下資源之學習筆記：
- [Github - microsoft/TypeScript](https://github.com/microsoft/TypeScript)
- [TypeScript 新手指南](https://willh.gitbook.io/typescript-tutorial/)
- [Angular Tutorial for Beginners: Learn Angular & TypeScript - Programming with Mosh
](https://www.youtube.com/watch?v=k5E2AVpwsko)

## 什麼是 TypeScript？

![](https://i.imgur.com/uowtOyV.png)

根據 [TypeScript 官網](https://www.typescriptlang.org/) 說明：

> TypeScript extends JavaScript by adding types.

> By understanding JavaScript, TypeScript saves you time catching errors and providing fixes before you run code.

> Any browser, any OS, anywhere JavaScript runs. Entirely Open Source.

將上述文字翻譯成中文：

> TypeScript 透過增加型別定義來擴展 JavaScript。

> 透過瞭解 JavaScript，TypeScript 可在執行程式碼之前找到錯誤並提供修復，節省開發除錯的時間。

> 可在任何瀏覽器、任何作業系統、任何能運行 JavaScript 的地方執行。並且是完全開源的。

簡單來說，TypeScript 是⋯⋯

- 一個基於 JavaScript 的超集合（SuperSet）
- 提供型別系統（Type System），能夠在開發時期宣告型別
- 支援 ECMAScript，可將 TS 檔編譯成 JS 檔給瀏覽器解讀

![](https://i.imgur.com/nTdyP5I.png)
（圖片來源：[Angular TypeScript Vs ES6 Vs ES5](https://www.sneppets.com/angular/typescript-vs-es6-vs-es5/)）

## 型別系統 Type System

由於 JavaScript 是弱型別語言，TypeScript 的出現就是為了解決這個問題。

而 TypeScript 就是原生 JavsScript 的延伸，包含 ES3、ES5 與 ES6+ 語法，以及本身的擴充內容，也就是說，TypeScript 具備以下特性：

- Strong typing 強型別
- Object-oriented-features 物件導向特性
- Compile-time errors 編譯錯誤

具體而言，TypeScript 和 JavsCript 的差別在哪呢？以下舉簡單的例子說明：

原生 JavaScript：

```javascript=
function sayHello {
    return 'Hello, ' + person;
}

let name = 'Heidi';
console.log(sayHello(name));
```

TypeScript：

```typescript=
function sayHello(person: string) {  // 傳入的參數
    return 'Hello, ' + person;
}

let name: string = 'Heidi';    // 宣告的變數
console.log(sayHello(name));
```

可以發現差別就在於「型別」，關於 TypeScript 如何處理型別，主要可分為這三個概念，也就是所謂的型別系統：

* Type annotation（型別註解）
  * 主動，大多使用在初始化階段，例如宣告變數或函式參數等
* Type Inference（型別推論）
  * 被動，自動推論資料型別的機制
* Type Assertion（型別斷言）
  * 主動，通常用於接收外部參數，需明確指定資料型別

### 型別註解 Type annotation

在上述範例中，TypeScript 透過 Type annotation（型別註解），在參數或變數之後加上冒號 `: type`，繼續以剛才的例子說明：

```typescript=
// 變數的型別註解
let name: string = 'Heidi';   

// 函式參數/回傳值的型別註解
function sayHello(person: string): string {
    return 'Hello, ' + person;
}
```

一旦宣告型別，就不能使用其他資料型別進行賦值，否則程式就會報錯（但 TypeScript Compiler 還是會編譯成 JS 檔）。

像這樣藉由 Type Annotation（型別註解）執行靜態型別檢查，可有效預防運行錯誤，並統一規格、提高程式碼的可讀性，以方便多人協作。

### 複習：JavaScript 型別

JavaScript 的型別分為兩種：原始資料型別（Primitive data types）和物件型別（Object types）。

- 原始資料型別
  - boolean 布林值
  - number 數值
  - string 字串
  - null 空值
  - undefined 未定義
  - Symbol（於 ES6 新定義）
- 物件型別
  - Object 物件
  - Array 陣列
  - Function 函式

在 TypeScript 中，除了上述這些型別，還有像是空值（Void）、任意型別（any）、Never 等特殊型別。

### 空值 Void：沒有回傳值

在 JavaScript 沒有空值（Void）的概念，而在 TypeScript 中，通常用 void 表示沒有任何 return 值的 function：

```typescript=
function alertName(): void {
    alert('My name is Heidi!');
}

// 若宣告一個 void 型別的變數，只能賦值為 null 或 undefined
let unusable: void = undefined;
```

### 補充：void 與 never 的差異

- Void 型別
  - 沒有回傳值，函式會繼續執行到結束

```typescript=
let noReturn = function sayHello(){
   console.log('hello');      // 不會有回傳值
}
```

![](https://i.imgur.com/POJa4wH.png)

- Never 型別
  - 應該要回傳，但因為函式中斷執行或進入無窮迴圈，永遠不會有回傳值的函式
  - 常用於處理函式的錯誤

```typescript=
let neverEnd = function forever(){
   while(true){    // 無窮迴圈
     // code
   }
}
```

![](https://i.imgur.com/RNQgOoe.png)

參考資料：[【Day 15】TypeScript 資料型別 - 特殊型別(上)- Never](https://ithelp.ithome.com.tw/articles/10222916)

### 任意型別 any：不檢查型別

其實就類似在 JavaScript 使用 var 宣告變數。一旦將變數宣告成 any 型別，或在定義時沒有賦值，不管之後有沒有賦值，都會被推斷成 any 型別，完全不會進行型別檢查：

```javascript=
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

![](https://i.imgur.com/u8oOgg5.png)

> 注意：不要濫用 any 任意型別，否則將失去型別檢查保障！

### 陣列型別 Array

表示陣列的方式，大致可分為以下幾種：
- 型別 + 方括號：`type[]`
- 陣列泛型（Array Generic）：`Array<elemType>`
- 用介面表示陣列
- 類別陣列（Array-like Object）

比較簡單，也較常使用的方法是以「型別 + 方括號」來表示陣列：

```typescript=
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

一旦宣告型別，陣列的項中就不允許出現其他型別，否則會報錯：

```typescript=
let fibonacci: number[] = [1, '1', 2, 3, 5];

// Type 'string' is not assignable to type 'number'.
```

常使用 any 來表示陣列中允許出現任意型別：

```typescript=
let list: any[] = ['heidiliu', 99, { website: 'https://github.com/heidiliu2020' }];
```

我們也可以使用陣列泛型（Array Generic） `Array<elemType>` 來表示陣列：

```typescript=
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

### 列舉型別 enum

以下方程式碼為例：

```typescript=
enum Color {Red, Green, Blue};    // 0, 1, 2
let c: Color = Color.Green;       // 1
```

經過 TSC（TypeScript Compiler）後，會得到下方結果：

```javascript=
"use strict";
var Color;
(function (Color) {
    Color[Color["Red"] = 0] = "Red";
    Color[Color["Green"] = 1] = "Green";
    Color[Color["Blue"] = 2] = "Blue";
})(Color || (Color = {}));
;
let c = Color.Green;
```

接著以番茄鐘為例，假設 Timer 有三種狀態，分別是停止、暫停、計時，即可列舉下列複合型別：

- timer-status.enum.ts：進行宣告並輸出

```typescript=
export enum TimerStatus {
  STOP = 'STOP',
  PAUSE = 'PAUSE',
  COUNTING = 'COUNTING'
}
```

- pomodoro.component.ts：引入使用，用法和物件類似

```typescript=
import { TimerStatus } from 'src/app/timer-status.enum';

// ...

  ngOnInit(): void {
    this.displayTime();
    console.log(TimerStatus);
    console.log(TimerStatus.STOP);
  }

// {STOP: "STOP", PAUSE: "PAUSE", COUNTING: "COUNTING"}
// STOP
```

### 型別推論 Type Inference

在 TS 檔中檢視上述範例程式碼，會發現不管有無明確註記型別，TypeScript 編譯器都會依照「型別推論」的規則，自動推斷出一個資料型別。

又以下方程式碼為例，雖然沒有指定值的型別，卻會在編譯時報錯：

```javascript=
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

這是因為在宣告變數並賦值時，TypeScript 編譯器就已經自動從程式碼推斷出 myFavoriteNumber 是字串型別，若嘗試以非字串的資料賦值時就會報錯！

對 TypeScript 編譯器來說，上方程式碼其實就等同於：

```javascript=
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

由於型別推論是 TypeScript 被動的防護機制，不管在什麼情況下都會自動推論型別，即使想故意註記一個錯誤，透過 TypeScript 的運算機制，仍會跳出錯誤訊息：

```javascript=
let randomNumber: string = Math.random()

/* TS2322: Type 'number' is not assignable to type 'string'. */
```

### 型別斷言 Type Assertion

但實際在開發時，專案的資料型別也會越發複雜，不能只仰賴 TypeScript 單純的推論，這時就需要適時搭配型別斷言。

開發者能藉由手動指定一個值的型別，覆蓋 TypeScript 編譯器的推論，避免出現警告錯誤，這個機制就是「型別斷言」。

型別斷言有兩種寫法：

- 第一種：`<型別>值 `（angle-bracket <>）

```typescript=
let code: any = 'Hello'; 
let helloCode = <string> code; 
```

- 第二種：`值 as 型別`（as keyword）

```typescript=
let code: any = 'Hello'; 
let helloCode = code as string; 
```

兩者語法效果相同，但如果是在 React 專案中，使用 JSX 語法時只能用第二種。

- 使用範例：將聯合型別的變數指定為更加具體的型別

在聯合型別的情境下，表示值可以是多種型別的其中一種。以下方程式碼為例，getLength 的參數可能是字串或數字，但因為 length 不為字串和數字的共同屬性，因此會報錯：

```typescript=
function getLength(something: string | number): number {
    if (something.length) {
        return something.length;
    } else {
        return something.toString().length;
    }
}

// error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

這時可使用型別斷言，將 something 指定為字串型別（如：`<string>something`），即可避免報錯：

```typescript=
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}

console.log(getLength(12345))    // 5
console.log(getLength("abc"))    // 3
```

> 參考資料：[【Day 04】 TypeScript 判斷資料型別的機制 - 型別推論 x 斷言 x 註解](https://ithelp.ithome.com.tw/articles/10217384)


<br>

## TypeScript 和 ES6+ 的差別？

接下來，在深入探討 TypeScript 與 ES6+ 差別之前，先來稍微回顧一些常用的 ES6+（或稱 ES2015）語法吧！

### 複習：那些常用的 ES6+ 語法

* let 與 const 宣告變數，以及變數作用域的不同
  * var：function scope 函式作用域
  * let 與 const：block scope 區塊作用域
* 解構賦值（Destructuring assignment）
  * 能夠快速建立變數並取值

```javascript=
const pcBrands = ['Apple', 'Lenovo', 'Acer'];
// 自動建立名為 first, second, third 的變數
// 再依照陣列內元素的順序把值取出來
const [first, second, third] = pcBrands;

console.log(first);      // Apple
console.log(second);     // Lenovo
console.log(third);      // Acer
```

* 箭頭函式（Arrow Functions）

```javascript=
const foo = () => {
    console.log("ES6");
}

function foo2() {
    console.log("ES5");
}

foo();    // "ES6"
foo2();   // "ES5"
```

* 模板字串（Template Literals）
  * ES5：使用單引號（''）或雙引號（""）
  * ES6：使用反引號（``）

```javascript=
// 可用於字串拼接
console.log("Hello, " + name)
console.log(`Hellooo,  ${name}`)
```

* 展開語法和其餘語法（Spread Syntax/Rest Syntax）
  * 根據不同用途使用 `...` 運算子

#### 展開語法：解壓縮，常用於複製一個物件或陣列，並為該物件增加屬性、或陣列添加元素

```javascript=
const PCOnSale = ['Apple', 'Lenovo', 'Acer'];
const allPCs = [...PCOnSale, 'HP', 'ASUS', 'TOSHIBA'];

console.log(allPCs);
// ["Apple", "Lenovo", "Acer", "HP", "ASUS", "TOSHIBA"] 
```

#### 其餘語法：壓縮，把沒有被取出來的物件屬性或陣列元素都放到一個壓縮包裡

```javascript=
const PCBrands = ["Apple", "Lenovo", "Acer", "HP", "ASUS", "TOSHIBA"] ;
const [first, second, third, ...other] = PCBrands;

console.log(other);
// ["HP", "ASUS", "TOSHIBA"] 
```

- Default Parameters 設定參數預設值
- Import & Export 引入與輸出
  - 引入與輸出 module，類似 require 與 module.exports 的用法

> 可參考：[[Day 02] React 中一定會用到的 JavaScript 語法](https://ithelp.ithome.com.tw/articles/10217085)

### 所以，TypeScript 特別在哪？

在複習了 ES6+ 語法之後，究竟 TypeScript 除了型別系統，還提供哪些重要的酷東西呢？為什麼不寫 ES6+ 就好？

主要可分為三個方面：

* Class 類型
* Interface 介面
* 未來的 ES2016+ 特性，例如：Annotations 註解, Decorators 裝飾器, async/await 異步/等待

### 介面 Interface：定義抽象物件的型別

Interface 被稱作介面或是接口，在物件導向程式語言中，用來定義抽象物件的型別，又被稱作是 TypeScript 的一個型別檢查工具。

> 因為介面只做描述，不做動作。

主要用於定義 Class（類別）行為，介面只會描述有哪些 Method（方法）和 Property（屬性），不包含怎麼執行，也就是說，具體行為必須由 Class 實現（implement）。

> 賦值的時候，Class 需和介面定義的行為保持一致。

舉例來說：

```typescript=
// 定義一個介面 Person
interface Person {
    name: string;
    age: number;
    address?: string;    // 可選屬性
}

// 定義一個變數 heidi，型別是 Person
let heidi: Person = {
    name: 'Heidi',
    age: 99
};
```

當 Class 用 implements 指定要實作的 Interface，除了可選屬性（在屬性名稱後方加上問號 `?`），必須實作介面內所有的 Method 和 Property，否則會在編譯時報錯。


## 結語

這篇是在剛接觸 Angular 框架時，寫下的學習筆記。偶爾會被問說，覺得學新框架最大的困難處在哪？其實 Angular 和 React 要說不同，也不盡然完全相差甚遠，或許實作方法不一樣，卻還是能用共通的邏輯去思考問題。

反而時常會卡關的地方，是在 TypeScript 型別判定上，為什麼這裡型別檢查這麼嚴格？難道我一定要給個 any 才會過嗎？直到學會如何定義 Interface 之後，後續開發就會順利不少，尤其是在打電文時更是如此，能事先統一 Request 和 Response 的格式，就比較不容易出現意外的錯誤。
