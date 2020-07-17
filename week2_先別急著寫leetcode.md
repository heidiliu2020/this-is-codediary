###### tags: `JavaScript` `leetcode`

# [week 2] 先別急著寫 leetcode - 虛擬碼、Debugger、解題技巧

> 本篇為 [[ALG101] 先別急著寫 leetcode](https://lidemy.com/p/alg101-leetcode) 這門課程的學習筆記。如有錯誤歡迎指正。
> [程式解題新手入門注意事項 - Huli](https://blog.huli.tw/2019/11/01/before-start-leetcode/)

---

## 寫程式≠寫程式碼
重點是在寫程式之前，先想好要怎麼做，整理自己的想法，並轉換成程式碼表達。

- 初學者寫程式
1.邊寫程式碼邊想怎麼解
2.試著套用自己以為學過的語法
- 會寫程式的人寫程式
1.先想解法，在腦中構思（太快看不出來）
2.把解法轉換成程式碼

### 初期如何學習寫程式？

- 步驟
1. 大概想一下解法，不寫程式碼
2. 把解法寫成 pseudo code（虛擬碼）
3. 把 pseudo code 翻譯成程式碼
- 利用條列式寫法
1. 將大問題分割成小問題
2. 一行只做一件事
3. 善用敘述、條件判斷
4. 善用跳轉（jump）來實現重複執行

### 範例：印出 1~100 之間的偶數

- 步驟一：如何印出 1~100？
1. 令 i 為 1　//　設置初始條件
2. 如果 i>100，結束　//　判斷是否該結束程式（終止條件）
3. 印出 i 
4. i = i+1
5. 跳回第 2 行　//　重複動作
- 步驟二：如何判斷某個數是偶數？
1. 令 i = 1
2. 如果 i>100，結束
3. 如果 i % 2 ===0，印出 i 　//　利用是否能整除2來判斷奇偶
4. i = i+1
5. 跳回第 2 行

## 如何撰寫 Pseudo Code（虛擬碼）？
虛擬碼是程式碼與想法之間的橋樑，把解法用抽象的方式表示。

將剛才步驟二得到的可執行步驟翻成英文，會發現和程式碼非常相似：

```
1. let i = 1
2. if i>100 then exit
3. if i % 2 no remainder, print  i 
4. i = i+1
5. jump to line 2
```

更像程式碼的虛擬碼：

```
1. for (i from 1 to 100) do　//　for迴圈，有數字i會從 1到 100，do代表迴圈內動作
2. 	if i / 2 no remainder, print i
3. end for　//　此迴圈結束
```

若以 JavaScript 撰寫成程式碼：

```
for (var i = 1; i< 100; i++) {
　if(i % 2 === 0){
　console.log(i)
　}
}
```

### 實戰練習：印出 1-100 的奇數

利用「縮排」來標明條件判斷的區塊，較容易閱讀分析。

```
1. for (i from 1 to 100) do
2.　if (i mod 2 === 1) then　//　mod（modulo）：取餘數，餘數為 1 代表為奇數
3.　　print i
4.　end if
5. end for
```

### 實戰練習：fiszz buzz

題目：
給一個數字 n
印出 1~n
但如果碰到 3 的倍數，改印 Fizz
碰到 5 的倍數，，改印 Buzz
若同時是 3 跟 5 的倍數，印 FizzBuzz

範例：n=7

```
1
2
Fizz
4
Buzz
Fizz
7
```

基本解法：

```
for (i from 1 to n) do　　//　先寫出 1~n的迴圈
if　(i % 15 === 0) print “FizzBuzz”
else if (i % 3 === 0) print “Fizz”
else if (i % 5 === 0) print “Buzz”
end for
```
但此解[並非最佳解法]()，若條件多 7 的倍數或更多，需要考慮的條件判斷也更複雜。

### 實戰練習：找最小值

題目：
假設今天有一副撲克牌
一次只能看一張牌
要怎麼找到最小的牌？

解法（虛擬碼）：

```
let 最小的牌 = 第一張牌
for (i from 1 to n) do　　　//　n為撲克牌總數
翻開第 i 張牌
if (第 i 張排比最小牌的還小) do
最小的牌 = 第 i 張牌
end if
end for
```

將上述解法翻譯成陣列：

```
let min = arr[0]　//　min是陣列中最小值，0代表array第一個元素
for (i from 0 to n-1) do　　//　n-1：因為array的 index（索引）所以從 0 開始
if (arr[i] < min) do　　//　如果第 i 個元素比最小值還小　
min = arr[i]　　//　i 就變成最小值
end if
end for
```

延伸閱讀：
[簡單的 FizzBuzz 藏有 深度(google 面試題)](https://medium.com/@Bear_/%E7%B0%A1%E5%96%AE%E7%9A%84-fizzbuzz-%E8%97%8F%E6%9C%89-%E6%B7%B1%E5%BA%A6-google-%E9%9D%A2%E8%A9%A6%E9%A1%8C-f5e66e3a97be)
[[課程學習筆記 ]先別急著寫 leetcode — 一步步打造程式腦](https://medium.com/@miahsuwork/%E8%AA%B2%E7%A8%8B%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-%E5%85%88%E5%88%A5%E6%80%A5%E8%91%97%E5%AF%AB-leetcode-%E4%B8%80%E6%AD%A5%E6%AD%A5%E6%89%93%E9%80%A0%E7%A8%8B%E5%BC%8F%E8%85%A6-7ed12aaa4a3d)

---

## 學會像電腦一樣思考

寫程式之前，先學會「看程式」，所謂的看程式碼 = 理解程式碼如何運作。

### 以陣列加總的虛擬碼為例

```
let sum = 0　　
for (i from 0 to n-1) do
    sum += arr[i]　　
end for
print sum
```
- 看懂程式碼：
1. 假設array 為 [1, 2, 3]，代表陣列長度 n = 3
2. 設 sum = 0
3. 讓 i 從 0 跑到 n-1（也就是 2）
4. i現在是 0
5. sum += arr[0]，sum 變成 1
6. 下一個迴圈
7. i 現在是 1
8. sum += arr[1]，sum 變成 1+2 = 3
9. 下一個迴圈
10. i 現在是2
11. sum += arr[2]，sum 變成 3+3 = 6
12. 下一個迴圈
13. i 現在是 3，超出 n-1，結束
14. 輸出 sum

### 人體編譯器

編譯（Compile）：將高階語言所編寫的原始程式，透過編譯器（Compiler）轉成機械碼。

### 以找最大值為例

- 翻譯虛擬碼：

```
let max = arr[0]
for (i from 0 to n-1) do
    if (arr[i] > max) do
        max = arr[i]
    end if
end for
print max
```

1. 假設 arr 為 [2, 7, 5]
2. 設 max 為 arr[0]，也就是 2
3. 讓 i 從 0 跑到 2
4. i 現在是 0 
5. 判斷 arr[0] 是否 >2
6. 不是
7. 下一個迴圈
8. i 現在是 1
9. 判斷 arr[1] 是否 > 2
10. 是，設 max = arr[2]，也就是 7
11. 下一個迴圈
12. i 現在是 2
13. 判斷 arr[2] 是否 > 7
14. 不是
15. 下一個迴圈
16. i 現在是 3，超出條件，結束
17. 輸出 max

接著翻譯真的程式碼（JavaScript）：

```js
let max = arr[0]
for (let i=0; i<arr.length; i++) {　　//　i=0 為初始條件；終止條件；每圈執行完 i+=1
　if (arr[i] > max) {
  　　max = arr[i]
　}
}
console.log(max)
```

1. 假設 arr 為 [2, 7, 5]
2. 設 max 為 arr[0]，也就是 2
3. 讓 i 從 0 跑到 2
4. i 現在是 0 
5. 判斷 arr[0] 是否 >2
6. 不是
7. 下一個迴圈
8. i 現在是 1
9. 判斷 arr[1] 是否 > 2
10. 是，設 max = arr[2]，也就是 7
11. 下一個迴圈
12. i 現在是 2
13. 判斷 arr[2] 是否 > 7
14. 不是
15. 下一個迴圈
16. i 現在是 3，超出條件，結束
17. 輸出 max

> 重點：當不知道程式碼錯在哪時，可以一行一行檢視程式碼，驗證程式碼是否符合實際想表達的。

---

## Debug 神器：Debugger（偵錯）

利用 Chrome Devtool（開發者工具）Debugger，直接設置中斷點（break point），一行一行執行程式碼以找出除錯範圍。

### 前置作業

1. 將程式碼貼到一個 html 檔案
2. 在 `<script>` 標籤（以執行 JavaScript）底下加上 `debugger`

```js
<script>
debugger
let arr = [2, 7, 5];
let max = arr[0];
for (let i=0; i<arr.length; i++) {　
    if (arr[i] > max) {
      max = arr[i];
    }
}
console.log(max);
</script>
```

3. 用 Chrome 開啟檔案，以開發者工具進行檢視，在 Sources 欄位點選 Step（F9），即可一步一步執行程式碼，進行除錯

![](https://i.imgur.com/lLFpAEk.png)

## Log 大法

另一個 debug 的方法，也就是「log 加好加滿」，透過 log 可以得知每個階段的值。

舉例：

```js
<script>
let arr = [2, 7, 5];
let max = arr[0];
console.log('max:', max)
for (let i=0; i<arr.length; i++) {　
    console.log('i=', i, 'arr[i]=', arr[i])
    if (arr[i] > max) {
    console.log('found new max!')
    console.log('arr[i]:', arr[i], 'old max:', max)
      max = arr[i];
    console.log('new max:', max)
    }
}
console.log(max);
</script>
```

檢視 console 欄位結果如下，可以檢視程式碼運作是否和所想的相同，是否有進入迴圈等等。

![](https://i.imgur.com/5jJvOFQ.png)

### 補充：為什麼用 console.log 印出來的值和想像中不一樣？

舉例：

```js
<script>
let arr = [2, 7, 5];　　//　[2, 7 , 5]
let max = arr[0];
for (let i=0; i<arr.length; i++) {　
    if (arr[i] > max) {
      max = arr[i];
    }
}
arr.push(3);　　//　[2, 7 , 5, 3] 註解是 Chrome 認為的 log
console.log(max);
</script>
```

#### 原因：

前後 `arr` 均為同一個變數，而 Chrome 會顯示該數最新儲存的值（抓取當下時間點的 log），而不是該 log 時間點的值。這個問題會出現在陣列和物件上。

#### 解決方法：

利用 `JSON.stringify()` 將陣列或物件轉成「字串」印出，也就是深拷貝（Deep Copy）。

參考資料：

1. [從Debugger學除錯](https://www.ithome.com.tw/voice/106635)
2. [六角學院 — Chrome 網頁除錯功能大解密](https://www.udemy.com/course/chrome-devtools/)
3. [你需要注意的 console.log 問題](https://blog.huli.tw/2020/03/23/console-log-bug/)
4. [[Javascript] 關於 JS 中的淺拷貝和深拷貝](https://larry850806.github.io/2016/09/20/shallow-vs-deep-copy/)

---

## 解題前需注意「輸入範圍」

在開始寫程式碼之前，必須注意輸入範圍，因為「不同範圍可能有不同限制」，而範圍會決定解題方法。比如說排列一百萬個數字跟排序十個數字，用的方法會不相同。

在不同限制中，需注意可能會遇到下列幾種狀況：

### 1. 空間限制

首先，對空間有個概念：

int: 4 bytes
double: bytes
JS 中的 Number: 8 bytes

由此可推算出，一百萬個數字所需記憶體：

(8 * 1e6 / 1024) = 7812 KB = 7.6 MB

那麼，十億個數字所需記憶體：

(8 * 1e8 / 1024) = 781200 KB = 7600 MB = 7.4 GB

由估算過程可知，我們不可能在程式語言中，宣告一個十億個元素儲存在陣列。
勢必得用其他方法，比如把排列好的數字先放在其他檔案等等。

### 2. 時間限制

對初學題目來說不太重要（先求有再求好），之後會再來談 big O，用來估算時間。

### 3. 型態限制
- int：-214783648 ~ 2147483648
- JS 數字： Number.MAX_SAFE_INTEGER：JavaScript 能儲存的最大值為 16 位數字，在超過這個範圍的運算可能會有誤差，例如：

```
Number.MAX_SAFE_INTEGER + 2 > Number.MAX_SAFE_INTEGER + 1
// false
Number.MAX_SAFE_INTEGER + 2 === Number.MAX_SAFE_INTEGER + 1
// true
```

- 浮點數精準度問題：這和電腦底層儲存數字的方式有關，許多程式語言都會有這個現象

```
0.1 + 0.2 === 0.3
// false
// 0.1 + 0.2 會印出的值為 0.30000000000000004
```

> 把範圍講清楚的題目，才是好題目。
> 沒有的話就問清楚！因為範圍決定解決的方法。

---

## 解題技巧：函式填空法

> 目的：簡化雙層迴圈，也就是切割問題。

### 判斷質數

以「[如何判斷質數](https://oj.lidemy.com/problem/1020)」為例：

> 印出陣列中的質數 = 印出陣列 + 質數

Step 1. 簡化問題：不知道怎麼寫的部分，先寫成函式 isPrime

```js
// 第一部分：主要邏輯

for (let i = 0; i < n; i++) {
  if (isPrime(arr[i])) {              // 變成函式：如果 arr[i] 是質數
  console.log("Prime")
  } else {
    console.log("Composite")
  }
}

function isPrime(n) {
  // TO DO
}
```

Step 2. 實作判斷質數邏輯

```js
// 第二部分：輔助函式

function isPrime(n) {
  if (n === 1) return false             // 1 不是函式，先去除
  for (let i = 2; i < n; i++) {
    if (n % i === 0) {
      return false
    }
  }
  return true                 　   // 注意放在迴圈外，前面都不符合，才符合質數
}
```

Step 3. 合併結果

```js
function isPrime(n) {
  if (n === 1) return false             
  for (let i = 2; i < n; i++) {
    if (n % i === 0) {
      return false
    }
  }
  return true                 　   
}

for (let i = 0; i < n; i++) {
  if (isPrime(arr[i])) {             
  console.log('Prime')
  } else {
    console.log('Composite')
  }
}

```

試著把 function 加回去，程式碼會複雜很多：

Step 1. 把函式拿掉，維持主要邏輯不便

```js
for (var i=0; i<n; i++) {
  let isPrime = true
  if (isPrime) { 
  console.log("Prime")
  } else {
    console.log("Composite")
  }
}
```

Step 2. 加上判斷質數程式碼

```js
// 雙層迴圈

for (var i=0; i<n; i++) {
  let isPrime = true
  if (arr[i] === 1) {
    isPrime = false
  }
  if (var j=2; j<arr[i]; j++) {         // 用 j 表示，是因為前面已出現變數 i
    if (arr[i] % j === 0) {
      is Prime = false
      break
    }
  }
  if (isPrime) { 
    console.log("Prime")
  } else {
    console.log("Composite")
  }
}
```

## 解題技巧：簡化法

### 印出星星

以「[如何印出好多星星](https://oj.lidemy.com/problem/1021)」為例：

> 目的：把大問題變成小問題。如果難的寫不出來，就先寫簡單的。

Step 1. 不會輸出 n 個星星，就先輸出一個就好

```js
for (let i=1; i<=N; i += 1) {
  console.log('*')
};
// 印出每行都有 *
```

Step 2. 搭配函式填空法

```js
for (let i=1; i<=N; i += 1) {
  printStar(i)
};

function printStar(n) {
  // TODO: 印出 n 個星星
}
```

Step 3. 利用迴圈重複加上星星

```js
for (let i = 1; i<=N; i += 1) {
  printStar(i)
};

function printStar(n) {
  let s = '';
  for (i = 1; i <= n; i+= 1) {
    s += '*'
  }
  console.log(s)
}
```

也可直接利用 `repeat` 函式來解題：


### 實戰：印出金字塔

預期輸出：

n = 1

```
*
```

n = 2
```
 *
***
```

n = 3
```
  *
 ***
*****
```

根據觀察可知規律為：
1. 一共有 n 層
2. 第 i 層會有 2i -1 個星星
3. 星星會置中
4. 需要 n - i 個空白

接著利用函式填空法：

```js
for (let i = 1; i <= n; i += 1) {
  printLayer(i, n)
}

function printLayer(i, n) {

}
```

加上一個輔助的函式：

```js
function repeat(str, n) {
  let s = ''
  for (let i = 1; i <= n; i += 1) {
    s += str
  }
  return s
}
```

再按照規律把答案填上去：

```js
for (let i = 1; i <= n; i += 1) {
  printLayer(i, n)
}

function printLayer(i, n) {
  // 空白 + 星星
  let str = repeat(' ', n - i) + repeat('*', 2*i - 1)
  console.log(str)
}

function repeat(str, n) {
  let s = ''
  for (let i = 1; i <= n; i += 1) {
    s += str
  }
  return s
}
```

### 實戰：九九乘法表

預期輸出：

```
1*1=1
1*2=2
...
1*9=9
2*1=2
...
9*9=81
```

首先用簡化法，產生 `1*1 ~ 1*9`：

```js
for (let i = 1; i <= 9; i += 1) {
  console.log('1*' + i '=' + 1*i )
}
```

包成函式，把 1 換成 k：

```js
function printTable(k) {
  for (let i = 1; i <= 9; i += 1) {
    console.log(k + '*' + i + '=' + k*i )
  }
}

printTable(1)
printTable(2)
...
printTable(9)
// 即可產生九九乘法表
```

其實就是再一個迴圈：

```js
function printTable(k) {
  for (let i = 1; i <= 9; i += 1) {
    console.log(k + '*' + i + '=' + k*i )
  }
}

// 這個會產生 1~9
for (let k = 1; k <= 9; k += 1) {
  printTable(k)
}
// 結果：產生 1*1~9*9
```

但總有一天要學會雙層迴圈，試著把上面的寫法合併：

```js
// 這個產生 1~9
for (let k = 1; k <= 9; k += 1) {
  // 這個也產生 1~9
  for (let i = 1; i <= 9; i += 1) {
    console.log(k + '*' + i + '=' + k*i )
  }
}
```

我們可以利用 Debugger 來觀察雙層迴圈如何執行。也就是先執行外層迴圈第一圈，然後進入內層迴圈第一到九圈，執行完再回到外層迴圈第二圈，依序執行下去，用文字表示如下：

```
k=1 i=1, 2, 3...9
k=2 i=1, 2, 3...9
k=3 i=1, 2, 3...9
...
k=9 i=1, 2, 3...9
```

### 實戰：印出 1~100 平方數

簡化法：先印出 1~100

```js
for (let i = 1; i <= 100; i += 1) {
  console.log(i)
}
```

搭配函式填空法

```js
for (let i = 1; i <= 100; i += 1) {
  if (isSquare) {
    console.log(i)
  }
}

function isSquare(n){

}
```

那麼，該如何判斷平方數呢？

開根號：`Math.sqrt(n)`
無條件捨去：`Math.floor(n)`

將 n 開根號後得到的數字，無條件捨去後再相乘，若還是等於 n 則為平方數。

```js
for (let i = 1; i <= 100; i += 1) {
  if (isSquare) {
    console.log(i)
  }
}

function isSquare(n){
  let root = Math.floor(Math.sqrt(n))
  return root * root === n            // 成立得到 true；反之得到 false
}
```

另一種想法：印出平方數，直到超過 100 為止。

```js
let i=1
while (i*i <= 100) {
  console.log(i*i)
  i += 1
}
```

### 作業檢討：印出聖誕樹

[LIOJ 1023：印出聖誕樹](https://oj.lidemy.com/problem/1023?_ga=2.105028611.1291128142.1593271357-1977277931.1590338596)

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
});

const lines = [];

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', (line) => {
  lines.push(line);
});

function isPrime(n) {
  if (n === 1) {
    return false;
  }
  for (let i = 2; i < n; i += 1) {
    if (n % i === 0) {
      return false;
    }
  }
  return true;
}

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  let n = Number(input[0])
  for (let i=1; i <= n ; i += 1) {
    printTree(i, n)
  }
  for (let i = 1; i <= n - 1; i += 1) {
    printBottom(n)
  }
}

function printTree(i, n) {
  console.log(
    repeat(' ', n - i) +
    repeat('*', 2*i - 1)
  )
}

function printBottom(n) {
  console.log(repeat(' ', n - 1) + '|')
}

function repeat(str, n) {
  let result = ''
  for (let i = 1; i <= n; i += 1) {
    result += str
  }
  return result
}

// 輸入結束，開始針對 lines 做處理
rl.on('close', () => {
  solve(lines);
});
```

### 作業檢討：NN 乘法表

[LIOJ 1024：NN 乘法表](https://oj.lidemy.com/problem/1024?_ga=2.109629445.1291128142.1593271357-1977277931.1590338596)

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
});

const lines = [];

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', (line) => {
  lines.push(line);
});

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  let N = Number(input[0])
  let M = Number(input[1])
  // 產生 1~N
  for (let k = 1; k <= N; k += 1) {
    // 產生 1~M
    for (let i = 1; i <= M; i += 1) {
      console.log(k + '*' + i + '=' + k * i)
      // 也可寫成 console.log(`${k}*${i}=${k*i}`)
    }
  }
}

// 輸入結束，開始針對 lines 做處理
rl.on('close', () => {
  solve(lines);
});
```

### 作業檢討：水仙花數

[LIOJ 1025：水仙花數](https://oj.lidemy.com/problem/1025?_ga=2.109629445.1291128142.1593271357-1977277931.1590338596)

水仙花數（Narcissistic number）定義為：「一個 n 位數的數字，每一個數字的 n 次方加總等於自身」。

例如說 153 是三位數，而 1^3 + 5^3 + 3^3 = 153。
而數字 0~9 也都是水仙花數，因為一位數 n 的 1 次方一定會等於自己。

解題難點：
1. 如何判斷幾位數
2. 如何取出各個數字

可直接判斷該數字是幾位數，但這個方法侷限於「需已知輸入範圍」，也就是必須知道有幾位數：


```js
// 回傳數字幾位數
function digitsCount(n) {
  if (n < 10) {
    return 1
  } else if (n < 1e2) {         // 1e2 代表 100 科學記號寫法
    return 2
  } else if (n < 1e3) {
    return 3
  } ...
} 
```

如何判斷幾位數：一直 `/ 10`

```js
// 回傳數字幾位數
function digitsCount(n) {
  if (n === 0) return 1
  let result = 0
  while (n != 0) {
    n = Math.floor(n / 10)        // 無條件捨去
    result += 1
  }
  return result
} 
```

取出各個數字：`% 10` 搭配 `/ 10`

```js
function isNarcissistic(n) {
  // 幾位數，宣告一個 m = n
  let m = n
  let digits = digitsCount(m)
  let sum = 0

  while (m != 0) {
    let num = m % 10
    // 可改成 Math.pow(num, digits) => num 的 digits 次方
    sum += num**digits
    m = Math.floor(m / 10)
  }
  
  // 可簡化成：return sum === n
  if (sum === n) {
    return true
  } else if {
    return false
  }
}

// 應用上方兩個函式即可完成輸出
function solve(input) {
  // 輸入 5 200 => ['5', '200']
  const temp = input[0].split(' ');
  let n = Number(temp[0]);
  let m = Number(temp[1]);
  for (let i = n; i <= m; i += 1) {
    if (isNarcissistic(i)) {
      console.log(i);
    }
  }
}
```

也可以把「數字轉成字串」，直接用字串來判斷幾位數：

例如：
```
'1234'.length => 4
'1234'.[0] => 1
```

```js
// 實際操作
function isNstr(n) {
  // 數字 + 空字串 => 字串
  // 或是 n.toString()、String(n)
  let str = n + ''         
  let digits = str.length
  let sum = 0
  for (let i = 0; i < str.length;; i+= 1) {
    sum += Number(str[i])**digits
  }
  return sum === n
}
```

### 實戰：判斷等差數列

判斷 [1, 3, 5, 7, 9] 是否為等差數列

解題技巧：
1. 先求公差：`arr[1] - arr[0]`
2. 再判斷 `arr[i] - arr[i-1]` 是否等於公差

```js
// 判斷是否為等差數列
function isValid(arr) {
  let d = arr[1] - arr[0]
  for (let i = 1; i < arr.length; i += 1) {
    if (arr[i] - arr[i-1] !== d) {
      return false
    }
  }
  return true
}

console.log(isValid([1, 3, 5, 7, 9]))  // true
console.log(isValid([1, 1, 1]))        // true
console.log(isValid([1, 2, 4]))        // false
```

解題時需考慮到 Edge case（邊緣條件）：

1. 空陣列
2. 只有一個元素的陣列

```
function isValid(arr) {
  // 涵蓋邊界條件，可避免不合法的存取
  if (arr.length <= 1) return true
  let d = arr[1] - arr[0]
  for (let i = 1; i < arr.length; i += 1) {
    if (arr[i] - arr[i-1] !== d) {
      return false
    }
  }
  return true
}
```

### 實戰：數字位數加總

與「判斷水仙花數」題型類似。

解法一：數學解

```js
function addDigits(n) {
  // 若 n 為負數，乘上 -1 變成正數
  if (n < 0) {
    n = n * -1
  }
  let sum = 0
  while (n != 0) {
    sum += n % 10
    n = Math.floor(n / 10)
  }
  return sum
}
console.log(addDigits(123))
```

解法二：字串偷吃步

```js
function addDigits(n) {
  let str = n + ''
  let sum = 0
  for (let i = 0; i < str.length; i += 1) {
    sum += Number(str[i])
  }
  return sum
}
console.log(addDigits(123))
```

---

## 經典題型

### [LIOJ 1026：判斷等比數列](https://oj.lidemy.com/problem/1026)

基本上和判斷等差數列作法類似：

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
});

const lines = [];

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', (line) => {
  lines.push(line);
});

// 判斷等比數列
function inValid(arr) {
  let r = arr[1] / arr[0]
  for (let i = 1; i < arr.length; i += 1) {
    if (arr[i] / arr[i - 1] !== r) {
      return false
    }
  }
  return true
}

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  let n = Number(input[0])
  let arr = input[1].split(' ')       // ['3', '9', '27'] => 得到字串的陣列
  if (inValid(arr)) {
    console.log('Yes')
  } else {
    console.log('No')
  }
}

// 輸入結束，開始針對 lines 做處理
rl.on('close', () => {
  solve(lines);
});
```

### [LIOJ 1027：信用卡號驗證](https://oj.lidemy.com/problem/1027)



### [LIOJ 1028：生命靈數](https://oj.lidemy.com/problem/1028)

```js
// 與位數加總做法相同
function addDigits(n) {
  let sum = 0
  while (n != 0) {
    sum += n % 10
    n = Math.floor(n / 10)
  }
  return sum
}

function solve(input) {
  // ['1991 11 7'] = > ['1991', '11', '7']
  let temp = input[0].split(' ')
  // 1991117
  let num = Number(temp[0] + temp[1] + temp[2])
  let p = addDigits(num)
  // 判斷是否不只一位
  while (p >= 10) {
    p = addDigits(p)
  }
  console.log(p)
}
```

### [LIOJ 1029：加減乘除](https://oj.lidemy.com/problem/1029)

```js
function solve(input) {
  let temp = input[0].split(' ')     // => ['3', '*', '4']
  let a = Number(temp[0])
  let b = Number(temp[2])

  if (temp[1] === '+') {
    console.log(a + b)
  } else if (temp[1] === '-') {
    console.log(a - b)
  } else if (temp[1] === '*') {
    console.log(a * b)
  } else {                            // 不是加減乘，就是除
    console.log(a / b)
  }
}
```

### [LIOJ 1030：判斷迴文](https://oj.lidemy.com/problem/1030)

```js
// 反過來輸出
function reverse(str) {
  let result = '';
  for (let i = str.length - 1; i >= 0; i -= 1) {
    result += str[i];
  }
  return result;
}

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  const str = input[0];
  if (reverse(str) === str) {
    console.log('True');
  } else {
    console.log('False');
  }
}
```

### [LIOJ 1031：完全平方和](https://oj.lidemy.com/problem/1031)

解一：找出比 N 小的完全平方數

```js
function square(n) {
  let result = 0
  for (let i = 1; i*i <= n; i += 1) {
      result += i*i
  }
  return result
}

console.log(square(30))
// 印出 55，也就是 1+4+9+16+25 = 55
```

解二：判斷是否為完全平方數

```js
function isSquare(n) {
  let r = Math.floor(Math.sqrt(n))
  return r*r === n
}

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  const n = Number(input[0]);
  let sum = 0
  for (let i = 1; i <= n; i += 1) {
    if (isSquare(i)) {
      sum += i
    }
  }
  console.log(sum);
}
```

### [LIOJ 1032：平面距離計算](https://oj.lidemy.com/problem/1032)

```js
function distance(x1, y1, x2, y2) {
  let dis = Math.sqrt(
    abs(x1 - x2) * abs(x1 - x2) +
    abs(y1 - y2) * abs(y1 - y2)
  )
  return dis.toFixed(2)            // 取到小數點第二位
}

// 回傳絕對值
function abs(n) {
  if (n < 0) {
    return -n
  }
  return n
}

function solve(input) {
  let t = Number(input[0])
  for (let i = 1; i <= t; i += 1) {
    let start = (i - 1)*4 + 1
    let x1 = Number(input[start])
    let y1 = Number(input[start + 1])
    let x2 = Number(input[start + 2])
    let y2 = Number(input[start + 3])
    console.log(distance(x1, y1, x2, y2))
  }
}
```

### [LIOJ 1033：最近點對](https://oj.lidemy.com/problem/1033)

```js
// 利用平面距離計算得到的函式
function distance(x1, y1, x2, y2) {
  let dis = Math.sqrt(
    abs(x1 - x2) * abs(x1 - x2) +
    abs(y1 - y2) * abs(y1 - y2)
  )
  return dis            // 取到小數點第二位
}

function abs(n) {
  if (n < 0) {
    return -n
  }
  return n
}

function solve(input) {
  let n = Number(input[0])
  let dots = []
  for (let i = 1; i < input.length; i += 1) {
    let temp = input[i].split(' ')
    dots.push({
      x: Number(temp[0]),
      y: Number(temp[1])
    })
  }

    let min = Infinity      // 最短距離先設一個最大值
    let ans = null
    for (let i = 0; i < dots.length; i += 1) {
      for (let j = i + 1; j < dots.length; j += 1) {
        let dis = distance(
          dots[i].x, dots[i].y, dots[j].x, dots[j].y
        )
        if (dis < min) {
          min = dis
          ans = {
            x1: dots[i].x,
            y1: dots[i].y,
            x2: dots[j].x,
            y2: dots[j].y
          }
        }
      }
    }

  if (ans.x1 > ans.x2) {
    console.log(ans.x2 + ' ' + ans.y2)
    console.log(ans.x1 + ' ' + ans.y1)
  } else if (ans.x1 < ans.x2) {
    console.log(ans.x1 + ' ' + ans.y1)
    console.log(ans.x2 + ' ' + ans.y2)
  } else {
    if (ans.y1 > ans. y2) {
      console.log(ans.x2 + ' ' + ans.y2)
      console.log(ans.x1 + ' ' + ans.y1)
    } else {
      console.log(ans.x1 + ' ' + ans.y1)
      console.log(ans.x2 + ' ' + ans.y2)
    }
  }
}
```

### [LIOJ 1034：凱薩加密](https://oj.lidemy.com/problem/1034)

```js
// 把字母右移 n 位
function ceaserCipher(n, s) {
  // 'a'.charCodeAt(0) = 97
  // 減去 97，可得到數字 0 ~ 25
  let code = s.charCodeAt() - 97
  // 英文字母有 26 個，當數字超過時，對編碼除 26 取餘數
  let newCode = (code + n) % 26
  // 取得真正的編碼
  return String.fromCharCode(newCode + 97)
}

function solve(input) {
  let n = Number(input[0])
  let str = input[1]
  let result = ''
  for (let i = 0; i < str.length; i += 1) {
    result += ceaserCipher(n, str[i])
  }
  console.log(result)
}
```

### [LIOJ 1046：圈圈叉叉](https://oj.lidemy.com/problem/1035)

輸入範例：

```
XXO
OXX
XOO
```

利用二維陣列的概念，可知：

```js
input[0] = 'XXO'         // 即 (0, i)
input[0][0] = 'X'        // 即 (0, 0)
```

```js
function whoWin(input){
  // 判斷橫的三排
  for (let i = 0; i < 3; i += 1 ) {
    if (input[i][0] === input[i][1] && input[i][1] === input[i][2]) {
      return input[i][0]
    }
  }
  // 判斷直的三排
  for (let i = 0; i < 3; i += 1) {
    if (input[0][i] === input[1][i] && input[1][i] === input[2][i]) {
      return input[0][i]
    }
  }
  // 判斷斜對角
  if (input[0][0] === input[1][1] && input[1][1] === input[2][2]) {
    return input[0][0]
  }
  if (input[0][2] === input[1][1] && input[1][1] === input[2][0]) {
    return input[1][1]
  }
  
  return  'DRAW'
}
```

### 作業 - [LIOJ 1004：聯誼順序比大小](https://oj.lidemy.com/problem/1004)

```js
// 判斷比大小
function compare(a, b, k) {
  if (a === b) {
    return 'DRAW';
  }

  // 若比小，就將 a b 互換，注意 k 是字串不能用 ===
  if (k == -1) {
    const temp = a;
    a = b;
    b = temp;
  }

  // 判斷字串長度是否相同
  // 不同：比較字串長度
  if (a.length > b.length) {
    return 'A';
  }
  if (a.length < b.length) {
    return 'B';
  }

  // 相同：從字串最左邊開始比大小
  if (a.length === b.length) {
    for (let j = 0; j < a.length; j += 1) {
      // 若相等，就繼續迴圈比下一位
      if (a[j] === b[j]) {
        continue;
      }
      if (a[j] > b[j]) {
        return 'A';
      } else {
        return 'B';
      }
    }
  }
}

function solve(input) {
  const n = Number(input[0]);

  // 長度為 512 個位數以內，不可轉成數字比大小
  for (let i = 1; i <= n; i += 1) {
    // 輸出會是陣列 => [1, 2, 1]
    let [a, b, k] = input[i].split(' ');
    console.log(compare(a, b, k));
  }
}
```

## 練習實作內建函式

### [LIOJ 1036：Array reverse](https://oj.lidemy.com/problem/1036)

```js
// 反轉陣列
function reverse(arr) {
  let result = []
  for(let i = arr.length - 1; i >= 0; i -= 1) {
    result.push(arr[i])     // 反轉 => [3, 2, 1]
  }
  return result
}

// 處理輸入
function solve(input) {
  let numbers = []
  for (let i = 1; i < input.length; i += 1) {
   numbers.push(input[i])  // [1, 2, 3]
  }
  // 傳進 reverse 函式，並印出結果
  let arr = reverse(numbers)
  for (let i = 0; i < arr.length; i +=1) {
    console.log(arr[i])
  }
}
```

也可以直接在處理輸入後，直接輸出翻轉後的結果：

```js
// 處理輸入
function solve(input) {
  let numbers = []
  for (let i = 1; i < input.length; i += 1) {
    numbers.push(input[i])  // [1, 2, 3]
  }

  for (let i = numbers.length - 1; i >= 0; i -= 1){
    console.log(numbers[i]);
  }
}
```

### [LIOJ 1037：Array filter](https://oj.lidemy.com/problem/1037)

```js
function filter(arr) {
  let result = []
  for (let i = 2; i < arr.length; i += 1) {
    // 跳過不符合的數字
    if (arr[i] == arr[0]) {
      continue;
    }
    result.push(arr[i])
  }
  return result
}

// 處理輸入
function solve(input) {
  let numbers = []
  for (let i = 0; i < input.length; i += 1) {
   numbers.push(input[i])  // [3, 5, 1, 3 , 3, 2, 8]
  } 
  // 傳進 filter 函式，並印出結果
  let arr = filter(numbers)
  for (let i = 0; i < arr.length; i +=1) {
    console.log(arr[i])
  }
}
```

或利用回傳函式 callback 來過濾 target：

```js
function filter(arr, callback) {
  let result = []
  for (let i = 0; i < arr.length; i += 1) {
    // 跳過不符合的數字
    if (callback(arr[i]) === true) {
      result.push(arr[i])
    }
  }
  return result
}

// 處理輸入
function solve(input) {
  // 排除目標
  let target = Number(input[0])
  let arr = []
  for (let i = 2; i < input.length; i += 1) {
    arr.push(Number(input[i]))    // [1, 3 , 3, 2, 8]
  } 
  // 傳進 filter 函式，留下不等於 target 的值
  // 也可寫成 filter(arr, element => element !== target)
  let newArr = filter(arr, function(element) {
    return element !== target
  })
  for (let i = 0; i < newArr.length; i +=1) {
    console.log(newArr[i])
  }
}
```

### [LIOJ 1038：Array indexOf](https://oj.lidemy.com/problem/1038?_ga=2.70826384.1291128142.1593271357-1977277931.1590338596)

```js
// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', (line) => {
  lines.push(line);
});

