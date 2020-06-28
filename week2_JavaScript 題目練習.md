###### tags: `JavaScript`

# [week 2] JavaScript - 綜合題目練習 Lv1

## 練習一：印出一到九

請你分別用 for loop 以及 while 迴圈，印出 1~9。

預期輸出：

```
1
2
3
4
5
6
7
8
9
```

解法：

```
// for loop
for (var i = 1; i<=9; i++) {
    console.log(i)
}

// while 迴圈
var i =1

while (i<=9) {
    console.log(i)
    i++
}

// do while 迴圈
var i = 1

do {
    console.log(i)
    i++
} while (i<=9)
```

## 練習二：寫一個能夠印出 1~n 的函式

請寫一個函式叫做 print，接收一個是數字的參數 n，並且印出 1~n。

```
print(1) 預期輸出：
1

print(3) 預期輸出：
1
2
3

print(9) 預期輸出：
1
2
3
4
5
6
7
8
9
```

解法：

```
function print(n) {
    for(var i=1; i<=n; i++){
    console.log(i)
    }
}

print(n)
```

## 練習三：寫一個能夠印出 n 個 * 的函式

寫一個函式 star，接收一個參數 n，並印出 n 個 *
（禁止使用內建函式 repeat）

```
star(1) 預期輸出：
*
star(5) 預期輸出：
*****
star(10) 預期輸出：
**********
```

解法：

```
function star(n) {
    var answer = ''
    for (var i = 1; i <= n; i++) {
        answer += '*'
    }
    console.log(answer)
}

star(5)            // 印出 *****
```

由此題可實作出 `repeat()` 函式：

```
function star(str, n) {
    var answer = ''
    for (var i = 1; i <= n; i++) {
        answer += str
    }
    console.log(answer)
}

star('*', 7)            // 印出 *******
```

## 練習四：寫一個能回傳 n 個 * 的函式

請寫出一個叫做 star 的 function 並且接受一個參數 n，能回傳 n 個 *。

```
star(1) 會回傳 *
star(5) 會回傳 *****

所以 console.log(star(5)) 的預期輸出是：
*****
```

解法：

```
var answer = ""
function star(n) {
    for (var i = 1; i <= n; i++) {
        answer += "*"
    }
    return answer
}

console.log(star(5))        // 印出 *****
```

## 練習五：判斷大小寫

請寫一個叫做 isUpperCase 的 functuon，並且接收一個字串，回傳這個字串的第一個字母是否為大寫。

```
isUpperCase("abcd") 正確回傳值：false

isUpperCase("Abcd") 正確回傳值：true

isUpperCase("ABCD") 正確回傳值：true

isUpperCase("aBCD") 正確回傳值：false
```

解法：

```
function isUpperCase(str) {
    var char = str[0]
    if (char >= "A" && char <= "Z"){
        return true
    } else {
        return false
    }
}

console.log(isUpperCase("abcd"))    // 印出 false
console.log(isUpperCase("Abcd"))    // 印出 true

// 又可簡化如下：

function isUpperCase(str) {
    var char = str[0]
    return char >= "A" && char <= "Z"
}
// 透過邏輯運算，可直接回傳 true or false，就不需重複寫出判斷式
```

## 練習六：回傳第一個大寫字母以及它的 index

請寫一個 function position，接收一個字串並回傳這個字串裡面的第一個大寫字母跟它的 index，若沒有則回傳 -1。

```
position("abcd") 正確回傳值：-1

position("AbcD") 正確回傳值：A 0

position("abCD") 正確回傳值：C 2
```

解法：

```
function position(str) {
    for (var i=0; i<= str.length; i++) {
        if (str[i] >= "A" && str[i]<= "Z") {
            return str[i] + ' ' + i        // 字母 + 空格 + index
        }   
    } 
    return -1                    // 注意不能寫在迴圈裡
}
```

## 練習七：回傳陣列裡面所有小於 n 的數的數量

請寫出一個函式 findSmallCount，接收一個陣列跟一個數字 n，回傳有多少個數小於 n。

```
findSmallCount([1, 2, 3], 2) 預期回傳值：1

findSmallCount([1, 2, 3, 4, 5], 0) 預期回傳值：0

findSmallCount([1, 2, 3, 4], 100) 預期回傳值：4
```

