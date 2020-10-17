###### tags: `Front-End` `gulp` `webpack`
# [week 13] 前端工具之三 - gulp、webpack

> 本篇為 [[FE201] 前端中階：那些前端會用到的工具們](https://lidemy.com/p/fe201) 這門課程的學習筆記。如有錯誤歡迎指正。

## 前言

在使用新工具之前，大致會依照下列步驟：
1. 安裝工具
2. 閱讀官方文件
3. 更改設定檔

---

## gulp：整合流程

[gulp](https://gulpjs.com/) 是一個前端自動化構建工具，開發者可使用它來建構自動化工作流程。

### 為什麼需要 glup？

為了將工作流程自動化，也就是整合前端開發環境。藉由簡化工作量，可讓開發者將重點放在功能的開發上。

例如下列功能：
- 讓網頁自動重新整理
- 編譯 SASS 、程式碼檢測
- 壓縮 CSS, JS, 圖檔
- 自訂任務流程..


### 如何安裝

> [Node.js](https://nodejs.org/en/) 是一個讓 Javascript 運行在服務端的開發平台。

由於 gulp 是基於 Node.js 進行開發，使用前必須先安裝 node.js、npm。接著即可按照[官方文件 Quick Start](https://gulpjs.com/docs/en/getting-started/quick-start/) 安裝 gulp：

1. `npm install --global gulp-cli`：安裝 gulp-cli
2. 開啟需使用 gulp 的專案資料夾
3. `npm init`：進行初始化
4. `npm install --save-dev gulp`：安裝 gulp package
5. `gulp --version`：可使用指令檢查是否安裝成功


### gulp 提供的 API

- gulp.task　執行工作
- gulp.src　執行資料來源
- gulp.dest　執行結果位置
- gulp.watch　監視執行過程中，資料是否變更

參考資料：
- [什麼是gulp,怎麼使用gulp | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/418553/)
- [前端中階：gulp. 介紹| by Hugh's Program learning | Medium](https://medium.com/@hugh_Program_learning_diary_Js/%E5%89%8D%E7%AB%AF%E4%B8%AD%E9%9A%8E-gulp-791132bea9f1)

---

## Webpack：打包程式碼

### 什麼是 Webpack

Webpack　是一套模組整合工具。可將零散的 JavaScript 模組打包，然後在瀏覽器上運行，解決舊瀏覽器不支援部分新語法的問題，也利於後續管理與維護。

此外，Webpack 也提供了前端開發缺乏的模組化開發方式，可將各種靜態資源視為模組，例如 JS、CSS、SASS、圖片檔等，透過不同的 loader 將資源轉換並載入，再利用 Webpack 進行打包成 JS 檔，生成最佳化過的程式碼。

### 為什麼要使用 Webpack？

若不使用 Webpack，可能會遇到下列問題：

1. 瀏覽器支援度：由於部分瀏覽器不支援 ES6 模組，例如 IE，若要使用 import、export 等語法，則需要其他工具來進行轉換。
2. 不易引用套件：若想要引用他人撰寫的 library 套件，需考慮相容性問題，以及可能造成命名衝突。

### 如何安裝

跟著官方教學步驟 [Getting Started](https://webpack.js.org/guides/getting-started/) 安裝 webpack：

```
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

![](https://i.imgur.com/MP3ufbd.png)

### 相關語法

> `dev`：代表開發時才會使用，打包後就不會出現在裡面（記錄在 package.json -> devDependencies）

- 下載 jquery

```
npm install jquery webpack-cli --save-dev
```

- css-Loader：用來解析 CSS 檔

```
npm install --save-dev css-loader
npm install --save-dev style-loader
```

- babel-loader

```
npm install -D babel-loader @babel/core @babel/preset-env
```

- sass-loader

```
npm install sass-loader sass webpack --save-dev
```

### 執行步驟

- 需要進行編譯的檔案，通常不會直接放在根目錄，而是會先放在 src（source code），使用工具轉換後再放到其他地方。
- webpack 預設執行檔案路徑如下：

```
webpack-demo/src/index.js
```

以 index.js 引入 utils.js 為例：

#### utils.js

```javascript=
const Utils = {
  first: function(str) {
    return str[0]
  }
}

module.exports = Utils
```

#### index.js

```javascript=
const utils = require('./utils')

console.log(utils.first('abc'))
```

在 CLI 執行 `npm src/index.js` 後印出 `a`，代表成功引入檔案。

![](https://i.imgur.com/QmPof6p.png)

接著以 `npx webpack` 執行打包，完成會自動把檔案 main.js 放在資料夾 dist。

若執行檔案 `npm dist/main.js` 仍會得到相同結果：

![](https://i.imgur.com/aE3nKoD.png)

接著在 index.html 引入：

```javascript=
<script src="dist/main.js"></script>
```

即可透過 webpack 打包 module，讓我們能夠在瀏覽器上執行使用 require 語法。

### `webpack.config.js`：預設執行檔

在安裝好 loader 後，需設定執行檔，透過指定 rules 來執行檔案：

#### 基本設定

```javascript=
// webpack.config.js
const path = require('path')

module.exports = {
   mode: '',
  entry: ['./src/index'], // 在 index 檔案後的 .js 副檔名是可選的
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}
```

#### 屬性介紹

- mode：使用的模式。預設為 production，代表正式生產環境版本；開發使用 development 則不會進行壓縮
- entry：是 bundle 的進入點，entry 是一個陣列。根據需求，webpack 允許可以有多個進入點，來產生多個 bundle 檔案。
- output：宣告 webpack 輸出的形式
  - path：存放 bundle 檔案的位置
  - filename：bundle 檔案名稱

#### 接著以打包 style.css 和 index.js 所需進行的設定為例：

```javascript=
const path = require('path');

module.exports = {
  mode: '',
  entry: './src/index.js',
  output: {
    filename: 'main.js',    // 輸出檔案名稱
    path: path.resolve(__dirname, 'dist'),　    // 輸出位置
  },
  // 載入 loader
  module: {
    rules: [
      {
        test: /\.css$/i,      // .css 結尾的檔案均使用 css-loader 解析
        use: ['style-loader', 'css-loader'],
      },{
        test: /\.m?js$/,      // .mjs 結尾的檔案均使用 babel-loader 解析
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ],
  },
};
```

參考資料：
- [webpack 新手教學之淺談模組化與 snowpack](https://blog.huli.tw/2020/01/21/webpack-newbie-tutorial/)
- [Webpack 初學者教學課程Part1 · Webpack Tutorial 繁體中文](https://neighborhood999.github.io/webpack-tutorial-gitbook/Part1/)
- [Webpack教學 (一) ：什麼是Webpack? 能吃嗎？](https://medium.com/i-am-mike/%E4%BB%80%E9%BA%BC%E6%98%AFwebpack-%E4%BD%A0%E9%9C%80%E8%A6%81webpack%E5%97%8E-2d8f9658241d)

---

### 總結：gulp vs webpack

#### gulp

- 是一套任務管理工具（task manager）
- 目的：提供自動化與流程管理，整合前端開發環境，藉由簡化工作量，可讓開發者將重點放在功能的開發上
- 功能：提供自訂任務流程，例如 babel、scss、壓縮、重新整理、校正時間等

#### Webpack

- 是一套模組整合工具（module bundler）
- 目的：利用模組化的概念，將各種資源打包成能在瀏覽器上執行的程式碼

由此可知，兩者目標其實並不相同，但是均能夠達到部分功能，因此容易被混淆，例如：

1. 使用 Babel 將 ES6 編譯成 ES5 語法
2. 將 SASS 檔編譯成 CSS 檔
3. 壓縮 CSS, JS, 圖檔等

簡言之，gulp 是用來管理任務，建構自動化流程的工具；而 Webpack 則是將資源打包，提供模組化開發方式。

參考資料：
- [Gulp和webpack的區別，是一種工具嗎？](https://www.itread01.com/content/1550221216.html)