// LIOJ 1038：Array indexOf
function indexOf(arr, searchElement) {
  for (let i = 0; i < arr.length; i += 1) {
    if (arr[i] === searchElement) {
      return i
    }
  }
  return -1
}

// 處理輸入
function solve(input) {
  // 目標數字
  let target = Number(input[0])
  // 要檢查的數字
  let arr = []
  for (let i = 2; i < input.length; i += 1) {
    arr.push(Number(input[i]))    // => [1, 2, 3, 3, 3]
  }
  console.log(indexOf(arr, target))
}

```

### [LIOJ 1039：Array fill](https://oj.lidemy.com/problem/1039)

```js
// LIOJ 1039：Array fill
function fill(arr, value) {
  let result = []
  for (let i = 0; i < arr.length; i += 1) {
    // 陣列每個元素會被取代成 value，也可寫成 result[i] = value
    result.push(value)
  }
  return result
}


// 處理輸入
function solve(input) {
  let target = Number(input[0])
  let arr = []
  for (let i = 2; i < input.length; i += 1) {
    arr.push(Number(input[i]))    // => [1, 2, 3]
  }
  let newArr = fill(arr,target)
  for (let i = 0; i < newArr.length; i += 1) {
    console.log(newArr[i])
  }
}
```

### [LIOJ 1040：Array join](https://oj.lidemy.com/problem/1040)

```js
// LIOJ 1040：Array join
function join(arr, separator) {
  let result = '';
  for (let i = 0; i < arr.length; i += 1) {
    result += arr[i]
    if (i < arr.length -1) {
      result += separator
    }
  }
  return result
}

