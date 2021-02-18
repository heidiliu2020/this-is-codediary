###### tags: `Angular`
# 初探 Angular｜基本介紹與環境建置

## 關於 Angular

> The modern web developer's platform（一套框架，多種平臺）

![](https://i.imgur.com/I9XUKCr.png)

Angular 是由 Google 開發，基於 TypeScript 編寫的 JavaScript 前端框架，常和同為三大框架的 React 和 Vue 比較。

1.x 版的 AngularJS 其實還算是函式庫，直到 Angular 2 版本出現之後，與其說是一套框架，更常被稱作是一個開發平台，因為 Angular 強大的地方在於，整合了非常多功能給開發者使用：

* i18n - 多國語系化
* Animation - 動畫
* Router - 提供路由機制開發 SPA 
* [Angular CLI](https://cli.angular.io/) - 命令列工具，可用來建立專案、建立應用和函式庫程式碼
* Material - Google 力推的 Material 網頁設計框架
* Augury - 官方出版的 Google Chrome 開發者工具，用於分析頁面中所用元件的狀態與方法

> 可參考 [Angular 2 skill map](https://github.com/TeamStuQ/skill-map/blob/master/data/designbyStuQ/png-Angular2-by-StuQ.png)。

### Angular 基本架構

以下是官網提供的 Angular 架構圖：

![](https://i.imgur.com/TevuOoo.png)

Angular App 主要由這八個元素構成：

* Metadata 描述資料
  * 在 `@Component` 裝飾器函式中，用於描述該元件的附加資料
* Template：模板，呈現畫面
  * 權重：template input variable = template reference variable > component variable
* Component：元件
  * Directive + Template 結合的產物
* Dependency Injection：倚賴注入
  * DI 是一種 Design Pattern，為了將功能集中，減少相依性的設計模式
* Service：服務，針對特定事情處理邏輯
* Directive：指令，有三種呈現形式
  * Structural Directive 結構指令
  * Attribute Directive 屬性指令
* Data Binding：資料綁定
  * proerty binding 屬性單向綁定
  * event binding 事件單向綁定
  * two way binding 屬性與事件雙向綁定
* Module：模組，打包功能

參考資料：
- [Angular小教室 - 用Note List了解頁面基本元素 (基礎篇)](https://github.com/marshal604/blog/issues/2)

---

## 環境建置

> 詳細可參考 [Angular 官方文件](https://angular.io/docs)。

### 前置作業

要執行一個簡單的 Angular 應用，可使用 Angular CLI 工具來加速開發，建議使用 [Node.js](https://nodejs.org/en/) 的 npm 套件管理工具來安裝 Angular CLI。

可在終端機輸入下方指令，查看版本資訊，確認是否有安裝成功：

```
$ node -v
```

### 第一步：安裝 Angular CLI

使用 Angular CLI 開發工具，可透過 ng 命令，快速產生 Directive 元件、服務元件（Service Component）、管線元件（Pipe Component），或是路由設定，並執行多種開發任務，比如測試、打包和釋出。

輸入下方指令進行安裝：

```
$ npm install -g @angular/cli
```

![](https://i.imgur.com/vT0Tdas.png)

如果在 mac 上安裝失敗，指令必須加上 `sudo` 切換成管理員帳戶才有權限安裝：

```
$ sudo npm install -g @angular/cli
```

可輸入 `ng version` 或是 `ng v` 指令查看版本資訊，確認是否安裝成功：

![](https://i.imgur.com/uKEUiSA.png)

### 第二步：建立 Angular 初始專案

透過 `new` 指令，可在當前目錄建立一個 Angluar 工作空間 （Workspace），並初始化 Angular 專案所需要的配置：

```
$ ng new my-app
```

![](https://i.imgur.com/IKVBKmI.png)

安裝需要一段時間，完成後會出現 `Packages installed successfully. Successfully initialized git.` 這段文字：

![](https://i.imgur.com/kQvlGXB.png)

除了透過 NPM 安裝必要套件，還會利用 Git 進行版本控制，並完成第一次提交（commit）。

建立專案內容如下：

- 根目錄 my-app：一個新的 workspace
- src/：主要開發原始碼
  - app/：整個網頁應用程式的 Module、Component、Service
  - assets/：圖片等靜態資源
  - environments/：環境變數設定檔
- e2e/：E2E 測試的程式碼
- browserslist：用來定義專案支援的瀏覽器與版本
- angular.json：Angular CLI 設定檔
- karma.conf.js：[Karma](https://karma-runner.github.io/3.0/index.html) 設定檔，用來進行單元測試，專案建立時會預設使用這套工具
- tsconfig.json：TypeScript 編譯設定檔
- tslint.json：TSLint 設定檔，為 TypeScript 的格式驗證工具，提高程式碼可讀性、偵測功能性錯誤
- 其他相關配置檔案

![](https://i.imgur.com/YAtLAJT.png)

> 詳細結構可參考這篇：[[Angular 深入淺出三十天] Day 04 - 資料夾結構說明](https://ithelp.ithome.com.tw/articles/10203534)。

### 第三步：啟動開發伺服器

在本地端 build 和 serve 專案，並運行在 `http://localhost:4200/`：

```
$ cd my-app
$ ng serve --open
```

![](https://i.imgur.com/Fm81XkW.png)

- ng serve 指令會啟動開發伺服器，並監視檔案變化，一旦有改變就會重新建構應用
- `--open` 或簡寫 `-o` 可自動開啟瀏覽器

預設畫面如下：

![](https://i.imgur.com/FspJDDM.png)

### VSCode 擴充套件：Angular Extension Pack

若使用 VSCode（Visual Studio Code）編輯器，可安裝一個叫做 Angular Extension Pack 的擴充套件，整合了 Angular 相關擴充套件方便使用：

![](https://i.imgur.com/tda1vFZ.png)


參考資料：
- [Angular 全集中筆記 系列](https://ithelp.ithome.com.tw/users/20109645/ironman/3762)
- [Angular 深入淺出三十天 系列](https://ithelp.ithome.com.tw/users/20090728/ironman/1600)
- [Angular 大師之路 系列](https://ithelp.ithome.com.tw/users/20020617/ironman/1630)
- [前端工程的夢幻逸品：Angular 2 開發框架介紹
](https://blog.miniasp.com/post/2016/07/26/Introduction-to-Angular-2)