###### tags: `React` 
# [week 21] React：用 SPA 架構實作一個部落格（三）- 淺談測試

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

## 淺談測試

這篇會稍微談談有關測試的東西，測試在現今已漸漸成為重要的議題，主要目的就是為了避免產品出現 Bug，藉此提高使用者體驗。

### 測試的種類

測試簡單來說可分成以下三種：

- 單元測試 Unit testing 
  - 以程式碼的最小單位進行測試
- 整合測試 Integration testing 
  - 對不同模組之間的交互作用進行測試
- 端對端測試 End-to-end testing 或 E2E testing 
  -  從使用者角度出發，對真實系統進行測試
  -  主要為「人工測試」

![](https://i.imgur.com/ybx5NbQ.png)

（參考資料：[一次搞懂單元測試、整合測試、端對端測試之間的差異](https://blog.miniasp.com/post/2019/02/18/Unit-testing-Integration-testing-e2e-testing)）

## React Testing Library：測試 React Component

如果要測試 React Component，可使用 [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) 套件，這在安裝 React 時就有內建。

其原理是把 React 的 Component 給 render 出來，並非真的 render 在瀏覽器上，而是透過 JS DOM 模擬成 JS 來執行，即可針對 render 出來的畫面進行各種測試。

在我們建立專案時，就有個 App.test.js 檔案可用來寫測試內容：

![](https://i.imgur.com/4dcIFV1.png)

每個測試會用一個 function 包住：

```javascript=
import { render, screen } from '@testing-library/react';
import App from './App';

test('renders learn react link', () => {
  // render component
  render(<App />);
  // 確認是否有出現指定文字
  const linkElement = screen.getByText(/learn react/i);
  // 確認是否有在 document 裡面
  expect(linkElement).toBeInTheDocument();
});
```

在終端機輸入指令即可執行測試：

```
$ npm run test
```

測試預設會找 App.test.js 在上次 commit 時有更動的部分，例如輸入 a 可跑所有測試結果：

![](https://i.imgur.com/Hao9KG7.png)

由於 App.test.js 是 watch 模式，只要檔案有變動就會自動跑測試，結果會長這樣：

![](https://i.imgur.com/EA3y0KO.png)

### 實際應用

但實際在測試時並不會真的去 call API，因為 mock API 代表要做什麼事，而我們在意的點其實是：

- 會不會發 request 去 back-end
- 拿到 API response 之後，會不會顯示想要的資料

也就是說，測試其實不需要知道 back-end 回覆的內容，而是透過 mock API Point，就可以在打 API 之前回傳一個結構，藉此來測試顯示的資料。

例如以下範例，藉由 mock API 來模擬每次 fetch 會回傳的資料，再使用 await waitfor() 非同步處理進行測試：

![](https://i.imgur.com/OX9LJ4X.png)

這種測試方法其實就類似於 Unit testing 或 Integration testing，再來要介紹的是用於 End-to-end testing 的程式。

## Cypress：JS E2E testing

End-to-end testing 的特點在於會真的跑一個 Browser 起來，並將手動測試轉變成程式碼執行，因此會更接近使用者實際在使用網站的感受。

我們要介紹的是 [cypress](https://www.cypress.io/) 這套 E2E testing 程式，在測試時也有提供錄影功能，能夠記錄錯誤截圖等等。

### 安裝程式

```
$ npm install cypress
```

### 如何執行

```
$ npx cypress open
```

或是在 package.json 的 scripts 新增 cypress open 指令，即可使用 `npm run cypress:open` 指令執行：

![](https://i.imgur.com/GNMrji1.png)

第一次執行 cypress open 時，會自動在 my-app 資料夾建立 cypress 相關程式，像是 integration 裡的內建測試等等。

![](https://i.imgur.com/GPisKnv.png)

執行後會自動開啟 GUI 介面：

![](https://i.imgur.com/deKsjBl.png)

預設的測試檔名為 `spec.js`，例如我們可在 integration 建立一個 home.spec.js，並在 my-app 底下的 cypress.json 新增 baseUrl：

```json=
{
  "baseUrl": "http://localhost:3000/react-board-test"
}
```

然後在 home.spec.js 寫入[官網範例](https://docs.cypress.io/guides/getting-started/testing-your-app.html#Step-2-Visit-your-server)：

```javascript=
describe("The Home Page", () => {
  it("successfully loads", () => {
  // 改成連線到首頁
    cy.visit("/");
  });
});

```

點選 cypress 介面中的 home.spec.js 執行：

![](https://i.imgur.com/20LvIQ2.png)

此時會自動開啟 Chrome 瀏覽器跑測試，結果如下：

![](https://i.imgur.com/L5NO3Ag.png)

### 透過 route 來 mock API

但是和先前的例子一樣，我們通常不會真的去 call API，而是會 mock API，在 cypress 可透過 route 來模擬 API，可參考[官方文件](https://docs.cypress.io/api/commands/route.html#Syntax)。

首先要修改 cypress.json 設定檔，加上 `experimentalFetchPolyfill` 參數：

```json=
{
  "baseUrl": "http://localhost:3000/react-board-test",
  "experimentalFetchPolyfill": true
}
```

改寫 home.spec.js 範例如下，這樣就是一個簡單的測試：

```javascript=
describe("The Home Page", () => {
  it("successfully loads", () => {
    cy.server();
    // mock API
    cy.route(
      "https://student-json-api.lidemy.me/posts?_sort=createdAt&_order=desc",
      [
        {
          id: 1,
          title: "Hello World!",
          createdAt: 20202020,
        },
      ]
    );
    // 導向首頁
    cy.visit("/");
    // 測試: 確認頁面是否包含該字串
    cy.contains("Hello World!");
  });
});
```

測試結果如下：

![](https://i.imgur.com/TdVmUf5.png)

查看 Request URL 會發現網址有被 cypress 換成 mock API，藉此來 mock 回傳結果：

![](https://i.imgur.com/xYYEpka.png)

- 參考文章：[[Cypress 1] E2E Testing 初探](https://medium.com/hannah-lin/cypress-e2e-testing-%E5%88%9D%E6%8E%A2-a10eca3c0cf7)

## React Testing Library vs Cypress

以上介紹的兩種測試，差別在於：
- React Testing Library
  - 類似於 Unit testing 或 Integration testing
  - 利用 JavaScript 模擬環境
- Cypress
  - E2E testing
  - 實際在瀏覽器載入頁面，透過 mock API 的方式測試行為