// 處理輸入
function solve(input) {
  const str = input[0];
  let arr = []
  for (let i = 2; i < input.length; i += 1) {
    arr.push(input[i])  // [1, 2, 3]
  }
  console.log(join(arr, str));
}
```

或是把字串 `1!2 3` 看成 `1 !2!3`，迴圈內就不用再進行判斷：

```js
function join(arr, separator) {
  let result = arr[0];
  for (let i = 1; i < arr.length; i += 1) {
    result += separator + arr[i]
  }
  return result
}
```

### [LIOJ 1041：String trim](https://oj.lidemy.com/problem/1041)

```js

```

### [LIOJ 1042：String toLowerCase](toLowerCasehttps://oj.lidemy.com/problem/1042)

```js
// LIOJ 1042：String toLowerCase
function toLowerCase(str) {
  let result = ''
  for (let i = 0; i < str.length; i += 1) {
    if (str[i] >= 'A' && str[i] <= 'Z') {
      let code = str.charCodeAt(i) 
      result += String.fromCharCode(code + 32)
    } else {
      result += str[i]        // 若不是大寫就直接加到字串
    }
  }
  return result
}

// 處理輸入
function solve(input) {
  console.log(toLowerCase(input[0]))
}
```

### [LIOJ 1043：String endsWith](https://oj.lidemy.com/problem/1043)

```js

