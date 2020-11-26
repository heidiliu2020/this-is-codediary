###### tags: `Back-End` `Express` `Middleware`
# [week 17] 後端中階 - Express 中不可或缺的拼圖：淺談 Middleware

> 本篇為 [[BE201] 後端中階：Express 與 Sequelize](https://lidemy.com/p/be201-express-sequelize) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 學習如何使用 Express 及其相關套件
 P1 我理解為什麼會需要框架
```

---

## Middleware 中間介

在上一篇筆記 [後端中階 - Node.js + Express 框架：建立一個靜態網頁](https://hackmd.io/@Heidi-Liu/note-be201-express-node)，我們學到如何透過 Node.js 搭配 Express 框架，來快速建立一個靜態網頁。並瞭解到什麼是 MVC 架構，以及如何串接 MySQL 資料庫。

而 Express 的核心，其實就是由 Routing（路由系統）和 Middleware（中間介）兩個部分所組成。

也就是說，Express 會根據定義不同路由來執行接收到的 request，過程中會透過一連串的 middleware 處理，執行到最後產生 response。

![](https://i.imgur.com/uvrlRi6.png)

接下來我們會針對 Middleware 的部分做介紹。

### 什麼是 Middleware？

在 Express 開發框架中，middleware 扮演資料庫與應用程式之間的溝通橋樑，透過不同類別的 middleware，依照需求對資料進行不同處裡，讓資料傳遞更加便利。

例如先前範例中的 `app.get('/todos', todoController.getAll)`，其實就可以看做是一個 middleware。

我們可透過 middleware function 傳入三個參數，然後輸出想要的資料：
- 第一個參數是 request
- 第二個參數是 response
- 再透過第三個參數 next 把控制權轉移到下一個 middleware

舉個簡單的例子，在之前實作 todolist 的 index.js 中加入 `app.use()`，代表整個程式都能使用這個 middleware：

```javascript=
const express = require('express');
const db = require('./db')
const app = express();
const port = 5002;

const todoController = require('./controllers/todo')

app.set('view engine', 'ejs')

// app.use(): 代表整個程式都能使用這個 middleware
app.use((req, res) => {
  console.log('Time: ', new Date())
  res.end()
})

app.get('/todos', todoController.getAll)
app.get('/todos/:id', todoController.get)

app.listen(port, () => {
  db.connect()
  console.log(`Example app listening at http://localhost:${port}`)
}) 
```

重整瀏覽器頁面時，會發現畫面什麼東西都沒有：

![](https://i.imgur.com/N6etIbh.png)

但是在 CLI 介面會印出執行結果，每重整一次畫面就會執行 log 一次：

![](https://i.imgur.com/J3r3CtM.png)

之所以沒有得到 response，是因為沒有加入第三個參數，也就是呼叫 next 把控制權轉移到下一個 middleware。可以把程式碼修改如下：

```javascript=
app.use((req, res, next) => {
  console.log('Time: ', new Date())
  next()  // 呼叫 next 把控制權轉移到下一個 middleware
})
```

重整頁面後，就能看到經渲染過的畫面，同時 CLI 介面上也會印出接收 request 的時間：

![](https://i.imgur.com/9vEZvbU.png)

這其實就是一個簡單的 middleware 應用。那這個機制實際上在 Express 有哪些用處呢？比如說，在 Express 程式中，並沒有內建解析透過 post method 的 request body、管理 session 機制等功能，就必須透過 middleware 來實現。

此外，middleware 處理是有順序性的。以一個簡單的權限管理機制為範例，例如網址列上必須有 `admin` 才能顯示頁面：

#### 方法一：最直覺的作法

這個方法是透過 Express 內建的 `.query` 語法，來拿到網址列上的參數。

直接在兩個 Controllers 都加上 checkPermission() 進行權限驗證：

```javascript=
const todoModel = require('../models/todo')

function checkPermission(req) {
  // 利用 .query 能夠拿到網址列上的參數
  return req.query.admin === '1';
}

const todoController = {
  getAll: (req, res) => {
    // 如果驗證失敗就結束 request
    if (!checkPermission(req)) return res.end();
    todoModel.getAll((err, results) => {
      if (err) return console.log(err);
      res.render('todos', {
        todos: results
      })
    })
  },

  get: (req, res) => {
    // 如果驗證失敗就結束 request
    if (!checkPermission(req)) return res.end();
    const id = req.params.id
    todoModel.get(id, (err, results) => {
      if (err) return console.log(err);
      res.render('todo', {
        todo: results[0]
      })
    })
  }
}

module.exports = todoController
```

回到瀏覽器，會發現必須網址列加上 `?admin=1` 參數才能讀取畫面：

![](https://i.imgur.com/rJV2764.png)

這樣就完成簡單的權限驗證機制，但這其實不是一個好做法，一旦 function 變多就會不易管理。這種情況就是 middleware 登場的時候了！

#### 方法二：透過 middleware 機制

在 index.js 加上 app.use()，並傳入 next 參數，若網址列通過驗證就會把控制權傳下去：

```javascript=
app.use((req, res, next) => {
  // 如果網址列通過驗證，就把控制權傳下去
  if (req.query.admin === '1') {
    next();
  } else {
    // 不通過的話就顯示 Error
    res.end('Error')
  }
})
```

執行結果如下：

![](https://i.imgur.com/WkQUoAq.png)

這其實就是 middleware 的作用，相較於方法一，我們能透過 middleware 來簡化程式碼。

此外，我們也能改寫上述程式碼，獨立出 checkPermission() 這個 function 來進行驗證：

```javascript=
function checkPermission(req, res, next) {
  if (req.query.admin === '1') {
    next();
  } else {
    res.end('Error')
  }
}

app.use(checkPermission)
```

這種寫法的好處，在於我們可以針對不同路由進行處理。例如加在 `/todos` 時，就只有這個路由會被影響：

```javascript=
app.get('/todos', checkPermission, todoController.getAll)
```

在 `/todos` 這個路由，必須加上 `?admin=1`  才能顯示畫面：

![](https://i.imgur.com/38VqxUf.png)

但是 `/todos/:id` 這個路由不會受到影響，因為沒有加上 checkPermission() 這個 middleware：

![](https://i.imgur.com/wqOkqgg.png)

在上一篇筆記的 todolist 範例中，之所以沒有寫到 next 來轉移控制權，是因為處理完就回傳 response 資料，既然不會用到 next 這個參數，就可省略宣告。

## body-parser：用來解析 HTTP Request

接著要來介紹 body-parser 這個很常使用到的 middleware，使用方法可參考 GitHub 上 [expressjs/body-parser](https://github.com/expressjs/body-parser#readme) 的範例。

body-parser 是一個用來解析解析 HTTP Request 的中間介。前面有提到說，我透過 Express 內建的語法，我們只能拿到 query string，因此只適用於 GET method，但若是 POST method 就必須透過 middleware 才能拿到 request body。

### 安裝 body-parser

```
$ npm install body-parser
```

![](https://i.imgur.com/4q8sKI0.png)

### body-parser 語法

body-parser 根據不同語法，能夠處理下列幾種格式資料：

- bodyParser.urlencoded()
  - 處理 UTF-8 編碼的資料，常見的表單（form）提交
  - 例如：application/x-www-form-urlencoded
- bodyParser.json()
  - 處理 JSON 格式的資料
  - 例如：application/json
- bodyParser.text()
  - 處理 type 為 text 的資料
  - 例如：text/html, text/css
- bodyParser.raw()
  - 處理 type 為 application 的資料
  - 例如：application/pdf, application/zip

程式碼範例如下：

```javascript=
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
```

### 實作新增 todo 功能

同樣以之前的 todolist 範例，繼續實作新增 todo 功能：

1. 在 index.js 引入 `body-parser` 套件，就可以使用 bodyParser() 處理 Request。接著在根目錄新增一個處理 addTodo 的路由：

```javascript=
const express = require('express');
// 記得要引入 body-parser 才能使用
const bodyParser = require('body-parser')
const db = require('./db')
const app = express();
const port = 5002;

const todoController = require('./controllers/todo')

app.set('view engine', 'ejs')
// 處理 UTF-8 編碼的資料
app.use(bodyParser.urlencoded({ extended: false }))
// 處理 json 資料
app.use(bodyParser.json())

app.get('/todos', todoController.getAll)
app.get('/todos/:id', todoController.get)
// 在根目錄新增一個 addTodo 路由
app.get('/', todoController.addTodo)

app.listen(port, () => {
  db.connect()
  console.log(`Example app listening at http://localhost:${port}`)
})
```

2. 在 Controllers 新增一個處理 addTodo 的 Controller，須注意這裡只負責 render 渲染頁面，而不是真的處理新增 todo 動作：

```javascript=
const todoModel = require('../models/todo')

const todoController = {
  getAll: (req, res) => {
    todoModel.getAll((err, results) => {
      if (err) return console.log(err);
      res.render('todos', {
        todos: results
      })
    })
  },

  get: (req, res) => {
    const id = req.params.id
    todoModel.get(id, (err, results) => {
      if (err) return console.log(err);
      res.render('todo', {
        todo: results[0]
      })
    })
  }

  // 這裡只負責 render 頁面，並不是真的處理新增 todo
  addTodo: (req, res) => {
    res.render('addTodo')
  }
}

module.exports = todoController
```

3. 接著要實作 addTodo 的 view 部分，也就是新增一個 addTodo.ejs 檔：

```javascript=
<h1>Add Todo</h1>

<form method="POST"" action="/todos">
  Content: <input type="text" name="content" />
  <input type="submit" />
</form>
```

執行後可在瀏覽器確認是否有畫面：

![](https://i.imgur.com/scuiz9Q.png)

這時如果點選提交，會跳轉到錯誤頁面，這是因為還沒有處理路由：

![](https://i.imgur.com/9xs6k0Y.png)

4. 回到 index.js 新增一個處理 newTodo 的路由：

```javascript=
// 新增一個處理 newTodo 的路由
app.post('/todos', todoController.newTodo)
app.get('/todos', todoController.getAll)
app.get('/todos/:id', todoController.get)
// 新增一個處裡 addTodo 的路由
app.get('/', todoController.addTodo)
```

5. 接著同樣新增一個處理 newTodo 的 Controller，確認是否有成功拿到表單資料：

```javascript=
newTodo: (req, res) => {
  // 透過 body-parser 解析 resquest body 來拿取 content
  const content = req.body.content
  // 先輸出確認是否有拿到資料
  res.end(content)
},
```

在瀏覽器提交表單，確認有拿到資料：

![](https://i.imgur.com/EQIScXT.png)

之所以能夠拿到表單提交的資料，是透過 body-parser 這個中間介解析 resquest body，才能拿取 content，否則程式會因為無法解析而出現錯誤。

6. 接著繼續修改 newTodo Controller，把資料交給 Model 處理：

```javascript=
newTodo: (req, res) => {
  // 透過 body-parser 解析 resquest body 來拿取 content
  const content = req.body.content
  todoModel.add(content, (err) => {
    if (err) return console.log(err);
    // 重新導回 todos 頁面
    res.redirect('/todos');
  })
},
```

7. 再來是處理 todoModel.add() 的部分，也就是在 Model 新增一個 add 功能：

```javascript=
// 新增 todoModel.add()
add: (content, cb) => {
  db.query(
    'INSERT INTO todos(content) VALUES(?)', [content], (err, results) => {
      if (err) return cb(err);
      cb(null)
    }
  );
}
```

回到瀏覽器確認是否能夠新增 todo：

![](https://i.imgur.com/Y9fPgmN.png)

這樣就完成一個簡單的 Back-end 專案了！並且有 MVC 架構，也就是 View 顯示畫面，Model 處理資料，Controller 藉由不同路由接收 requset，會執行相對應的 method；還有透過 body-parser 這個 middleware 處理 POST 表單提交的資料。

## express-session：負責管理 session

再來介紹 Express 框架中，用來管理 session 的中間介：express-session，使用方法可參考 GitHub 的 [expressjs/session](https://github.com/expressjs/session) 頁面。

### 安裝 express-session

```
$ npm install express-session
```

![](https://i.imgur.com/jWw5ZYd.png)

### 實作簡易登入功能

接著延續前面的 todolist 範例，實作一個簡單的登入功能。

1. 在 index.js引入 express-session 套件：

```javascript=
// 引入 express-session
const session = require('express-session')
```

2. 接著設定 app 載入模組 express-session：

```javascript=
// 在 app.js 中設定載入模組 express-session
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true
}))
```

3. 實作 login、提交表單、logout 的路由，須注意是 req.session，request 才有 session：

```javascript=
// 實作 login 路由
app.get('/login', (req, res) => {
  res.render('login')
})
// login 提交表單的路由
app.post('/login', (req, res) => {
  if (req.body.password === 'abc') {
    // 注意是 request 才有 session
    req.session.isLogin = true
    // 成功就導回首頁；失敗則導回上一頁
    res.redirect('/')
  } else {
    res.redirect('/login')
  }
})
// 實作 logout 路由
app.get('/logout', (req, res) => {
  req.session.isLogin = false
  res.redirect('/')
})
```

4. 設定 Controller 部分，在 addTo 首頁加上 isLogin 參數，用來判別是否登入：

```javascript=
  addTodo: (req, res) => {
    res.render('addTodo', {
      isLogin: req.session.isLogin
    })
  }
