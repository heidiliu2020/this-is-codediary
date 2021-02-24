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

* Metadata 元資料、描述資料
  * 在 `@Component`、`@NgModule` 裝飾器函式中，用於描述該元件的附加資料
  * 元資料中指定的樣式只會作用於該元件
* Template：模板，呈現畫面
  * 權重：Template Input Variable（模板輸入變數）= Template Reference Variable（模板參考變數） > Component Variable
* Component：元件，為最小 UI 單位
  * Directive + Template 的組合
* Dependency Injection：倚賴注入
  * DI 是一種 Design Pattern，為了將功能集中，減少相依性的設計模式
* Service：服務，針對特定事情處理邏輯
* Directive：指令，有三種呈現形式
  * Component 元件，擁有樣板的指令
  * Structural Directive 結構指令
    * 修改檢視結構
    * 如：`*ngFor`、`*ngIf`、`NgSwitch`，星號是用來簡化復雜語法的「語法糖」
  * Attribute Directive 屬性指令
    * 改變元素的外觀或行為
    * 如：`NgClass`、`NgStyle`、`NgModel`
* Data Binding：資料綁定
  * Property Binding 屬性單向綁定
  * Event Binding 事件單向綁定
  * Two Way Binding 屬性與事件雙向綁定
* Module：模組，打包功能以實現功能模組化

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

如果是在 mac 系統，指令必須加上 `sudo`，切換成管理員帳戶才有權限安裝：

```
$ sudo npm install -g @angular/cli
```

可輸入 `ng version` 或是 `ng v` 指令查看版本資訊，確認是否安裝成功，畫面如下：

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

## 預設專案架構

> 詳細可參考這篇：[[Angular 深入淺出三十天] Day 04 - 資料夾結構說明](https://ithelp.ithome.com.tw/articles/10203534)。

建立好 Angular 專案之後，預設架構如下：

![](https://i.imgur.com/YAtLAJT.png)

- 根目錄 my-app：一個新的 workspace
- 子目錄 src/：主要開發原始碼
  - app/：包含整個網頁應用程式的 Module、Component、Service
  - assets/：圖片等靜態資源
  - environments/：環境變數設定檔
- 子目錄 e2e/：E2E 測試的程式碼
- browserslist：用來定義專案支援的瀏覽器與版本
- angular.json：Angular CLI 設定檔
- karma.conf.js：[Karma](https://karma-runner.github.io/3.0/index.html) 設定檔，用來進行單元測試，專案建立時會預設使用這套工具
- tsconfig.json：TypeScript 編譯設定檔
- tslint.json：TSLint 設定檔，為 TypeScript 的格式驗證工具，提高程式碼可讀性、偵測功能性錯誤
- 其他相關配置檔案

### 從建立 Component 開始

透過 Angular CLI 提供的指令，我們能快速建立一個專案架構，其中最常使用 ng generate 來產生 Component、Service、Pipe 等檔案的程式碼。

指令如下：

```
$ ng generate component <name>
```

或簡化成：

```
$ ng g c <name>
```

舉例來說，如果要建立一個叫做 todo-list 的 Component，指令是 `ng g c todo-list`，接著 Angular 就會建立四個檔案，以及更新 app.module.ts：

![](https://i.imgur.com/HDeeSVB.png)

* todo-list.component.html：模板 Template
* todo-list.component.spec.ts：執行 ng test 命令會透過 Karma 進行測試
* todo-list.component.ts：元件 Component
* todo-list.component.css：樣式

## 小結

因為工作需要，而開始接觸 Angular 這套前端框架，卻也了解到這套工具涵括的功能種類眾多！

雖然一時半刻還無法消化，但其實也不是說剛開始就要完全了解，有些概念還是和 React 有相似之處，總之就先跟著官網提供的[練習指南](https://angular.tw/tutorial)，一步一步跟著實作，慢慢踏入 Angular 的世界！

參考資料：
- [Angular 全集中筆記 系列](https://ithelp.ithome.com.tw/users/20109645/ironman/3762)
- [Angular 深入淺出三十天 系列](https://ithelp.ithome.com.tw/users/20090728/ironman/1600)
- [Angular 大師之路 系列](https://ithelp.ithome.com.tw/users/20020617/ironman/1630)
- [前端工程的夢幻逸品：Angular 2 開發框架介紹
](https://blog.miniasp.com/post/2016/07/26/Introduction-to-Angular-2)