```

### [LIOJ 1044：String padEnd](https://oj.lidemy.com/problem/1044)

```js
// LIOJ 1044：String padEnd
function padEnd(str, targetLength, padString) {
  if (str.lenght >= targetLength) {
    return str
  }
  // 從 'S 字串長度 + 1' 開始填充，直到預期字串長度
  while (str.length < targetLength) {
    for (let i = 0; i < padString.length; i += 1) {
      str += padString[i]
      // 若 S 達到預期字串長度，則結束迴圈
      if (str.length === targetLength) {
        return str
      }
    }
  } 
}

// 處理輸入
function solve(input) {
  let S = input[0]
  let target = Number(input[1])     // 預期字串的最後長度
  let padString = input[2]          // 要填充的字串
  console.log(padEnd(S, target, padString))
}
```

### [LIOJ 1045：String slice](https://oj.lidemy.com/problem/1045)

```js
// LIOJ 1045：String slice
function slice(str, beginIndex, endIndex) {
  let result = ''
  for (let i = beginIndex; i < endIndex; i += 1) {
    result += str[i]
  }
  return result
}

// 處理輸入
function solve(input) {
  let str = input[0]
  let start = Number(input[1])
  let end = Number(input[2])
  console.log(slice(str, start, end));
}
```
---

## 補充：[Lidemy OJ](https://www.youtube.com/watch?v=v7zv1ixaO3M) 解題方式

有三種方式可以驗證輸出：

1. 直接在 Command Line 介面直接執行 `node index.js`，即可直接輸入數字，按 ctrl+D 輸出結果（Windows 無法執行）
2. 直接在 `index.js` 呼叫 function
3. 新增檔案來放輸入，利用指令來產生輸出：`cat input.txt | node index.js`
補充：Windows 需改成 `cat input.txt | env node code.js` 或是 `cat input.txt | command node code.js`

固定輸入程式碼如下，答題時只需更改 function solve(lines)：

```js
var readline = require('readline');
var rl = readline.createInterface({
    input: process.stdin
});