```

5. 設定 view 部分，在 addTodo.ejs 頁面顯示是否登入，增加連結導向 login 頁面或 logout：

```javascript=
<h1>Add Todo</h1>

<% if(isLogin) { %>
  已經登入  <a href="/logout">登出</a>
<% } else { %>
  請先登入  <a href="/login">登入</a>
<% } %>

<form method="POST"" action="/todos">
  Content: <input type="text" name="content" />
  <input type="submit" />
</form>
```

6. 新增 login.ejs 頁面，能夠輸入密碼提交表單：

```javascript=
<h1>Login</h1>

<form method="POST" action="/login">
  Password: <input type="password" name="password" />
  <input type="submit" />
</form>
```

執行結果：

![](https://i.imgur.com/3RZUKgn.png)

這樣就透過 express-session 中間介提供的功能，完成簡單的登入登出功能。

但這種寫法其實會遇到一個問題，也就是每個 render 的頁面都要加上 isLogin 判斷登入狀態，我們再來要介紹的中間介就可以解決這個問題。

## connect-flash：顯示錯誤訊息

藉由 connect-flash 提供的 flash message 功能，我們就能在頁面顯示錯誤訊息等等，其實這背後的機制就是透過 session，能夠和 express-session 搭配使用。

使用方法可參考 GitHub 的 [jaredhanson/connect-flash](https://github.com/jaredhanson/connect-flash) 頁面。

### 安裝 connect-flash

```
$ npm install connect-flash
```
### 實作錯誤顯示功能

1. 在 index.js引入 connect-flash 套件：

```javascript=
// 引入 connect-flash
const flash = require('connect-flash');
```

2. 設定 app 載入 flash 模組：

```javascript=
app.use(flash())
```

3. 在 login 路由使用 flash()，傳入的兩個參數分別代表 key: value：

```javascript=
app.get('/login', (req, res) => {
  res.render('login', {
    // 從 flash() 中拿取 errorMessage 這個 key 的 value
    errorMessage: req.flash('errorMessage')
  })
})
app.post('/login', (req, res) => {
  if (req.body.password === 'abc') {
    req.session.isLogin = true
    res.redirect('/')
  } else {
    // falsh() 要傳入兩個參數，代表 key: value
    req.flash('errorMessage', 'Please input the correct password.')
    res.redirect('/login')
  }
})
```

4. 設定 login.ejs，當登入失敗就會在畫面顯示 errorMessage：

```javascript=
<h1>Login</h1>

