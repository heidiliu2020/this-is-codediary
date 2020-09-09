###### tags: `JavaScript` `API`
# [week 8] 傳送資料的方式 & 瀏覽器的限制

> 本篇為 [[FE102] 前端必備：JavaScript](https://lidemy.com/p/fe102-javascript) 這門課程的學習筆記。如有錯誤歡迎指正。

當我們運用 JavaScript 在網頁進行操作時，主要可分為下列三大面向：

1. 介面（Interface）：如何改變介面
2. 事件（Event）：如何監聽事件並做出反應
3. 資料（Data）：如何和伺服器交換資料

> 以下主要探討「改變介面」與「事件監聽」的部分。

## 使用 Node.js 與在瀏覽器上呼叫 API 的差別

### Node.js

- 是直接發出 request 到 Server，再直接收到 Server 的 request
- 中間不會受到任何限制

![](https://i.imgur.com/pOzNqr8.png)

### 瀏覽器上

- 透過瀏覽器發出 request 到 Server，再透過瀏覽器接收 Server 的 response
- 中間可能會受瀏覽器處理影響，必須按照瀏覽器的規則獲取資料

![](https://i.imgur.com/FCaXrNH.png)

## 傳送資料的方式 - 表單 form

```htmlmixed=
    <div class="app">
      <form method="GET" action="https://google.com">
        username：<input name="username"/>
        <input type="submit">
      </form>
    </div>
```

實際上 request 流程（和 JavaScript 無關）：

1. 透過 `GET` 送出 request 給 google 網頁
2. 表單的參數會附加在網址上：`https://google.com/?username=123`，因此登入帳密通常會以 `POST` 指令送出
3. 瀏覽器接收 response（`301`：轉址），因此會換頁

## 傳送資料的方式 - AJAX

Ajax，全名是「Asynchronous JavaScript and XML」，重點是 Asynchronous（非同步）。

- JavaScript：使用的程式語言
- XML：Client 與 Server 的資料交換格式。近年由於 JSON 等格式的流行，使用 AJAX 處理的資料已不限於 XML

Ajax 並非單一技術，而是 JavaScript 以「非同步」方式與伺服器交換資料的統稱。使網頁不須換頁，就能即時更新渲染畫面。

### Ajax 實現非同步請求（Asynchronous Request）

以 Javascript 程式碼作為範例，可使用 Ajax 這項技術得到 Server 端資料：

```javascript=
// 首先建立了一個 XMLHttpRequest 物件
const request = new XMLHttpRequest()

// 當 load 的時候就會執行 function
request.onload = function () {
  // 判斷 request 是否成功
  if (request.status >= 200 && request.status < 400) {
    console.log(request.responseText)
  } else {
    console.log('error')　　// 若失敗．可印出 request.status 得知狀態
  }
}
// 當 request 有錯誤就執行 function
request.onerror = function () {
  console.log('error')
}
// 要發 request 到哪個地方；true 是非同步，false 是同步
request.open('GET', 'https://reqres.in/api/users', true)
// 發出 request
request.send()
```

也可改寫成監聽事件語法，會得到相同結果：

```javascript=
request.onload = function () {
  // 事件反應...
}

<!-- 可改寫成 -->
request.addEventListener('load', function () {
  // 事件反應...
})

```

### 使用 AJAX 的好處

1. 瀏覽不中斷（即不需要換頁）：可提高使用者體驗
2. 只需進行局部內容的資料抽換：降低資料量，提高網頁效率
3. 用 Ajax 和表單傳送資料的差異，在於如何處理「回傳結果」：
- 表單：是以 html 來傳送資料，當瀏覽器接收到 response 後，頁面就會重新刷新。
- Ajax：當瀏覽器接收 response，會轉傳資料至 JavaScript，進行局部內容的資料抽換，不須換頁就能即時更新頁面。


參考資料：[什麼是 Ajax？ 搞懂非同步請求 (Asynchronous request)概念](https://tw.alphacamp.co/blog/ajax-asynchronous-request)

---

## 瀏覽器的限制 - 同源政策與跨網域問題

當我們發出 request 給 Google 網頁時，會出現下列訊息，顯示無法取得 response：

![](https://i.imgur.com/YJCjsgp.png)

這是因為瀏覽器為了安全性而設置的同源政策。一旦脫離瀏覽器，就不會有這些限制。

### Same Origin Policy 同源政策

- 兩個網頁具有相同網域、埠號（如果有）以及主機位置
- 具備相同協定：http 和 https 要分開來看

以瀏覽器來說，預設不同來源會擋掉 response，解決方法如 CORS。

### CORS 跨來源資源共用

- 全名是 Cross-origin Resource Sharing（CORS）
- 由於同源政策的限制，不同來源要跨網域時，就必須使用這項規則

只要 Server 端在 response header 加上 `access-control-allow-origin: *`：

- `*`：代表所有來源均可存取
- 非同源的網域均可以從 Server 存取 response

補充資料：[輕鬆理解 Ajax 與跨來源請求](https://blog.techbridge.cc/2017/05/20/api-ajax-cors-and-jsonp/)

---

### 傳送資料的方式 - JSONP（補充）

> 此方法已經很少使用，僅作為補充。

首先要瞭解下列元素不受同源政策管理：

- `<img>` ：圖片沒有安全性問題
- `<script>`：能夠引入不同源的 JS

#### JSONP

- JSON，全名是 JSON with padding，這裡的 padding 是填充的意思
- 是資料格式JSON的一種「使用模式」，和 CORS 同樣能讓網頁從別的網域要資料

透過 JSONP 資料格式，我們可以先藉由 `<script src = "要 GET 資料的網域名稱"></script>` 讀取網頁的 JS 資訊，再透過指定的 function 進行輸出，就能夠拿到想要的資料。

### 單向傳送資料的應用（補充）

透過在某個元素裡面塞 `src="域名"`，得知使用者是否有打開網頁或其他操作。

- 例如：

```htmlmixed=
<!-- 在圖片塞一個透明的方塊，也不會被使用者察覺 -->
<img width="1" height="1" src="https://user.com/id=134">
```

一旦使用者打開網頁，將透過 src 傳遞訊息到 Server 端，即可得知使用者進到該頁面。可應用在 email 上，確認使用者是否已經打開信件或廣告，得以進行追蹤。

---

## 綜合示範：抓取資料並顯示

以下列程式碼為例：

```javascript=
<head>
  <meta charset="utf-8" />
  <title>FE102 抓取資料並顯示</title>
  <style>
    body {
      font-size: 38px;
    }

    .profile {
      border: 1px solid #333;
      margin-top: 20px;
      display: inline-flex;
      align-items: center;
    }

    .profile__name {
      margin: 0px 10px;
    }
  </style>
</head>

<body>

  <!-- container -->
  <div class="app">
  </div>

  <script>
    // 使用 AJAX 的方法
    const request = new XMLHttpRequest()
    const container = document.querySelector('.app')

    // 當 load 的時候就會執行 function
    // 也可寫成：request.addEventListener('load', function() {...})
    request.onload = function () {
      if (request.status >= 200 && request.status < 400) {
        const response = request.responseText
        const json = JSON.parse(response)
        const users = json.data
        for (let i = 0; i < users.length; i++) {
          const div = document.createElement('div')
          div.classList.add('profile')
          div.innerHTML = `
              <div class="profile__name">${users[i].first_name} ${users[i].last_name}</div>
              <img class="profile__img" src="${users[i].avatar}" />
            `
          container.appendChild(div)
        }
      }
    }
    // request 如果有錯誤就執行
    request.onerror = function () {
      console.log('error')
    }
    // 要發 request 到哪個地方
    request.open('GET', 'https://reqres.in/api/users', true) //true 是要非同步
    // 把 request 傳出去
    request.send()

  </script>
</body>
```

也就是說，利用 AJAX 送出一個 HTTP 請求，必須有三個步驟：

### 1. 建立一個 XMLHttpRequest 物件
- XMLHttpRequest 是一個 JavaScript 準備好的物件，主是要拿來發送 request 與接收 response。
- `const request = new XMLHttpRequest()`

### 2. 開啟一個 url .open()
- `request.open(method, url[, async[, user[, password]]])`
- 參數分別為：Method、URL、同步/非同步、使用者、密碼

### 3. 發起一個請求 .send()
- `request.send()`

完成這三個步驟就完成送出一個 HTTP 請求，但我們還需要透過「監聽載入」來取得資料：`.onload`，當 load 時就會執行該 function。

### 「監聽載入」有兩種寫法

- `目標.onload = function() { … }`
- `目標.addEventListener('load', function() { … })`

### 相關 api 指令

- `.responseText`：拿到資料
- `.status`：拿到 Http status code 狀態碼

參考資料：
1. [[第九週]透過瀏覽器交換資料 — 表單、AJAX、XMLHttpRequest](https://medium.com/@miahsuwork/%E7%AC%AC%E4%B9%9D%E9%80%B1-%E9%80%8F%E9%81%8E%E7%80%8F%E8%A6%BD%E5%99%A8%E4%BA%A4%E6%8F%9B%E8%B3%87%E6%96%99-%E8%A1%A8%E5%96%AE-ajax-xmlhttprequest-fef80856da16)
2. [[第九週]透過瀏覽器交換資料 — 同源政策、CORS、第三種方式 JSONP](https://medium.com/@miahsuwork/%E7%AC%AC%E4%B9%9D%E9%80%B1-%E9%80%8F%E9%81%8E%E7%80%8F%E8%A6%BD%E5%99%A8%E4%BA%A4%E6%8F%9B%E8%B3%87%E6%96%99-%E5%90%8C%E6%BA%90%E6%94%BF%E7%AD%96-cors-%E7%AC%AC%E4%B8%89%E7%A8%AE%E6%96%B9%E5%BC%8F-jsonp-e45603886287)