var lines = []

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', function (line) {
    lines.push(line)
});

// 輸入結束，開始針對 lines 做處理
rl.on('close', function () {
    solve(lines)
})

// 拿到所有資料
function solve(lines) {
 
}
```

以[靈魂伴侶](https://oj.lidemy.com/problem/1010)為例：

```
var readline = require('readline');
var rl = readline.createInterface({
    input: process.stdin
});

var lines = []

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', function (line) {
    lines.push(line)
});

// 輸入結束，開始針對 lines 做處理
rl.on('close', function () {
    solve(lines)
})

function solve(lines) {
    var arr = lines[0].split(' ')        // 注意需要取陣列！
    var a = Number(arr[0])
    var b = Number(arr[1])
    if (a === b) {
        console.log('Yes')
    } else {
        console.log('No')
    }
}

solve([                           // 輸入是陣列，可用函式驗證是否正確
    '15 15'
])
```
---

## 補充：使用 eslint 檢查語法

> 相關筆記：[[week3] 設定 eslint：用來檢查語法的工具](https://hackmd.io/m95fj9SsRLe1XDZEdsyBww)

需注意宣告方式、縮排問題、結尾加分號等問題，修改後模板如下：

```
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
});

const lines = [];

// 讀取到一行，先把這一行加進去 lines 陣列，最後再一起處理
rl.on('line', (line) => {
  lines.push(line);
});

// 前面已宣告過 lines，這裡改用 input 作為參數，拿取內容 lines
function solve(input) {
  "在這裡輸入函式內容"
}

// 輸入結束，開始針對 lines 做處理
rl.on('close', () => {
  solve(lines);
});
```