<h2><%= errorMessage %></h2>

<form method="POST" action="/login">
  Password: <input type="password" name="password" />
  <input type="submit" />
</form>
```

執行結果如下，當提交錯誤時會顯示 errorMessage，重整頁面後就會消失，這就是 flash 的功用：

![](https://i.imgur.com/OepYfsy.png)

但這種寫法其實還是不夠簡潔，如果要判斷輸出錯誤都還是要向 isLogin 那樣加上 errorMessage。

### 重構程式碼：透過 res.locals 傳值給 view

其實在 express 中有個捷徑，我們可以自己新增 middleware。也就是把東西存放在 `res.locals` ，view 就可以直接從 locals 存取使用，可想像成全域變數的感覺：

```javascript=
// 透過 locals 傳值給 view: session 功能和 errorMessage
app.use((req, res, next) => {
  res.locals.isLogin = req.session.isLogin
  res.locals.errorMessage = req.flash('errorMessage')
  // 記得加上 next() 把控制權轉移到下一個中間介
  next()
})

// 就不需在路由加上 errorMessage
app.get('/login', (req, res) => {
  res.render('login')
})
```

addTodo 的 Controller 也可以改回原本的：

```javascript=
addTodo: (req, res) => {
  res.render('addTodo'）
}
```

修改完成之後，同樣能夠執行程式，透過範例整理兩個重點：

- 透過 req.flash() 可實作出 errorMessage
- 透過 res.locals 可傳值給 view 使用，通常會用在驗證功能或是顯示 errorMessage

---

## 結語

透過上述範例，我們能夠得知在使用 Express 框架實作網頁時，大致上會依照下方流程進行：

1. 思考產品全貌：網頁外觀、需要哪些功能等等
2. 規劃資料庫結構
3. 載入需要的模組，設定 app 路由部分
4. 依照 MVC 架構撰寫程式碼：
    - 設定 Controller：針對不同路由進行控制
    - 設定 Model：如何處理資料
    - 設定 View：如何呈現畫面

在接下來的課程，我們會綜合之前所學的知識，來實作簡單的會員註冊系統以及留言版功能。

參考資料：

- [「筆記」- 何謂 Middleware？如何幫助我們建立 Express 的應用程式](https://medium.com/pierceshih/%E7%AD%86%E8%A8%98-%E4%BD%95%E8%AC%82-middleware-%E5%A6%82%E4%BD%95%E5%B9%AB%E5%8A%A9%E6%88%91%E5%80%91%E5%BB%BA%E7%AB%8B-express-%E7%9A%84%E6%87%89%E7%94%A8%E7%A8%8B%E5%BC%8F-19082b1d8e06)
- [bodyParser中间件的研究](https://segmentfault.com/a/1190000004407008)
