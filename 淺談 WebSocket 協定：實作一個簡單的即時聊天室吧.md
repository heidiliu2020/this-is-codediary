###### tags: `網路基礎` `JavaScript` `WebSocket`
# 淺談 WebSocket 協定：實作一個簡單的即時聊天室吧！

[TOC]

![](https://i.imgur.com/xLBMhye.png)
> 參考資料：https://leesonhsu.blogspot.com/2018/07/socketwebsocketsocketio.html

## 1.1 What is WebSocket?

[WebSocket](https://zh.wikipedia.org/zh-tw/WebSocket) 是 [HTML5](https://zh.wikipedia.org/zh-tw/HTML5) 提供的一種網路傳輸協定，是瀏覽器（Client）與伺服器（Server）交換資料的方式之一。

與我們較為熟知的 [HTTP](https://zh.wikipedia.org/zh-tw/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE) 或 [HTTPS](https://zh.wikipedia.org/zh-tw/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8%E5%8D%8F%E8%AE%AE) 協定，同樣位於 [OSI 模型](https://zh.wikipedia.org/zh-tw/OSI%E6%A8%A1%E5%9E%8B)的[應用層](https://zh.wikipedia.org/zh-tw/%E5%BA%94%E7%94%A8%E5%B1%82)，且基於傳輸層的 [TCP](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE) 協定。其最大不同在於，WebSocket 協定只需連線一次，就能保持雙向溝通，不需重複發送 Request，因此回應更即時，進而提升 Web 通訊速度。

## 1.2 Why WebSocket?

- HTTP 協定只能由 Client 端「單向」發送請求，無法由 Server 端主動發送請求；WebSocket 協定則允許 Server 端主動向 Client 端推播資料，實現「雙向溝通」機制
- 透過 [HTTP 1.1 持久連接（keep-alive）](https://zh.wikipedia.org/wiki/HTTP%E6%8C%81%E4%B9%85%E8%BF%9E%E6%8E%A5)，有效解決 HTTP [輪詢（Polling）](https://zh.wikipedia.org/zh-tw/%E8%BC%AA%E8%A9%A2)產生的效能問題，節省伺服器資源
- 實際範例：訊息推播、即時聊天室、共同編輯等功能

![](https://i.imgur.com/S3Mhxau.png)

## 1.3 How to use WebSocket?

WebSocket 的建立是由瀏覽器透過 JavaScript 指令，發起一個 HTTP Request 來實現。請求網址會長這樣：

```jsx
ws://example.com/wsapi
```

若經過 SSL 加密（WebSocket Secure），則加密連接網址會變成：

```jsx
wss://secure.example.com/wsapi
```

WebSocket 進行一次雙方握手過程，Client 端發送資訊：

```jsx
GET ws://localhost:8080 HTTP/1.1             // 透過 HTTP/1.1 進行交握
Origin: http://localhost:8080                // Client 端 URL
Host: localhost:8080
Upgrade: websocket                           // 表示發送 WebSocket 類型請求
Connection: Upgrade                          // 表示 Client 希望連接升級
Sec-WebSocket-Key: xqBt3ImNzJbYqRINxEFlkg==  // base64 編碼密文，瀏覽器在每次握手隨機生成
Sec-WebSocket-Protocol: chat, superchat      // 用來區分同 URL 下不同 Server 需要之協議
Sec-WebSocket-Version: 13                    // 支持的 WebSocket 版本號
```

Server 端回應資訊：

```jsx
HTTP/1.1 101  Switching Protocols    // 表示成功建立連接
Upgrade: websocket     
Connection: Upgrade     
Sec-WebSocket-Accept: K7DJLdLooIwIG/MOpvWFB3y3FE8= // Server 端回覆對應的加密
Sec-WebSocket-Protocol: chat         // Server 端使用的協議
Sec-WebSocket-Location: ws://localhost:8080
```

可參考 MDN 上提供的 [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) 使用範例，建立簡單的 WebSocket 連線。

接下來會分幾個步驟來實作 WebSocket，其中又分 Server 和 Client 兩個面向進行：

- Step1. 建置 WebSocket Server
- Step2. 從 Client 發送請求給 WebSocket Server 建立連線
- Step3. Server 處理來自 Client 的訊息與回應
- Step4. Client 處理來自 Server 的訊息與回應
- Step5. 實戰練習：實現 Server 與多個 Client 連線溝通

那麼就開始吧！

### 1.3.1 Server 端：建置 WebSocket 環境

這裡以 Node.js 為例，使用 [Express](https://expressjs.com/) 框架搭配 WebSocket 套件來建立環境。

關於如何使用 Express 來架設 Server，可參考過去寫的筆記：**[[week 17] 後端中階 - 使用 Node.js + Express 框架建立一個靜態網頁](https://heidiliu2020.github.io/express/)。**

- 首先是安裝套件

```jsx
$ npm install express
$ npm install ws
```

- 安裝完套件後，在專案中建立 `server.js` 檔案，作為專案的進入點：

```jsx
// import library
const express = require('express')
const ServerSocket = require('ws').Server   // 引用 Server

// 指定一個 port
const PORT = 8080

// 建立 express 物件並用來監聽 8080 port
const server = express()
    .listen(PORT, () => console.log(`[Server] Listening on https://localhost:${PORT}`))

// 建立實體，透過 ServerSocket 開啟 WebSocket 的服務
const wss = new ServerSocket({ server })

// Connection opened
wss.on('connection', ws => {
    console.log('[Client connected]')

    // Connection closed
    ws.on('close', () => {
        console.log('Close connected')
    })
})
```

即可輸入下方指令來運行 Server：

```jsx
$ node server.js
```

出現以下 log 代表 Server 正在監聽 8080 port：

![](https://i.imgur.com/mwIvN1w.png)

### 1.3.2 Client 端：與 WebSocket Server 連線

處理完 Server 後，接下來要從 Client 端連結 WebSocket 服務。這裡需另起一個專案，包含 index.html 和 index.js 兩個檔案。

- UI 畫面：新增一個 index.html 檔案

```html
<html>
    <head>
    </head>
    <body>
        <!-- Connect or Disconnect WebSocket Server -->
        <button id="connect">Connect</button>
        <button id="disconnect">Disconnect</button>

        <!-- Send Message to Server -->
        <div>
            Message: <input type="text" id="sendMsg" ><button id="sendBtn">Send</button>
        </div>

        <!-- Import index.js after UI rendered -->
        <script src='./index.js'></script>
    </body>
</html>
```

渲染後如下圖所示：

![](https://i.imgur.com/0zhHOVC.png)

- 邏輯處理：新增一個 index.js 檔案

```jsx
var ws

// 監聽 click 事件
document.querySelector('#connect')?.addEventListener('click', (e) => {
    console.log('[click connect]')
    connect()
})

document.querySelector('#disconnect')?.addEventListener('click', (e) => {
    console.log('[click disconnect]')
    disconnect()
})

document.querySelector('#sendBtn')?.addEventListener('click', (e) => {
    const msg = document.querySelector('#sendMsg')
    sendMessage(msg?.value)
})

function connect() { 
    // Create WebSocket connection
    ws = new WebSocket('ws://localhost:8080') 
    // 在開啟連線時執行
    ws.onopen = () => console.log('[open connection]')
}

function disconnect() {
    ws.close()
    // 在關閉連線時執行
    ws.onclose = () => console.log('[close connection]')
}
```

- 可搭配 https://github.com/indexzero/http-server 套件快速架設 [localhost](http://localhost) 測試環境：

```jsx
// 安裝套件
$ npm install http-server
// 運行 http-server
$ http-server [path] [options]

// 預設為 8080 port，這裡指定 3000 port 為例
$ http-server -p 3000
```

實際運行如下方所示 ：

![](https://i.imgur.com/SYUbMM9.gif)

建立好 WebSocket 連結之後，就可以進行雙向溝通了！

### 1.3.3 Server 端：處理接發送 message

Server 端分別能使用 `send` 發送訊息，以及透過監聽 `message` 事件接收來自 Client 的訊息：

```jsx
// Connection opened
wss.on('connection', ws => {
    console.log('[Client connected]')

	// Listen for messages from client
	ws.on('message', data => {
	    console.log('[Message from client]: ', data)
	    // Send message to client
	    ws.send('[Get message from server]')
	})

	// ...
})
```

### 1.3.4 Client 端：處理接發送 message

同樣的，Client 端也能使用 `send` 送出訊息，以及透過 `onmessage` 接收 Server 端的訊息：

```jsx
// 監聽 click 事件
document.querySelector('#sendBtn')?.addEventListener('click', (e) => {
    const msg = document.querySelector('#sendMsg')
    sendMessage(msg?.value)
})

// Listen for messages from Server
function sendMessage(msg) {
    // Send messages to Server
    ws.send(msg)
    // Listen for messages from Server
    ws.onmessage = event => console.log('[send message]', event)
}
```

## 1.4 實戰練習：實作一個簡單的即時聊天室

先前提到 WebSocket 常應用於即時聊天室等功能，也就是實現 Server 同時與多個 Client 連線。那該如何在 ClientA 傳送訊息給 Server 的同時，讓 ClientB 也接收到來自 Server 回傳的訊息呢？

這時就要仰賴「廣播功能」，首先透過 `ws` 提供的方法 `clients` 取得目前所有連線中的 Clients 資訊，再使用 forEach 迴圈送出訊息給每個 Client：

```jsx
// Connection opened
wss.on('connection', ws => {
  console.log('[Client connected]')

	// Listen for messages from client
	ws.on('message', data => {
	    console.log('[Message from client]: ', data)
	    // Get clients who connected
	    let clients = wss.clients
	    // Use loop for sending messages to each client
	    clients.forEach(client => {
	        client.send('[Broadcast][Get message from server]')
	    })
	})

	// ...
})
```

至於如何區分各個 Client，可參考這篇提供的方法：[《webSocketServer node.js how to differentiate clients》](https://stackoverflow.com/questions/13364243/websocketserver-node-js-how-to-differentiate-clients)，直接賦值 id 一個隨機變數。

或是參考 github 上的這篇討論 [《unique identifier for each client request to websocket server》](https://github.com/websockets/ws/issues/859)，直接取 request header 中的 `'sec-websocket-key'` 給每個 Client 新屬性 id：

```jsx
wss.on('connection', (ws, req) => {
      var id = req.headers['sec-websocket-key']
      // Do something...
})
```

總之目的是取得各個使用者獨一無二的識別證，透過這些識別證，又能夠對照到我們所熟悉的遊戲 ID 或是帳號。

經整理後的程式碼如下：

- Server 端：server.js

```jsx
// import library
const express = require('express')
const ServerSocket = require('ws').Server   // 引用 Server

const PORT = 8080

// 建立 express 物件並用來監聽 8080 port
const server = express()
    .listen(PORT, () => console.log(`[Server] Listening on https://localhost:${PORT}`))

// 建立實體，透過 ServerSocket 開啟 WebSocket 的服務
const wss = new ServerSocket({ server })

// Connection opened
wss.on('connection', (ws, req) => {
    ws.id = req.headers['sec-websocket-key'].substring(0, 8)
    ws.send(`[Client ${ws.id} is connected!]`)

    // Listen for messages from client
    ws.on('message', data => {
        console.log('[Message from client] data: ', data)
        // Get clients who has connected
        let clients = wss.clients
        // Use loop for sending messages to each client
        clients.forEach(client => {
            client.send(`${ws.id}: ` + data)
        })
    })

    // Connection closed
    ws.on('close', () => {
        console.log('[Close connected]')
    })
})
```

- Client 端：index.js

```jsx
var ws

// 監聽 click 事件
document.querySelector('#connect')?.addEventListener('click', (e) => {
    connect()
})

document.querySelector('#disconnect')?.addEventListener('click', (e) => {
    disconnect()
})

document.querySelector('#sendBtn')?.addEventListener('click', (e) => {
    const msg = document.querySelector('#sendMsg')
    sendMessage(msg?.value)
})

function connect() { 
    // Create WebSocket connection
    ws = new WebSocket('ws://localhost:8080') 
    
    // ws = new WebSocket('ws://192.168.17.35:58095') 
    // 在開啟連線時執行
    ws.onopen = () => {
        console.log('[open connection]')
        // Listen for messages from Server
        ws.onmessage = event => {
            console.log(`[Message from server]:\n %c${event.data}` , 'color: blue')
        }
    }
}

function sendMessage(msg) {
    // Send messages to Server
    ws.send(msg)
}

function disconnect() {
    ws.close()
    // 在關閉連線時執行
    ws.onclose = () => console.log('[close connection]')
}
```

實際運行如下圖，完成具備廣播功能的即時聊天室！

![](https://i.imgur.com/AdQEjiF.gif)

## 1.5 WebSocket API

在實際練習後，這裡整理一些常用的 [WebSocket API](https://developer.mozilla.org/zh-TW/docs/Web/API/WebSocket)：

- `new WebSocket()`：和 Server 建立 WebSocket 連線

### 1.5.1 WebSocket Event

在建立 WebSocket 連線之後，即可透過 `addEventListener()` 監聽各種 Events，做出不同的對應方法，可參考 MDN 上的 [Events](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket#events)：

- open：成功和 Server 建立連線時觸發
- message：接收到 Server 端訊息時觸發
- close：關閉連線時觸發
- error：連線發生錯誤時觸發

### 1.5.2 WebSocket Methods

同樣在建立 WebSocket 連線後，可使用以下 WebSocket 提供的方法進行互動：

- send()：發送訊息給 Server
- close()：關閉連線

## 2.1 比較：Socket / WebSocket  / Socket.IO

[Socket](https://zh.wikipedia.org/zh-tw/%E7%B6%B2%E8%B7%AF%E6%8F%92%E5%BA%A7)、Websocket、[Socket.io](http://socket.io/) 這三者都和網路即時通訊有關，名稱也有 87 像，但實際上是不同的東西，以下做簡單的整理介紹：

![](https://i.imgur.com/aI5zQVj.png)

> 參考資料：[https://leesonhsu.blogspot.com/2018/07/socketwebsocketsocketio.html](https://leesonhsu.blogspot.com/2018/07/socketwebsocketsocketio.html)

### 2.1.1 Socket：傳輸與應用層之間的接口

- 是應用層與 TCP/IP 協議之間的抽象層介面，是一組 API 接口
- 使用者可透過 Socket 來操作 TCP/IP 協議通信
- 傳輸方式多為 stream
- 傳輸載體類型為 binary

### 2.1.2 WebSocket：應用層協議

- 為了改善 Web 即時雙向通訊而創造出來的協議
- 瀏覽器底層使用 Socket 作為通訊介面
- 載體類型有二種：binary（二進位）或 text（文字），只能擇一使用

### 2.1.3 Socket.IO：一套 JavaScript 函式庫

- 是一套用來建立即時通訊應用的 JavaScript 函式庫，透過 [Socket.IO](http://Socket.IO) 能夠實現 Server 與多個 Client 之間的溝通
- 是建立在 WebSocket 之上的套件，並支持代理和負載平衡器
- 由兩部分組成，包含 Server 端的 node.js 和 Client 端的 JavaScript
- 核心是 [engine.io](https://github.com/socketio/engine.io)，透過 WebSocket 和 HTTP long-polling 方式實現伺服器和客戶端之間的連線

## 3. 小結

這次同樣是工作上接手新專案，需要研究 WebSocket 相關技術，於是又把過去一些網路基礎知識給複習一遍，每次都對網路協議多了一層認識。

對自己而言算是比較陌生的領域，即使大概知道功用是什麼，實際操作起來又是另一回事，但學習新知識果然還是很快樂，期許自己能夠早日上手。

## 4. Reference

- [WebSocket 是什么原理？为什么可以实现持久连接？](https://www.zhihu.com/question/20215561)
- [HTML5 WebSocket](http://yiyimiao.com/html/html5-websocket.html)
- [JavaScript | WebSocket 讓前後端沒有距離 - Medium](https://medium.com/enjoy-life-enjoy-coding/javascript-websocket-%E8%AE%93%E5%89%8D%E5%BE%8C%E7%AB%AF%E6%B2%92%E6%9C%89%E8%B7%9D%E9%9B%A2-34536c333e1b)
- [【筆記】Socket，Websocket，Socket.io的差異](https://leesonhsu.blogspot.com/2018/07/socketwebsocketsocketio.html)
- [[note] socket.io 筆記](https://pjchender.dev/npm/npm-socket-io/)
- [用Socket.io 做一個即時聊天室吧！（直播筆記）](https://creativecoding.in/2020/03/25/%E7%94%A8-socket-io-%E5%81%9A%E4%B8%80%E5%80%8B%E5%8D%B3%E6%99%82%E8%81%8A%E5%A4%A9%E5%AE%A4%E5%90%A7%EF%BC%81%EF%BC%88%E7%9B%B4%E6%92%AD%E7%AD%86%E8%A8%98%EF%BC%89/)