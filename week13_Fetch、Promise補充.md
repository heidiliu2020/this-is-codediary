###### tags: `Back-End` `JavaScript` `fetch` `Promise`
# [week 13] Fetch & Promise 補充

> 本篇為 Limedy「Fetch 與 Promise 補充系列」課程的學習筆記。如有錯誤歡迎指正。

以下內容將涵蓋：
1. 如何發出 HTTP 請求
2. 回顧 XMLHttpRequest
3. 如何使用 Fetch
4. 什麼是 Promise

---

## 如何發出 HTTP 請求

想要在前端網頁發出 Ajax Request，可以有下列兩種方式：
1. XMLHttpRequest
2. Fetch

## 回顧 XMLHttpRequest

[XMLHttpRequest]((https://developer.mozilla.org/zh-TW/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest))，簡稱 XHR。在[課程第八週](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week8_%E5%82%B3%E9%80%81%E8%B3%87%E6%96%99%E7%9A%84%E6%96%B9%E5%BC%8F%E3%80%81%E7%80%8F%E8%A6%BD%E5%99%A8%E7%9A%84%E9%99%90%E5%88%B6.md)時曾提到，我們可利用 XMLHttpRequest 物件來實現非同步請求，以下列程式碼為例：

```javascript=
function reqListener () {
 if (request.status >= 200 && request.status < 400) {
  console.log(request.responseText)
 } else {
  console.log("err")
}

const request = new XMLHttpRequest();
request.addEventListener("load", reqListener);

// 加載失敗會回傳 error
request.onerror = function () {
  console.log('error');
};

request.open("GET", "http://www.example.org/example.txt", true);
request.send();
```

1. 建立一個 XMLHttpRequest 物件：request
2. 開啟一個 URL `.open()`
    - 設定三個參數：請求方法﹑請求的 URL、是否非同步傳送請求
4. 掛載 load callback：加載完成時會呼叫 reqListener 函式
5. 發起一個請求 `.send()`

如果利用 Fetch 來達到相同結果，程式碼如下：

```javascript=
fetch("http://www.example.org/example.txt")
 .then(res=>res.text())
 .then(console.log)
```

若不指定方法，Fetch 預設方法是使用 GET，與前者相比可發現程式碼簡潔許多。

## 初探 Fetch

要進行 API 串接，發出 GET Request，可利用這個網站：[Mocky: The world easiest & fastest tool to mock your APIs](https://designer.mocky.io/)，能夠自行創造 Response 並指定內容。

### 如何發出 Request

在 index.html 加上 `fetch('請求的 URL')` 即可發出最簡單的 Resquest：

```javascript=
<script>
  fetch('https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605')
</script>
```

使用開發者工具查看 Network，確實有收到 Response：

![](https://i.imgur.com/eOZlngN.png)

或是直接將結果印出來，可知 fetch 其實會回傳一個 Promise：

```javascript=
<script>
  const result = fetch('https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605');
  console.log(result)
</script>
```

![](https://i.imgur.com/QtvlDpl.png)

---

## 什麼是 Promise

Promist 是一個獨特的物件，簡單來說就是一個容器，裡面儲存著某個未來才會結束的事件（也就是非同步操作）的結果。

### `then()`：拿取 Promise Response

那麼該如何拿到 Promise 結果呢？我們可利用 `.then()` 來拿到 Response，如下列程式碼：

```javascript=
<script>
  function pringResult(response) {
    console.log(response)
  }
  const result = fetch('https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605');
  result.then(pringResult)
</script>
```

![](https://i.imgur.com/d88g2Ox.png)

可把上述程式碼簡化如下：

```javascript=
fetch('https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605')
  .then( response => {
    console.log(response)
  })
```

### `.text()` & `.json()`：處理 Response Body

#### `.text()`

利用 `.text()` 我們會同樣會得到一個 Promise，再以 `.then()` 處理，可讀取 Response 的 body：

```javascript=
const api200 = 'https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605'

fetch(api200)
  .then(response => {
    response.text().then(text => {
      console.log(text)
    })
  })
```

確認可在瀏覽器上印出 text 內容：

![](https://i.imgur.com/rqFF0In.png)

#### `.jaon()`

若確定 Response 是 JSON 格式，可使用 `.json()` 以相同寫法來處理，回傳的結果會如同 `JSON.parse()` 幫我們轉成 json 格式：

```javascript=
const api200 = 'https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605'

fetch(api200)
  .then(response => {
    response.json().then(json => {
      console.log(json)
    })
  })
```

確認得到 JSON 格式的結果：

![](https://i.imgur.com/3ZOk7YA.png)

### 簡化再簡化

由於 `response.json()` 這個 Promise 的回傳值是 text，我們可將程式碼簡化如下：

```javascript=
fetch(api200)
  .then(response => {
    return response.json()  // 回傳值
  }).then(json => {         // then() 會將回傳值帶到 Response
    console.log(json)       // 取到回傳值
  })
```

若使用 return 的寫法，可減少縮排層數，簡化程式碼的複雜程度。

### Chaining 特性

上述的寫法，其實是利用 Promise 具有的 Chaining 特性。

由於 `.then()` 裡面回傳的還是一個 promise 物件，我們可以一直使用 `.then()` 來對回傳的 promise 做處理。

```javascript=
fetch() ⇒ Promise
response.text() ⇒ Promise
response.json() ⇒ Promise
respose.json.then() ⇒ Promise
```

透過這個特性，我們能夠有效減少層數，以第八週串接 twitch API 為例：

- Callback function

```javascript=
getGames(()=>{
  getStreams(()=>{
    getChannel(()=>{
      getTitle(()=>{
          ...
      })
    })
  })
})
```

- Promise

```javascript=
getGames(() => {
  return getStreams();
}).then((streams) => {
  return getChannel();
}).then((channel) => {
  return getTitle();
});
```

---

## 如何處理 Error

這裡指的錯誤，並非 http status 400、500 那種錯誤，而是根本無法拿到 Response，Fetch 才會回傳錯誤訊息。

在過去課程中，以同步方式傳送請求，可使用 `try / catch` 來處理錯誤訊息：

```javascript=
try {
  // 正常執行..
} catch(e) {
  // 出錯執行..
}
```

但這個錯誤處理無法應用在 callback function，因為 Fetch 使用非同步方式發出請求。

### `.catch()`：處理錯誤訊息

程式碼範例如下：

```javascript=
fetch(api200)
  .then(response => {
    return response.json()
  }).then(json => {
    console.log(json)
  }).catch(err => {
    console.log('error', err)
  })
```

再把原先的請求 URL 稍作修改，確認可接收錯誤訊息：

![](https://i.imgur.com/qN3HBCE.png)

簡言之，我們可使用 `then()` 來接收回傳的結果，`catch()` 來接收回傳的錯誤。

## 利用 Fetch 發 POST

前面提到過，Fetch 預設方法是使用 GET，若要發出 POST Request，需帶入其他參數，以下列程式碼為例：

```javascript=
const data = {
  name: 'heidi'
}
fetch(api200, {
  method: 'POST',
  body: JSON.stringify(data),
  headers: new Headers({
    'Content-Type': 'application/json'
  })
})
  .then(response => {
    return response.json()
  }).then(json => {
    console.log(json)
  }).catch(err => {
    console.log('error', err)
  })
```

### Fetch 使用時的注意事項

#### Content-Type

- 決定 server 如何處理 Request
- 根據不同的 Content-Type，需修改 body 如何解析接收到的資料格式

#### credential

發 request 給不同來源 domain 的 API 時，並不會自動把 cookie 帶上去，需加上 `credentials: 'includes'`，如下列程式碼：

```javascript=
const data = {
  name: 'heidi'
}
fetch(api200, {
  method: 'POST',
  body: JSON.stringify(data),
  credentials: 'includes',
  headers: new Headers({
    'Content-Type': 'application/json'
  })
})
  .then(response => {
    return response.json()
  }).then(json => {
    console.log(json)
  }).catch(err => {
    console.log('error', err)
  })
```

#### 對 mode 的誤解

- `mode: 'no-cors'` 並不能突破 CORS 限制，而是會回傳一個空的 response
- 這條指令只是跟瀏覽器說：「我沒有要拿 response，不用傳錯誤訊息給我」
- 要在瀏覽器解決 CORS 限制，必須在 Server 端加上開 CORS 的 header

---

## 建立 Promise 物件

根據上述結論，要處理非同步，我們可利用 callback function 或 Promise。

以下程式碼示範如何建立 Promise 物件，也就是把一個 Promise 物件 new 出來：

```javascript=
function init(resolve, reject) {
  resolve(3)
}
const myPromise = new Promise(init)

myPromise.then((data) => {
    console.log('data', data)       // 正確
  }).catch(err => {
    console.log('err', err)         // 錯誤
  })
  
// 印出 data 3
```

簡化 function 如下：

```javascript=
const myPromise = new Promise((resolve, reject) => {
  resolve(3)
})

myPromise.then((data) => {
    console.log('data', data)       // 正確
  }).catch(err => {
    console.log('err', err)         // 錯誤
  })
```

### 應用範例

透過 `setTimeout()` 結合 promise 應用：

```javascript=
const myPromise = new Promise((resolve) => {
  setTimeout(resolve, 3000)         // 3 秒後呼叫 resolve()
})

myPromise.then((data) => {
  console.log('myPromise data', data)
}).catch(err => {
  console.log('err', err)
})

// 經過 3 秒後會印出 myPromise Data undefined
```

再來利用 `function sleep()` 宣告一個叫做 myPromise 的 Promise 再回傳回去。如下列程式碼：

```javascript=
function sleep(ms) {
  const myPromise = new Promise(resolve => {
    setTimeout(resolve, ms)      // 把秒數設為參數
  })
  return myPromise;
}

sleep(1500).then((data) => {      // 執行完 sleep() 會得到 Promise
  console.log('myPromise Data', data);
})
  .catch(err => {
    console.log('err', err);
  })
```

上述的 `sleep()` 函式可簡化成：

```javascript=
function sleep(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms)
  })
}
```

若以箭頭函式表達則是：

```javascript=
const sleep = ms => {
  return new Promise(resolve => {
    setTimeout(resolve, ms)
  })
}
```

還可以簡化成這樣，省略 return 的大括號：

```javascript=
const sleep = ms => new Promise(resolve => {
    setTimeout(resolve, ms)
  })
```

最終可簡化成一行：

```javascript=
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))
```

## async / await

利用 `async / await` 可以用看起來像同步的語法，做到非同步的事情。

方法是用 async 宣告一個非同步的 function，裡面放 await 接一個 Promise 物件，會等到執行完 await 裡面的 promise 才往下執行。

以下列程式碼為例：

```javascript=
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

async function main() {
  console.log('enter main');
  await sleep(1000);        // 接一個 Promise
  console.log('exit main');
}

main();
// 執行 main() 會發現兩個 console.log 會相隔一秒印出
```

對照使用 Promise 的寫法：

```javascript=
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

function mainPromise() {
  console.log('enter main');
  sleep(1000).then(() => {
    console.log('exit main');
  })
}
```

#### 應用練習

```javascript=
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

function getData() {
  const api200 = 'https://run.mocky.io/v3/d49195d4-9e5b-4b63-8b13-6f97e46eb605'
  return fetch(api200)      // 回傳執行完的結果 => Promise
    .then(response => {
      return response.json()
    })
}

async function main() {
  console.log('enter main');
  await sleep(1000);              // 等 1 秒
  const result = await getData();    // 接收到結果才會繼續執行
  console.log('result', result);
}

main();
```

甚至可使用同步方式的 `try / catch` 來處理錯誤訊息：

```javascript=
async function main() {
  console.log('enter main');
  await sleep(1000);              // 等 1 秒
  try {
    const result = await getData();
    console.log('result', result);
  } catch(err) {
    console.log('err', err);
  }
}
```


參考資料：
- [ES6入門之Promise物件](https://www.itread01.com/content/1570606323.html)
- [JavaScript 的同步與非同步 - 從 Callback function 到 Promise](https://nicolakacha.coderbridge.io/2020/09/11/sync-async/)