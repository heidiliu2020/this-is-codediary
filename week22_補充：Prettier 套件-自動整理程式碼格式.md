###### tags: `Front-End`
# [week 22] 補充：Prettier 套件 - 自動整理程式碼格式

## Prettier：Code formatter

> 相關安裝套件與如何操作可參考[官方文件](https://create-react-app.dev/docs/setting-up-your-editor/)。

Prettier 是一個 Code formatter，能夠將 JavaScript, TypeScript, CSS 程式碼格式化，進而統一程式碼風格（Coding Style）。

### 安裝套件 & 設定檔

為了進行程式碼格式化，共需要安裝下列三個套件：

- `husky`: makes it possible to use githooks as if they are npm scripts.
- `lint-staged`: allows us to run scripts on staged files in git. See this blog post about lint-staged to learn more about it.
- `prettier`: is the JavaScript formatter we will run before commits.

並在 package.json 檔案中加上套件相關資訊：

```json=
+  "husky": {
+    "hooks": {
+      "pre-commit": "lint-staged"
+    }
+  },

+ "lint-staged": {
+   "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": [
+     "prettier --write"
+   ]
+ },
```

簡單來說，就是在 pre-commit 之前，透過 husky -> lint-staged -> prettier 這個傳遞過程，對 commit 的檔案做 prettier。

安裝完成後，在 commit 之前，prettier 就會自動檢查程式碼並進行格式化：

![](https://i.imgur.com/JlV2aOS.png)

### 在 VSCode 安裝 prettier

除了安裝套件，在 VSCode 也有提供 [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) plugin 能夠使用。

![](https://i.imgur.com/PBQKXwF.png)

接下來，就可以設定在存檔時執行 prettier，在設定中把 Format on Save 選項打勾：

![](https://i.imgur.com/OEF4TpC.png)

接著隨意更改 JS 檔案並存檔，會看到右下角的小鈴鐺出現通知：

![](https://i.imgur.com/vBbep8k.png)

點選設定後，可選取預設格式器：

![](https://i.imgur.com/w33Lf4T.png)

這時候再次存檔，就會發現又下方有顯示 Prettier 運行結果，檔案中若有沒加逗號的地方、或是多於的空格會被格式化：

![](https://i.imgur.com/1luVuIV.png)

透過 Prettier 這套工具，可以快速將程式碼格式化，但需注意 Prettier 只管理格式問題，語法上的錯誤仍需由 eslint 套件來檢查。

> 可參考之前的學習筆記：[[week3] 設定 eslint：用來檢查語法的工具](https://hackmd.io/@Heidi-Liu/note-eslint)