解法：

```
// 檢查陣列中每個數字是否小於 n：

function findSmallCount(arr, n) {
    var counter = 0
    for (var i = 0; i < arr.length; i++) {
        if (arr[i] < n){
            counter++
        }
    }
    return counter
}
```

```
// 或使用 filter() 函數：

function findSmallCount(arr, n) {
    return arr.filter(function(item) {
        return item < n        // 所有小於 n 的數
    }).length                  // 所有小於 n 的數的數量
}
```

## 練習八：回傳陣列裡面所有小於 n 的數的總和

請寫一個函式 findSmallerTotal，接收一個陣列以及數字 n，回傳陣列裡面所有小於 n 的數的總和。

```
findSmallerTotal([1, 2, 3], 3) 正確回傳值：3

findSmallerTotal([1, 2, 3], 1) 正確回傳值：0

findSmallerTotal([3, 2, 5, 8, 7], 999) 正確回傳值：25

findSmallerTotal([3, 2, 5, 8, 7], 0) 正確回傳值：0
```

解法：

```
function findSmallerTotal(arr, n) {
    var total = 0
    for (var i = 0; i < arr.length; i++) {
        if (arr[i] < n) {
            total += arr[i]
        }
    } 
    return total
}
```

## 練習九：回傳陣列裡面所有小於 n 的數

請寫一個函式 findAllSmall，接收一個陣列跟一個數字 n，回傳一個裡面有所有小於 n 的數的陣列（需按照原陣列順序）。

```
findAllSmall([1, 2, 3], 10) 正確回傳值：[1, 2, 3]
findAllSmall([1, 2, 3], 2) 正確回傳值：[1]
findAllSmall([1, 3, 5, 4, 2], 4) 正確回傳值：[1, 3, 2]
```

解法：

```
function findAllSmall(arr, n) {
    var answer = []
    for (var i = 0; i < arr.length; i++) {
        if (arr[i] < n) {
            answer.push(arr[i])
        }
    } 
    return answer
}
```

```
// 或使用 filter() 函數：

function findAllSmall(arr, n) {
    return arr.filter(function (item) {
        return item < n
    })
}
```

## 練習十：回傳陣列總和

請寫一個 function sum，接收一個陣列並回傳陣列中數字的總和。

```
sum([1, 2, 3]) 預期回傳值：6

sum([-1, 1, 2, -2, 3, -3]) 預期回傳值：0
```

解法：

### 方法一

```
function sum(arr) {
    var total = 0
    for (var i = 0; i < arr.length; i++) {
        total += arr[i]
    }
    return total
}
```

### 方法二

也可利用 `.reduce(function)` 來解題，reduce() 函式作用如下:

```
function(accumulator, currentValue) {
    return currentValue + accumulator
}

accumulator（累積器）：記憶起來的變數
currentValue：現在的值
在這個函式 return 的值，會成為下一個 accumulator 參數
```

以練習時為例：

```
function sum(arr) {
    return arr.reduce (function(accumulator, value) {
        return accumulator + value
    })
}

sum([1, 2, 3])             // 回傳 6
sum([-1, 1, 2, -2, 3, -3]) // 回傳 0
```

也可設定 accumulator 的初始值，預設值為 0：

```
function sum(arr) {
    return arr.reduce (function(accumulator, value) {
        return accumulator + value
    }, 10)                        // 初始值為 10
}

sum([1, 2, 3])             // 回傳 16
sum([-1, 1, 2, -2, 3, -3]) // 回傳 10
```

### 方法三

也可以使用 `forEach()` 來解題，和 `map()` 類似，兩種函式均可「用來存取陣列中的每個元素」。差別在於 `forEach()` 不用進行 return。

而這個方法和 `reduce()` 的差異在於「需在外部宣告變數」，用來存取狀態。

```
function sum(arr) {
    var total = 0                       // 宣告變數
    arr.forEach(function(value) {
        total += value
    })
    return total
}
```

拿掉宣告變數，並在 function 增加參數來存取，就變成 `reduce()` 函式了：

```
function sum(arr) {
    return　arr.reduce(function(total, value) {
        return total + value           // 用 return 來存取 total 的值
    })
}
```
