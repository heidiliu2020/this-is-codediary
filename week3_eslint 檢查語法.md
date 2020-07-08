###### tags: `JavaScript`
# [week3] 設定 eslint：用來檢查語法的工具

## 什麼是 eslint？

ESLint（ECMAScript + lint）是用來檢查 JavaScript 程式碼的工具。可在 commit 前檢查語法錯誤、提示潛在的 bug，藉此有效提高程式碼質量，和統一基本的 coding style。

> 參考資料：
> 1. [[JS] 使用 ESLint 提高程式碼品質](https://larrylu.blog/improve-code-quality-using-eslint-742cf1f384f1)
> 2. [深入淺出eslint——關於我學習eslint的心得](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/693370/)

### 如何安裝

要在 Node.js 上運行 ESLint，必須安裝 npm，接著運行下列程式碼：

#### `npm install -g eslint `：安裝 eslint 套件

在 package.json > scripts > lint，可查看 eslint 的檢查範圍：

```js
// 代表 homeworks 底下所有的 js 檔案都要經過 eslint 的規則檢查
{
	"scripts": {
		"lint": "eslint ./homeworks/**/*.js"
	}
}
```

- 若程式碼均符合規則，即可順利 commit：

![](https://i.imgur.com/c9Nv0Gh.png)

- 若有不符合規則的程式碼，則會出現下列訊息，告知幾行幾欄出現 error 需進行修正，通過才可進行 commit：

![](https://i.imgur.com/FLkbnqX.png)


---

## 如何忽略檢查語法

若想忽略規則，可在檔案中加入 `eslint-disable` 指令。

### 忽略所有規則

放在檔案開頭：
```
/* eslint-disable */                    // 在該檔案關閉 ESLint
```

### 忽略特定規則

```
/* eslint no-unused-vars: 0 */            // off 或 0 代表關閉規則
```

### 忽略單行

忽略所有規則。可分為「忽略同一行」或「忽略下一行」兩種寫法。

```
var a = 10;   // eslint-disable-line

// eslint-disable-next-line
var result = '';
```

忽略特定規則。直接在指令後面加「想要忽略的規則名稱」。

```
alert('Hello');       // eslint-disable-line no-alert

// eslint-disable-next-line no-alert
alert('Hello');
```

---

## eslint 報錯例子

### 1. 換行符問題

`Expected linebreaks to be 'LF' but found 'CRLF' `

原因：不同系統不同工具下換行符的問題。

解決方法：如果是 windows 系統，點擊vscode右下角，將默認 CRLF 改為 LF。

> 參考資料：
> 1. [項目中 eslint 報錯： Expected linebreaks to be 'LF' but found 'CRLF'](https://www.twblogs.net/a/5bb0204c2b7177781a0fc038)
> 2. [VUE項目中eslint報錯： Expected linebreaks to be 'LF' but found 'CRLF'](https://blog.csdn.net/weixin_42342975/article/details/81486373)

### 2. 宣告變數需使用 const 或 let

`'arr' is never reassigned. Use 'const' instead       prefer-const`

原因：eslint 會嚴格的抓 const 跟 let 的使用，而不是用 var 宣告變數。

解決方法：沒有重新賦值過的變數都要用 const 宣告，其餘則是let。

 - const（常數）：代表不變的數值。宣告時就要同時賦值，且不能再更動。
 - let：和 var 作用差不多，但 let 所宣告的變數，只在代碼塊區域內有效。也就是說，被所屬 `{ }` 包起來的 let 變數，不會被外面（全域）影響。

舉個例子：

```
let price = 20;          // 全域變數
if (true) {              
  let price = 30;        // 區域變數
}
console.log(price)      // 印出 20，因為區域變數不會覆蓋全域變數
```

> 參考資料：[Day02【ES6 小筆記】變數宣告 - let、const 哪裡好？跟 var 說掰掰](https://ithelp.ithome.com.tw/articles/10213188)

#### 3. 縮排問題

原因：開頭必須使用 2 個空白來設定縮排，但 VSCode 預設是 4 個空白。

解決方法：

1. 點選狀態列上的 [空白:4] 
2. 選擇 [使用空格鍵進行縮排]
3. 選擇定位點長度 2 
4. 即可修改成 2 個空白設定縮排

> 參考資料：[[Rails] Visual Studio Code 修改 Tab設定](https://medium.com/@yujiewang/rails-visual-studio-code-%E4%BF%AE%E6%94%B9-tab%E8%A8%AD%E5%AE%9A-74b0fc748f28)
