###### tags: `網路基礎`
# [week 4] 網路基礎概論 - HTTP 協定、TCP/IP、API

> 本篇為 [[NET101] 網路基礎概論（搭配 JS 實作練習）](https://lidemy.com/p/net101-js) 這門課程的學習筆記。如有錯誤歡迎指正。

```
學習目標：

 知道網路背後大概的運作模式
 知道什麼是 Request 跟 Response
 知道什麼是 DNS 以及運作原理
 知道 HTTP 與 HTTPS 的差異
 知道 localhost 跟 127.0.0.1 是什麼
 知道 GET 與 POST 的差別
 知道常用的 HTTP Header
 知道什麼是 API
 會使用 node.js 寫出串接 API 的程式
 知道 HTTP method 有哪些
 知道基本的 HTTP statud code，像是 200、301、400、404、500
```

---

## HTTP 是什麼？

全名是 HyperText Transfer Protocol，中文翻作「 超文本傳輸協定」。

HTTP 是一套網路傳輸協定，為[全球資訊網的資料通訊的基礎](https://zh.wikipedia.org/zh-tw/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)。也就是說，網頁前端和後端在溝通時，就是透過 HTTP 協定進行。

### 為什麼我們需要 Protocol（協定）？

簡言之，協定就是一個「標準」。協定是為了讓彼此溝通而建立的規範，制定標準以統一格式，如此能夠進行規模化。

## Client & Server

而透過協議溝通的兩端，通常可分為客戶端（Client）和伺服器端（Server）。

![](https://i.imgur.com/EYmEfr8.png)

### 我們如何看到網頁的畫面？

網頁上的資訊，其實就是由許多 request 跟 response 構成。且兩者內容均分成 header 與 body，分別帶著不同資訊：

- header：額外資訊
- body：主要內容

我們可透過瀏覽器的開發者工具、或是 [HTTP 抓包工具 Charles](https://www.itread01.com/content/1547377087.html) 來查看詳細資訊，過程大致如下：

1. 瀏覽器（Client）產生 HTTP「request」傳給 Server
2. Server 經過處理後，回傳一個「response」
3. 再由瀏覽器進行解析（html、css、js 等），將程式碼渲染成我們所熟悉的網頁介面

![瀏覽器](https://i.imgur.com/4pi9zu5.png)

## DNS 域名系統

全名是 Domain Name System，負責將域名轉換成 IP 位置。

- 域名（Domain）：即常用的網址。google.com 就是一個域名，類似景點名稱。
- IP 位置：每個主機都有個 IP 位置，是網路溝通的地址。由四個數字組成，範圍是 0 ~ 255。

> 補充：localhost 跟 127.0.0.1 是什麼？
> - `localhost`：是一個域名，對應的的 IP 位址是 127.0.0.1 
> - `127.0.0.1` 是回送地址，指本地機，一般用來測試使用

### 以查詢地址為例

1. 如果今天想要抵達 台北 101（域名）
2. 查詢 Google 地址在哪（DNS）
3. 回覆正確地址：台北市信義區信義路五段7號（IP）

> [Google 提供免費的 DNS 伺服器](https://zh.wikipedia.org/wiki/Google_Public_DNS) 8.8.8.8 和8.8.4.4，如此可透過搜尋引擎來蒐集大數據。

### 以 GitHub 網站為例

就是透過 DNS 把 Request URL（github.com） 轉換成實際 IP 位置（13.250.177.223）。

![DNS](https://i.imgur.com/LsnJjwB.png)

### `nslookup` 指令

在終端機輸入 `nslookup '網址'`：可查詢 DNS 伺服器。有可能出現多個結果，代表對應到多個 server。

![nslookup](https://i.imgur.com/wTIOWZF.png)

## 瀏覽器只是一個程式

即使我們沒有瀏覽器，也能夠過其他方式發送 request 跟取得 response。 

### 實做一個 Client 端發送 request

利用 Node.js 的一套 library：[request - Simplified HTTP client](https://github.com/request/request)，來模擬瀏覽器發送 request 的過程，步驟如下：

1. 安裝套件：`npm install request`
2. 使用方法：複製官網提供的範本，貼到 index.js 裡

```jsx=
const request = require('request');
request('http://www.google.com', function (error, response, body) {
  console.error('error:', error); // Print the error if one occurred
  console.log('statusCode:', response && response.statusCode); // Print the response status code if a response was received
  console.log('body:', body); // Print the HTML for the Google homepage.
});
```
3. 修改成要發送 request 的域名

```jsx=
const request = require('request');
request('https://github.com/Lidemy/mentor-program-4th-heidiliu2020', function (error, response, body) {
  console.error('error:', error); 
  console.log('statusCode:', response && response.statusCode); 
  console.log('body:', body);
});
```

4. 執行 `node index.js` 可拿到 response

![執行 request](https://i.imgur.com/795sNFV.png)

5. 或是利用 `node index.js > github.html` 導入程式碼，一樣能打開網頁

---

## HTTP Request Method 請求方法

什麼是 `HTTP Method`？在 HTTP 1.1 協定中，定義了八種方法，來以不同方式操作指定的資源。詳細可參考 [HTTP 請求方法- HTTP | MDN](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Methods)：

* GET：只應用於取得資料
* HEAD：只獲取回應的 header，但沒有 body。較少使用，通常應用在只想知道 response 資訊時
* POST：用於提交指定資源的實體。通常會改變伺服器的狀態
* PUT：取代原本的整個 request。和 PATCH 類似
* PATCH：修改部分 request
* DELETE：刪除資源
* OPTIONS：會回傳 server 支援哪些方法
* TRACE
* CONNECT

這是為了讓 Server 能夠清楚辨別 request 的目的。

### CRUD 原則

常用的幾個動作分別為：GET / POST / PUT / DELETE，正好對應到資料庫基本操作 [CRUD 增刪查改](https://zh.wikipedia.org/wiki/%E5%A2%9E%E5%88%AA%E6%9F%A5%E6%94%B9)：

- Create：新增（POST）
- Delete：刪除（DELETE）
- Read：讀取（GET）
- Update：更新（PUT）

### RESTful

- REST：全名是 Representational State Transfer，中文為「表現層狀態轉換」，是一種設計風格，以語意化且更為嚴謹的方式描述 API
- RESTful：形容以此規範設計的 API

一般的 API 可能會長這樣：

```
新增使用者：POST + /new_user
刪除使用者：POST + /delete_user
查詢使用者：GET + /user_data/:id
更改使用者：POST + /update_user
```

若以 RESTful API 風格開發：

```
新增使用者：POST + /users
刪除使用者：DELETE + /users/:id
查詢使用者：GET + /users/:id
更改使用者：PATCH + /users/:id
```

由此可見 RESTful 的寫法較一致，且語意化更好理解。透過把「動作」藏在 HTTP method 裡面，而有唯一的 URL（`/users`）表示資源位置，藉此統一 API 接口。

> 如果用 HTML 來比喻，寫網頁時我們可以全部使用 div 標籤，但為了幫助理解，通常會使用 li、section、article 等具有語意的標籤，即可從結構看出內容的作用。

參考資料：[API 是什麼? RESTful API 又是什麼? - Medium](https://medium.com/itsems-frontend/api-%E6%98%AF%E4%BB%80%E9%BA%BC-restful-api-%E5%8F%88%E6%98%AF%E4%BB%80%E9%BA%BC-a001a85ab638)

### GET & POST 請求的區別

當中又以 `GET` 和 `POST` 兩種，為最常使用的 HTTP 請求方法。兩者最直觀的區別是「資料傳遞方式」與「安全性」。

#### `GET`：向指定的資源要求資料，類似於查詢操作。

- 資料傳遞方式：將引數由 URL 帶至 Server 端
- 安全性：較 POST 不安全，因為傳遞的引數會在URL上顯示
- 例如：讀取連結或圖片

![GET](https://i.imgur.com/C2MUvS4.png)

#### `POST`：將要處理的資料提交給指定的資源，類似於更新操作。

- 資料傳遞方式：將引數放在 request body 中傳遞
- 安全性：較 GET 安全，適合用於隱密性較高的資料
- 例如：會員登入系統（如下圖所示）

![POST](https://i.imgur.com/K7UVu6f.png)

在最下方夾帶 request 資訊：

![request body](https://i.imgur.com/7dixXCe.png)

### HTTP Status Code 狀態碼

是 HTTP 用「3 位數字代碼」來表示回應狀態，通常以開頭的數字來進行區分。詳細可參考 [HTTP狀態碼- 維基百科](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)：

#### 1xx：訊息（較少見）

#### 2xx：Success 成功
- `200 OK`：代表成功
- `204 No Content`：伺服器成功處理了請求，沒有返回任何內容（例如發出 DELETE XXX 訊息，回傳 204 代表刪除成功）

#### 3xx：Redirect 重新導向
- `301 Moved Permanently`：資源「永久」移到新位置
- `302 Found`：資源「暫時」移到其他位置

#### 4xx：User error 客戶端錯誤
- `400 Bad Request`：請求語法錯誤、資源太大、請求訊息無效等
- `404 Not Found`：找不到資源

#### 5xx：Server error 伺服器端錯誤
- `500 Internal Server Error`：伺服器出錯。例如搶票時伺服器當機
- `503 Service Unavailable`：由於臨時的伺服器維護或者過載，伺服器當前無法處理請求

參考資料：

1. [[第四週] 網路基礎 - HTTP、Request、Response - Yakim](https://yakimhsu.com/project/project_w4_Network_http.html)
2. [[Day 09] 表單中的 GET 與 POST](https://ithelp.ithome.com.tw/articles/10192095)
3. [GET和POST請求的區別（面試和原理都得知道）](https://www.itread01.com/content/1543571046.html)
4. [http Post 和 Get 差異](https://medium.com/@totoroLiu/http-post-%E5%92%8C-get-%E5%B7%AE%E7%95%B0-928829d29914)

### 實作一個簡易 HTTP Server 端

前面我們已經實作一個 Client 端，也就是利用 request 這個 library 來顯示出來。接下來我們要利用 Node.js 內建 `library：http` 來實作 Server 端：

1. 建立一個檔案 `server.js`，並輸入下列程式碼：

```js
let http = require('http');     // 引用 library: http 

let server = http.createServer(handleRequest)
function handleRequest(req, res) {
  console.log(req.url)
  res.write('hello')
  res.end()
}

server.listen(5000);    // 監聽 5000 這個 port
```

2. 執行 `node server.js`：不會跑出任何東西。因為沒有 `console.log` 任何東西，所以是正常的。此時程式會一直執行到按 `Crtl + C` 才會退出，符合 server 端必須不斷運行來接收資訊。

3. 在瀏覽器輸入網址 `localhost:5000` 會跑出下列畫面：

![localhost:5000](https://i.imgur.com/PUPfab0.png)

4. 若修改程式碼如下，即可根據「不同網址」給出「不同回應」：

```js
let http = require('http');     // 引用 library: http 

let server = http.createServer(handleRequest)
function handleRequest(req, res) {
  console.log(req.url)
  if (req.url === '/') {        // 若網址為根目錄
    res.write('welcome!') 
    res.end()
    return 
  }
  if (req.url === '/hello') {    // 若網址為 /hello
    res.write('hello')
    res.end()
    return
  }

  if (req.url === 'redirect') {

  }
}

server.listen(5000);    // 監聽 5000 這個 port
```

- 輸入網址 `localhost:5000`：回應 welcome

![welcome](https://i.imgur.com/p1PKLHk.png)

- 輸入網址 `localhost:5000/hello`：回應 hello

![hello](https://i.imgur.com/Ay4ut32.png)

5. 再增加 `redirect` 部分和 `res.writeHead(404)`，就完成一個較完整的 Server 端：

```js
let http = require('http');     // 引用 library: http 

let server = http.createServer(handleRequest)
function handleRequest(req, res) {
  console.log(req.url)
  if (req.url === '/') {
    res.write('welcome!') 
    res.end()
    return 
  }
  if (req.url === '/hello') {
    res.write('hello')
    res.end()
    return
  }

  if (req.url === '/redirect') {
    res.writeHead(200, {
      'lidemy': 'good'
    })
    res.end()
    return
  }

  res.writeHead(404)
  res.end()
}

server.listen(5000);    // 監聽 5000 這個 port
```
- 若輸入不存在的網址，就會出現狀態 `404 Not Found`：

![404](https://i.imgur.com/Dy8kyCR.png)

- 若輸入 `localhost:5000/redirect`，會在 Response Headers 會出現 `'lidemy': 'good'`：

!['lidemy': 'good'](https://i.imgur.com/KufqrXq.png)

6. 在之前的狀態碼有提到，若要轉址必須改成 `302 Found`：

```js
  if (req.url === '/redirect') {
    res.writeHead(302, {
      'Location': '/hello'       // 指定要轉到哪裡
    })
    res.end()
    return
  }
```

7. 輸入網址 `'localhost:5000/redirect`，瀏覽器就會轉址到新位置：

![302](https://i.imgur.com/WX6bq3s.png)

- 若將程式碼改成 `'Location': 'https://google.com'`：輸入網址 `localhost:5000/redirect` 就會直接連到 `google.com`

---

## 淺談 TCP/IP

### 網路的層級

先前提到「從電腦發出 request 到 server」，在溝通訊息這段過程，其實需要經過非常多道手續。因此就有組織將網路標準化，將網路連接過程分成數個階層（layer），每個階層都有特別的獨立的功能。

分層的好處，是只要處理該層級的事情就好，因為每個階層的程式碼可以獨立撰寫，功能也不會相互干擾。

而著名的分層模型主要有兩種：

### OSI 七層協定

由於協定非常嚴謹，較偏向理論。

![OSI](https://i.imgur.com/wLsG4hH.png)

### TCP/IP 四層模型

TCP/IP 是由 OSI 七層協定簡化而來，為目前網路通訊的基礎架構。以下為兩者之間的比較圖與常見的通訊協定：

![OSI TCP/IP](https://i.imgur.com/GFDpM8z.png)

參考資料：[2.4 TCP/IP 的傳輸層相關封包與資料 - 鳥哥的Linux 私房菜](http://linux.vbird.org/linux_server/0110network_basic.php#whatisnetwork_tcpip)

---

## 網路層 - IP 地址

IP 的全名是 Internet Protocol，中文是「網際網路協定」。

可分為 IPv4、IPv6 這兩種協議，兩者最主要差異在於「IP 地址的不同」：

- IPv4：由四個數字組成，範圍是 0~255
- IPv6：用來取代 IPv4，主要是為了解決 IP 地址不夠用的問題，可容納更多 IP 地址

### 各種 IP

我們常聽到的 IP 地址，代表在網路上的地址。在網際網路上，每台電腦之間，是透過 IP 位址來通訊。當中又分為下列幾種類型：

- 固定 IP：不會改變的 IP

理想情況下，是每一台電腦都有一個 IP 位址。固定的 IP 位址，適合架設網站，也因為不會變動，確保使用者能夠連上伺服器。例如：伺服主機、網路設備多使用固定 IP。

- 浮動 IP：每次連上網路時的 IP 位置都會不一樣

一般用戶大多是浮動 IP。浮動 IP 解決了「IP 不夠用」這個問題，因為每次連線的 IP 位址皆不同，也不會輕易被駭客攻擊。

- 虛擬 IP：僅能使用於內部網路，外網連不上該位置

只有在內網才能互相連接，外網找不到該地址，所以內網 IP 位址是可以重複的。但對外網而言，一定會有一個「固定 or 浮動」的 IP 位址。例如：公司使用內網溝通，藉由鎖 IP 位址，可提高安全性。

> 通常以 `192.168` 或 `10.0` 開頭的，都是虛擬 IP。

![虛擬 IP](https://i.imgur.com/TFcsKyY.png)

參考資料：[浮動 IP 與 固定 IP 有何不同?? 各有何優缺點??](https://ithelp.ithome.com.tw/questions/10000796)

---

## Port 連接埠（端口）

在應用層當中，每台電腦主機 IP 位置（localhost），可能對應到不同應用程式。例如：HTTP、FTP、信件收發等服務。

而 Port 扮演網路通訊的端點，用來區分不同功能。如此即可辨認出，該連線要對應到哪個應用程式。

- 例如實作 server 端提過的 `server.listen(5000)`：代表監聽 `5000` 這個 Port，輸入網址 `localhost:5000` 即可連到這個服務

如果沒有輸入 Port，會有一些預設或常用值：

- HTTP 80：說明用於網頁瀏覽
- HTTPS 443
- FTP 21
- 「測試」常用：3000、4000、8080、8000 等冷門 Port

參考資料：[[網路管理]常用 port 說明](https://dotblogs.com.tw/box5068/2011/01/19/20901)

---

## 傳輸層 - TCP & UDP

傳輸層（Transport Layer）協定提供不同主機之間的資料傳輸及控制。根據不同需求，又分為「可靠的 TCP」和「速度較快的 UDP」兩種協定：

### TCP

- 全名：Transmission Control Protocol 傳輸控制協定
- 是一種可靠的資料傳輸，因此大部分的網路協定都是建立在 TCP 上面
- 透過「三次握手」確認建立一個連接：

![](https://i.imgur.com/PHSLrDe.png)

```
若以「傳紙條概念」比喻三次握手：
小明：安安，在嗎？
小美：在阿，你好。
小明：收到，太好了！
```

### UDP

- 全名：User Datagram Protocol 用戶資料包協定
- 流量是不受規範的，需要快速、重複傳送資料的情況會使用 UDP，相對而言也較不穩定
- 例如：視訊功能

參考資料：
1. [TCP協議 - Wikipedia](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE)
2. [什麼是OSI的7層架構？和常聽到的Layer 7有關？](https://ithelp.ithome.com.tw/articles/10000021)

## 小結 - 從傳紙條概念看 TCP/IP

參考 [Huli - 從傳紙條輕鬆學習基本網路概念](https://medium.com/@hulitw/learning-tcp-ip-http-via-sending-letter-5d3299203660)，我們能夠透過傳紙條的例子，來試著理解網路通訊概念：

- 應用層（HTTP / FTP）：傳輸的資料內容
- 傳輸層（TCP / UDP）：傳輸方式
- 網路層（IP）：傳輸地址
- 實體層（網路電纜）：實體傳輸

![傳紙條概念](https://i.imgur.com/oJPxLiH.png)

---

## 淺談 API

> 可參考 [Huli - 從拉麵店的販賣機理解什麼是 API](https://medium.com/@hulitw/ramen-and-api-6238437dc544)，或是這部影片：[什麼是 API？](https://www.youtube.com/watch?v=zvKadd9Cflc&feature=emb_logo)來瞭解。

全名是 Application Programming ==Interface==，中文是「應用程式==介面==」。簡言之，就是方便溝通、交換資料的管道。

API 是應用程式、裝置之間資料的交換，但不一定要透過網路才能有 API，例如：

- 軟硬體廠商的 API：USB 與 電腦交換資料
- 作業系統裡的 API：讀取、傳輸及寫入等等電腦上的操作

### 而對於網頁來說就是 Web API

- Web API：是基於 http 協定下運作的 API，代表透過網路進行資料交換

以下為實際的運用例子：

- 會員登入系統：社群連結註冊登入（例如：設置 FB 或 Google 登入按鈕）
- 社群嵌入：分享、留言版、按讚按鈕、影音（例如：[Facebook Graphic](https://developers.facebook.com/docs/graph-api?locale=zh_TW)）
- 資料嵌入：[Yahoo 氣象](https://developer.yahoo.com/weather/?guccounter=1&guce_referrer=aHR0cHM6Ly95YWtpbWhzdS5jb20vcHJvamVjdC9wcm9qZWN0X3c0X05ldHdvcmtfQVBJLmh0bWw&guce_referrer_sig=AQAAABbKqoTfIecNtugMpoKKf5L53XwIJA_l1zPrrJUmPD2Yh1N-rcXbaJhbnPQAKW-UbnT_NJhQX_4isY374OTvbtR1q91DdGlYOK4qSQyQqr2bkr5XMPQuo8bQT874RICV6IF2sHsr2yp84UXMV4549R1aXFjHNt9lifII3YdTvbAY)、[Google 地圖](https://developers.google.com/maps/documentation/timezone/start?hl=zh-tw)、[Tweet timelines](https://developer.twitter.com/en/docs/tweets/timelines/overview)
- [PokéAPI](https://pokeapi.co/)：拿到寶可夢的資訊
- [假圖 API](https://picsum.photos/)：隨機產生出圖片

透過 API 能夠達到省時、便利、營利等優點，因此我們也可以說，API 是一個「能讓生產者與消費者雙方溝通的介面」。

## 實際串接 API

### 串接的前置作業

1. 準備 Client 端（發出端）：使用 node.js [library request](https://github.com/request/request) 實作一個 Client 端
2. 提供 API 的網站（接收端）：[Regres](https://reqres.in/) 是一個提供 API 測試的網站
3. 能夠發出 request 的工具：終端機

### 實戰演練

### Step1. 實作一個 Client 端

與先前「實做一個 Client 端發送 request」操作方式相同，是利用 [library request](https://github.com/request/request) 來模擬瀏覽器發送 request 的過程。

- 新增 index.js 並貼上官網提供的[範本](https://github.com/request/request#super-simple-to-use)：

```js 
const request = require('request');
request('http://www.google.com', function (error, response, body) {
   console.error('error:', error);
   console.log('statusCode:', response && response.statusCode);
   console.log('head:', response.headers);
   console.log('body:', body);
});
```

### Step2. 串接 [Regres](https://reqres.in/api/users) API

將範本中的 google 網址改成 `https://reqres.in/api/users`

### Step3. 用 node.js 運行

在終端機輸入 `node index.js`，即可獲得 Regres API 所提供的資訊：

![運行](https://i.imgur.com/DWirKxl.png)

這樣其實就完成了簡單的 API 串接！透過丟一個 request 到網站，我們能夠獲取想要的資訊。

## 學會查看官方文件

而透過 [request 文件](https://github.com/request/request#forms)，我們能找到不同的 `request.METHOD()`，瞭解如何使用各種方法：

```js
request.get(): Defaults to method: "GET".
request.post(): Defaults to method: "POST".
request.put(): Defaults to method: "PUT".
request.patch(): Defaults to method: "PATCH".
request.del() / request.delete(): Defaults to method: "DELETE".
```

### 情境一：查詢不同使用者資料

- Method：GET（預設方法）
- url ：`https://reqres.in/api/users/<userID>`
- 透過 [Reqres 官網](https://reqres.in/)可知，更改 「Request 網址結尾」可獲得不同使用者資訊，以 `/api/users/2` 為例：

方法一：直接將網址改成 `https://reqres.in/api/users/2`

![id 2](https://i.imgur.com/JiCCL5b.png)

方法二：使用 node.js 內建 library `process`

這個方法是利用 `process.argv` 達成帶入參數，我們可以先用 `console.log` 來查看 `process.argv` 是什麼：

```js
const process = require("process");
console.log(process.argv);
```

![array](https://i.imgur.com/HrpOUWq.png)

會發現 `process.argv` 其實是一個陣列，利用 `process.argv[2]` 就可以拿到我們需要的參數：

```js
const request = require('request');
const process = require('process');

request(
  'https://reqres.in/api/users/' + process.argv[2],
  function (error, response, body) {
    console.log('body:', body);
  }
);
```

輸入 `node index.js 2`，就可獲得 `id: 2` 的使用者資料：

![node index.js 2](https://i.imgur.com/dRHS9rO.png)

### 情境二：新增使用者資料

- method：POST
- url ：`https://reqres.in/api/users/`
- 到 request library 的 Github 頁面，[點選 forms](https://github.com/request/request#forms)，填入相關程式碼來新增資料至 reqres

```js
const request = require('request');

request.post(
  { 
    url: 'https://reqres.in/api/users',
     form: { 
       name: 'Heidi',
       job: 'web engineer'
      } 
  },
  function (error, response, body) {
    const json = JSON.parse(body)      // 轉成 JS 物件
    console.log(json);
  }
);
```

成功新增使用者資料：

> 註：這裡的操作只是測試用，並不會真的新增資料到網站。

![新增使用者資料](https://i.imgur.com/SOHZu0h.png)

### 情境三：刪除使用者資料

- method：DELETE
- url ：`https://reqres.in/api/users/<userID>`

```js
const request = require('request');
request.delete(
  {
    url: `https://reqres.in/api/users/2`,   // 將使用者 id 帶入
  },
  function (error, response, body) {
    // console.log(JSON.parse(body));
    // 因為是刪除，所以不會回傳東西，因此轉印狀態碼
    console.log('status code:', response.statusCode)
  }
);
```

回傳 `204`，代表成功刪除使用者資料：

![delete](https://i.imgur.com/JHOyAUo.png)

### 情境四：修改使用者資料

- method ：PATCH
- url ：`https://reqres.in/api/users/<userID>`

```js
const request = require('request');
request.patch(
  {
    url: `https://reqres.in/api/users/2`,
    form: {        // 傳入要修改的資料
      name: 'hello'
    }
  },
  function (error, response, body) {
    console.log(JSON.parse(body))
  }
);
```

成功修改使用者資料：

![patch](https://i.imgur.com/HXbVmTk.png)

### 綜合應用

Base URL: https://lidemy-book-store.herokuapp.com

| 說明     | Method | path       | 參數                   | 範例             |
|--------|--------|------------|----------------------|----------------|
|獲取所有書籍| GET  | /restaurants  | _limit:限制回傳資料數量 | /restaurant?_limit=6
|獲取單一書籍| GET  | /restaurants/:id | 無            | /restaurant/12
|刪除書籍    | DELETE | /restaurants/:id | 無          | 無
|新增書籍    | POST | /restaurants  | name: 書籍名稱       | 無
|更改書籍    | PATCH| /restaurants/:id | name: 書籍名稱    | 無

```js
const request = require('request');

const API_ENDPOINT = 'https://lidemy-book-store.herokuapp.com';

request(`${API_ENDPOINT}/books?_limit=10`, (error, response, body) => {
  // 判斷 status code 是否為 2 開頭：偵測回傳是否成功
  if (response.statusCode >= 200 && response.statusCode < 300) {
    let books = '';
    // try catch：偵測處理資料這個動作是否出現錯誤
    try {
      books = JSON.parse(body);
      // 列出前十筆資料
      for (let i = 0; i < books.length; i += 1) {
        console.log(`${books[i].id} ${books[i].name}`);
      }
    } catch (err) {
      // 若 response 不是一個合法的 JSON 字串，會回傳錯誤
      return console.log(err);
    }
  }
});
```

### 補充：如何在請求帶入資料

在發出 request 時，我們其實能透過兩種方式來帶入資料：

1. Request header：把資料放入 header
   - 通常會放層級較高、具有機密性的資料，例如身分驗證
2. Query-string parameter：把資訊放在網址列結尾
   - 由結尾的問號開始，是 KEY / Value 的組合

實際例子可參考[Twitch API v5](https://dev.twitch.tv/docs/v5) 文件，同樣支援這兩種方法：
1. Request header (`Client-ID: XXXXX`)
2. Query-string parameter
(`https://api.twitch.tv/kraken/users/44322889?client_id=XXXXX`)

因此在串接之前，必須先確認該 API 支援哪種方式來發出請求。

---

## 資料格式 XML & JSON

在 API 實戰中的 response 資料其實就是「JSON 格式字串」。在談到如何整理資訊之前，先來介紹常用的資料格式：

### XML

- 全名為 Extensible Markup Language，中文為「可延伸標記式語言」
- 和 HTML 非常類似，均屬於 Markup Language（標記語言），內容用`前後標籤`包起來。
  - 例如：`<firstName>John</firstName>`
- 缺點是檔案較大、不易閱讀，因此現代開發較少使用

### JSON

- 全名為 JavaScript Object Notation，中文為「JavaScript 物件表示法」
- 為現代最普遍、常用的資料格式
- 格式容易理解，且相容性高，許多程式均支援讀取或修改
- 資料格式看似 JavaScript 物件，但需注意以下幾點：
  1. 回傳值的型態是「字串」
  2. `key` 值要用雙引號 `"key"` 包起來
  3. 支援許多資料格式：`[array]`、`{object}` 等；但 `value 值` 不能放 function
  4. 整個 JSON 格式字串不能使用註解
- 相關函式
  1. `JSON.prase(<JSON>)`：將 JSON 格式字串轉成物件
  2. `JSON.stringify(<object>)`：將物件轉成 JSON 格式字串

參考資料：
1. [你不可不知的 JSON 基本介紹](https://blog.wu-boy.com/2011/04/%E4%BD%A0%E4%B8%8D%E5%8F%AF%E4%B8%8D%E7%9F%A5%E7%9A%84-json-%E5%9F%BA%E6%9C%AC%E4%BB%8B%E7%B4%B9/)
2. [JSON Editor online](https://jsoneditoronline.org/)：可將得到的資料轉成 JSON 格式

### 實際來處理 JSON 格式字串

同樣以串接 Reqres API 為例。得到 response 就是「JSON 格式的字串」，但這種資料並不易閱讀，也無法直接使用。

因此使用內建函式 `JSON.parse()` 做處理，就會轉成「JS 物件」：

```js
const request = require('request');

request(
  `https://reqres.in/api/users/2`,
  function (error, response, body) {
    console.log('原始格式，JSON 格式的字串----------');
    console.log(body);
    console.log('轉成 JS 物件--------------------');
    console.log(JSON.parse(body));
  }
);
```

![json](https://i.imgur.com/rxiOXN2.png)

如此就可以「物件」方式來取出想要的資料：

```js
const request = require('request');

request(
  `https://reqres.in/api/users/2`,
  function (error, response, body) {
    const json = JSON.parse(body)         // JSON 物件
    console.log(json.data.first_name);    // 印出 Janet
  }
);
```

反之，若想把「JS 物件」轉成「JSON 格式字串」，可以用： `JSON.stringify(<object>)`

---

## 必學指令 `curl`

> 在 [curl 官網](https://curl.haxx.se/)下載，安裝方法可參考 [Day14 - cURL 工具](https://ithelp.ithome.com.tw/articles/10185923)，即可在終端機使用。

curl 做法非常簡單，只要在終端機輸入指令就可支援發 HTTP request 、下載及上傳檔案的功能，基本格式為：

```js
curl [options] [URL...]
```

### 預設是 `GET` 方法

以使用 curl 能夠發 request　到 google 首頁為例：

- 輸入 `crul 'http://www.google.com'`：即可下載該網頁程式碼
- 或輸入 `crul '網址' > google.html`：可將回傳值導向其他檔案

![](https://i.imgur.com/RbyGVvV.png)

參考資料：
1. [linux指令 curl指令详解](https://blog.51cto.com/doiido/1564631)
2. [Linux Curl Command 指令與基本操作入門教學](https://blog.techbridge.cc/2019/02/01/linux-curl-command-tutorial/)

---

