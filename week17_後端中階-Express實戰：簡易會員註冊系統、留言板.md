###### tags: `Back-End` `Express` `Middleware`
# [week 17] 後端中階 - Express 實戰：簡易會員註冊系統 & 留言板

> 本篇為 [[BE201] 後端中階：Express 與 Sequelize](https://lidemy.com/p/be201-express-sequelize) 這門課程的學習筆記。如有錯誤歡迎指正！

在之前的課程中，我們學到如何使用 Express 這套框架，以及在 Express 扮演重要角色的 Middleware：

- [[week 17] 後端中階 - 使用 Node.js + Express 框架建立一個靜態網頁](https://hackmd.io/@Heidi-Liu/note-be201-express-node)
- [[week 17] 後端中階 - Express 中不可或缺的拼圖：淺談 Middleware](https://hackmd.io/@Heidi-Liu/note-be201-express-middleware)

接下來要整合我們之前所學的東西，透過串聯資料庫系統，來實作一個簡單的會員註冊系統，並且具有留言版功能的網頁。

---

## 實戰練習：簡易會員註冊系統

在使用 Express 框架實作網頁時，大致上會依照下方流程進行：

1. 思考產品全貌：會員註冊系統
2. 規劃資料庫結構：users table
3. 載入需要的模組，設定 app 路由部分
4. 依照 MVC 架構撰寫程式碼：
    - 設定 Controller：針對不同路由進行控制
    - 設定 Model：如何處理資料
    - 設定 View：如何呈現畫面

接下來我們也同樣會依照這個流程來進行實作。

### Step1. 規劃資料庫

首先在資料庫新增一個 users Tabel：

![](https://i.imgur.com/22wyXlm.png)

可以把 username 設定為 UNIQUE（唯一），只要 username 有重複就會自動跳出錯誤訊息：

![](https://i.imgur.com/75SSRX2.png)

### Step2. 實作 Model 部分

接著要來建立 userModel：

```javascript=
const db = require('../db');

const userModel = {
  // 新增 user 功能
  add: (user, cb) => {
    db.query(
      'INSERT INTO users(username, password, nickname) VALUES(?, ?, ?)',
      [user.username, user.password, user.nickname],
      (err, results) => {
        if (err) return cb(err);
        cb(null);
      }
    );
  },

  // 登入 -> 讀取 user 功能
  get: (username, cb) => {
    db.query(
      'SELECT * FROM users WHERE username = ?', [username], (err, results) => {
      if (err) return cb(err);
      cb(null, results[0]);
    });
  }
}

module.exports = userModel;
```

### Step3. 實作 Controller 部分

1. 先建立好程式碼架構，再來填寫路由部分：

```javascript=
// 引入 userModel
const userModel = require('../models/todo')

const userController = {
  get: (req, res) => {

  }
}

// 輸出 userController
module.exports = userController
```

2. 在 index.js 引入 userController，並新增 userController 處理 login 和 logout 的路由：

```javascript=
const userController = require('./controllers/user')

app.get('/login', userController.login)
app.post('/login', userController.handleLogin)
app.get('/logout', userController.logout)
```

3. Controller 就會根據不同路由，建立相對應的 Method：

```javascript=
// 引入 userModel
const userModel = require('../models/user')

const userController = {
  // 渲染登入畫面
  login: (req, res) => {
    res.render('login')
  },

  // 驗證登入狀態
  handleLogin: (req, res) => {
    if (req.body.password) === 'abc' {
      req.session.isLogin = true
      res.redirect('/')
    } else {
      req.flash('errorMessage', 'Please input the correct password.')
    }
  },

  // 登出: 清除 session 並導回首頁
  logout:: (req, res) => {
    req.session.isLogin = false;
    res.redirect('/');
  }
}

// 輸出 userController
module.exports = userController
```

### Step4. 實作 View 部分

1. 在根目錄增加一個首頁 index 的路由：

```javascript=
// 建立首頁
app.get('/', (req, res) => {
  res.render('index')
})
```

2. 接著建立 index.ejs 檔案，實作主要頁面：

```javascript=
<h1>簡易會員系統</h1>

<a href="/register">註冊</a>
<a href="/login">登入</a>
<a href="/logout">登出</a>
```

到這裡可先運行程式看看有沒有問題：

![](https://i.imgur.com/Z1iMl9S.png)

3. 加入前面範例中的驗證功能：

```javascript=
<h1>簡易會員系統</h1>

<% if(isLogin) { %>
  <a href="/logout">登出</a>
<% } else { %>
  <a href="/register">註冊</a>
  <a href="/login">登入</a>
<% } %>
```

這樣在未登入狀態下，就只會看到註冊和登入，這樣就完成了首頁 index 的部分：

![](https://i.imgur.com/n7ew34I.png)

### Step5. 實作註冊功能

1. 先建立 register 路由：

```javascript=
app.get('/register', userController.register)
app.post('/register', userController.handleRegister)
```

2. 根據不同路由建立相對應的 controller，這裡可以把有關 user 的 view 放在同一個資料夾，較方便管理：

```javascript=
  // 渲染註冊頁面
  register: (req, res) => {
    // 把路徑設在 user 資料夾，較方便管理
    res.render('user/register');
  },

  // 驗證註冊
  handleRegister: (req, res) => {

  },
```

3. 新增 register 的 view：

```htmlmixed=
<h1>註冊頁面</h1>

<h2><%= errorMessage %></h2>

<form method="POST" action="/register">
  <div>username: <input type="text" name="username" /></div>
  <div>nickname: <input type="text" name="nickname" /></div>
  <div>password: <input type="password" name="password" /></div>
  <input type="submit" />
</form>
```

記得每做到一個段落就執行程式看有沒有出錯，這樣我們就完成簡單的註冊頁面。這時按提交還不會有反應：

![](https://i.imgur.com/AiRq3HA.png)

4. 再來就是實作 handleRegister 的 Controller 部分：

```javascript=
  // 驗證註冊
  handleRegister: (req, res) => {
    // 從 resquest body 拿取 user 資料
    const {username, password, nickname} = req.body;
    if (!username || !password || !nickname) {
      // 這裡用 return 就可避免 if-else 寫法增加層數
      return req.flash('errorMessage', '缺少必要欄位');
    }
    // 資料都沒問題的話，就可透過 userModel 寫入資料
    // 傳入一個物件，若有錯誤會回傳 cb 
    userModel.add({
      username,
      nickname,
      password
    }, (err) => {
        if (err) {
          // 印出 err
          return req.flash('errorMessage', err.toString());
        }
      }
      // 註冊成功就保持登入狀態，並導回首頁
      req.session.username = username;
      res.redirect('/');
    });
  },
  
  logout: (req, res) => {
  // 登出就把 session 重置
  req.session.username = null;
  res.redirect('/');
}
```

5. 但這樣其實就要把 session 改成存 username，有 username 就代表有登入狀態：

```javascript=
// 透過 locals 傳值: session 功能和 errorMessage
app.use((req, res, next) => {
  // 有 username 代表有登入狀態
  res.locals.username = req.session.username
  res.locals.errorMessage = req.flash('errorMessage')
  next()
})
```

6. 首頁 index.ejs 的驗證也同樣修改成判斷是否有 username，因為使用 `<%= %>` 會輸出原始碼，相當於做 escpae：

```htmlmixed=
<h1>簡易會員系統</h1>

<!-- 以是否有 username 來判斷登入狀況 -->
<% if(username) { %>
  <!-- 使用 <%= %> 會輸出原始碼，相當於做 escpae -->
  <div>Hello, <%= username + '!' %></div>
  <a href="/logout">登出</a>
<% } else { %>
  <a href="/register">註冊</a>
  <a href="/login">登入</a>
<% } %>
```

### Step6. 用 hash 雜湊處理明碼

1. 接下來可安裝 [node.bcrypt.js](https://github.com/kelektiv/node.bcrypt.js/) 套件，即可將明碼進行雜湊處理，避免儲存明碼造成安全性問題：

```
$ npm install bcrypt
```

- 使用方法

```javascript=
// 引入 bcrypt
const bcrypt = require('bcrypt');
// 加鹽，增加密碼的複雜程度
const saltRounds = 10;
```

- 雜湊函式

```javascript=
bcrypt.hash(myPlaintextPassword, saltRounds, function(err, hash) {
    // Store hash in your password DB.
});
```

2. 再來修改剛才的 handleRegister，利用 bcrypt 套件對密碼進行雜湊處理，再儲存 hash 過的密碼到資料庫：

```javascript=
  // 驗證註冊
  handleRegister: (req, res, next) => {
    // 從 resquest body 拿取 user 資料
    const {username, password, nickname} = req.body;
    if (!username || !password || !nickname) {
      // 這裡用 return 就可避免 if-else 寫法增加層數
      req.flash('errorMessage', '缺少必要欄位');
      return next();
    }

    // 利用 bcrypt 套件對密碼進行雜湊處理
    bcrypt.hash(password, saltRounds, function (err, hash) {
      // 若有 err 就直接顯示錯誤訊息
      if (err) {
        req.flash('errorMessage', err.toString());
        return next();
      }
      // 資料都沒問題的話，就可透過 userModel 寫入資料
      userModel.add({
        username,
        nickname,
        password: hash
      }, (err) => {
        // 若有 err 就直接顯示錯誤訊息
        if (err) {
          req.flash('errorMessage', '已存在相同用戶名');
          return next();
        }
        // 註冊成功就保持登入狀態，並導回首頁
        req.session.username = username;
        res.redirect('/');
      });
    });
  },

```

執行程式並註冊一個帳號，確認資料是否有成功寫進資料庫，密碼也有先經過 hash 才儲存：

![](https://i.imgur.com/SVAu3GZ.png)

這樣就差不多完成了註冊功能：

![](https://i.imgur.com/zJZMhbF.png)

### Step7. 實作登入功能

1. 再來要修改登入頁面 login.ejs，畫面和註冊頁面很類似：

```htmlmixed=
<h1>登入頁面</h1>

<h2><%= errorMessage %></h2>

<form method="POST" action="/login">
  <div>username: <input type="text" name="username" /></div>
  <div>password: <input type="password" name="password" /></div>
  <input type="submit" />
</form>
```

2. 然後是 handleLogin，確認是否有填入帳密、確認資料是否正確，透過 bcrypt.compare() 驗證雜湊密碼：

```javascript=
  // 渲染登入畫面
  login: (req, res) => {
    res.render('login')
  },

  // 驗證登入狀態
  handleLogin: (req, res, next) => {
    const { username, password } = req.body;
    // 確認是否有填入資料
    if (!username || !password) {
      req.flash('errorMessage', '請輸入您的帳密！');
      // 每當呼叫 next 時，就會將控制權給下一個中間介 redirectBack(導回上一頁)
      return next();
    }
    // 輸入正確就從 userModel 找出 user 資料
    userModel.get(username, (err, user) => {
      if (err) {
        req.flash('errorMessage', err.toString());
        return next();
      }
      if (!user) {
        req.flash('errorMessage', '使用者不存在');
        return next();
      }
      // 驗證密碼是否正確，三個參數代表: 明碼, 雜湊密碼, 方法
      bcrypt.compare(password, user.password, function (err, isSccess) {
        // 若出現錯誤或比對不成功，就顯示錯誤訊息
        if (err || !isSccess) {
          req.flash('errorMessage', '密碼錯誤');
          return next();
        }
        req.session.username = user.username;
        res.redirect('/')
      });
    })
  },

  // 登出: 清除 session 並導回首頁
  logout: (req, res) => {
    req.session.username = null;
    res.redirect('/');
  }
```

3. 在登入和註冊頁面，每當呼叫 next 時，就會將控制權給下一個中間介，可以自己寫一個中間介 redirectBack，用來在提交表單後自動導回上一頁：

```javascript=
// 自己寫的中間介，用來導回上一頁
function redirectBack(req, res, next) {
  res.redirect('back')
}

app.get('/register', userController.register)
app.post('/register', userController.handleRegister, redirectBack)
app.get('/login', userController.login)
app.post('/login', userController.handleLogin, redirectBack)
app.get('/logout', userController.logout)
```

能夠根據未填欄位、或是資料輸入錯誤，顯示相對應的錯誤訊息：

![](https://i.imgur.com/rS4BspM.png)

執行程式確認註冊和登入功能都沒問題的話，這樣就完成了簡易的會員註冊系統！藉由 MVC 架構，就能夠很清楚的分工，簡化程式邏輯。

---

## 實戰練習：有新增功能的簡易留言板

再來要結合會員系統，實作一個有新增功能的簡易留言版。那麼就趕緊開始吧！

### Step1. 規劃資料庫

在 app database 新增一個 comments table，以 username 欄位和 users table 進行關聯

![](https://i.imgur.com/WcPt20O.png)

### Step2. 新增留言區塊

在首頁 index.ejs 介面新增留言表單，如果是登入狀態，就能看到留言區塊：

```htmlmixed=
<h1>簡易會員系統</h1>

<!-- 以是否有 username 來判斷是否登入 -->
<% if(username) { %>
  <div>Hello, <%= username + '!' %></div>
  <a href="/logout">登出</a>

  <form method="POST" action="/comments">
    <textarea name="content" id="" cols="30" rows="10"></textarea>
    <input type="submit" />
  </form>

<% } else { %>
  <a href="/register">註冊</a>
  <a href="/login">登入</a>
<% } %>
```

畫面看起來會是這個樣子：

![](https://i.imgur.com/KbxPCEY.png)

### Step3. 新增留言功能

1. 新增路由：

```javascript=
const commentController = require('./controllers/comment');

// 提交表單來新增 comment，同樣以 redirectBack 來導回上一頁
app.post('/comments', commentController.add, redirectBack)
```

2. 建立 commentConrtoller 架構：

```javascript=
// 引入 commentModel
const commentModel = require('../models/comment')

const commentController = {
  add: (req, res, next) => {
    const {username} = req.session
    const {content} = req.body
    if (!username) {
      req.flash('errorMessage', '請先登入');
      return next();
    }
    if (!content) {
      req.flash('errorMessage', '缺少必要欄位');
      return next();
    }
    // 若新增失敗就導回首頁
    commentModel.add(username, content, err => {
      return res.redirect('/');
    })
  }
}

// 輸出commentController
module.exports = commentController;

```

3. 建立 commentModel 並新增 add，將表單提交內容寫入資料庫：

```javascript=
const commentModel = {
  // 新增 comment 功能
  add: (username, content, cb) => {
    db.query(
      'INSERT INTO comments(username, content) VALUES(?, ?)',
      [username, content],
      (err, results) => {
        if (err) return cb(err);
        cb(null);
      }
    );
  }
}
```

### Step4. 讀取留言功能

1. 繼續在 commentModel 新增 getAll 來處讀取 username 資料，進行 users 和 comments 資料庫關聯：

```javascript=
  // 讀取 comment 功能
  getAll: (cb) => {
    // 資料庫關聯
    db.query(
      `
      SELECT U.nickname, C.content FROM comments as C
      LEFT JOIN users as U on U.username = C.username       
      `,
      (err, results) => {
      if (err) return cb(err);
      cb(null, results);
    });
  }
```

2. 修改首頁 index 路由：

```javascript=
app.get('/', commentController.index)
```

3. 在 Controller 處理 index 頁面，讀取資料並進行渲染：

```javascript=
  // 在 index 頁面讀取資料
  index: (req, res) => {
    commentModel.getAll((err, results) => {
      if (err) {
        console.log(err);
      }
      res.render('index', {
        comments: results;
      });
    });
  }
```

4. 修改首頁 index.ejs 顯示留言部分：

```javascript=
<% comments.forEach(function(comment) { %>
  <div>
    <h2><%= comment.nickname %> <%= comment.created_at %></h2>
    <p><%= comment.content %></p>
  </div>
<% }) %>
```

這樣其實就完成簡易的留言版了！但可以注意到，透過 EJS 語法輸出的時間，和我們預期的 `'MMM-DD-YYYY'` 格式不太相同：

![](https://i.imgur.com/7BWBdzc.png)

### 補充：透過 moment.js 修改時間格式

透過 [moment.js](https://momentjs.com/) 這個套件，我們就能夠制定要輸出的時間日期格式，也能設定不同時區的時間。

1. 安裝 moment.js 套件：

```
npm install moment --save
```

2. 在 index.js 引入套件，並將 moment 和設定好的格式放入 locals，這樣就能在 View 使用：

```javascript=
// 引入 moment 套件
const moment = require('moment');
// 設定時間格式
const shortDateFormat = "YYYY-MM-DD h:mm:ss";
// 將 moment 和 shortDateFormat 放到 locals 全域環境中
app.locals.moment = moment;
app.locals.shortDateFormat = shortDateFormat;
```

3. 修改 index.ejs 的 View，用 moment 語法來輸出時間，也就是 `moment('datetime').format('模版')`，改寫如下：

```javascript=
<% comments.forEach(function(comment) { %>
  <div>
    <h2><%= comment.nickname %> <%= moment(comment.created_at).format(shortDateFormat) %></h2>
    <p><%= comment.content %></p>
  </div>
<% }) %>
```

這樣就成功把時間修改成 "YYYY-MM-DD h:mm:ss" 格式了！

![](https://i.imgur.com/yiiWQeZ.png)

找了幾種引用 moment 的寫法，最後覺得這篇：[How to use node modules (like MomentJS) in EJS views?](https://stackoverflow.com/questions/12794860/how-to-use-node-modules-like-momentjs-in-ejs-views) 內容蠻符合需要的，也就是要如何透過 node 提供的套件來改變 EJS views，稍微嘗試過後，也成功將時間格式調整成想要的樣子！

---

## 實戰：優化留言板

在上述範例中，我們在完成了一個有新增功能簡易留言版，接下來我們要試著把系統變得更完整一點。

### Step1. 思考產品全貌

在開始實作產品內容之前，需要先思考幾個重點：

1. 產品會有哪些功能：新增留言、編輯留言、刪除留言
2. 如何設計路由
 - 刪除留言
   - `GET /delete_comments/:id => /`
  - 編輯留言
    - 跳轉到編輯頁面 `GET /update_comments/:id => form`
    - 編輯完成提交 `POST /update_comments/:id => /`

### Step2. 規劃資料庫結構

規劃好系統功能和路由之後，再來就是規劃資料庫結構，這部分在先前的實作中就完成得差不多了。

在 users 和 comments table 中有個同樣的欄位 username，透過這個外鍵（Foreign Key），我們就能夠進行資料庫關聯，拿到想要的資料形式。

### Step3. 實作刪除功能

1. 在 index.ejs 新增刪除按紐，並且只有留言本人才看得到刪除鍵：

```htmlmixed=
<% comments.forEach(function(comment) { %>
  <div>
    <h2><%= comment.nickname %> <%= moment(comment.created_at).format(shortDateFormat) %></h2>
    <p><%= comment.content %></p>
    <!-- 判斷使用者的 username 和 comment 的 username 是否相同-->
    <% if (username === comment.username) {%>
      <!-- 只有本人才會看到刪除鍵 -->
      <a href="/delete_comments/<%= comment.id %>">刪除</a>
    <% } %>
  </div>
<% }) %>
```

2. 修改 commentModel 來拿取需要的資料，也就是 comment table 的 id 和 username：

```javascript=
  // 讀取 comment 功能
  getAll: (cb) => {
    // 資料庫關聯
    db.query(
      `
      SELECT U.nickname, U.created_at, C.content, C.id, C.username
      FROM comments as C
      LEFT JOIN users as U on U.username = C.username
      ORDER BY C.created_at DESC       
      `,
      (err, results) => {
      if (err) return cb(err);
      cb(null, results);
    });
  }
```

3. 在 index.js 新增刪除留言的路由：

```javascript=
app.get('/delete_comments/:id', commentController.delete)
```

4. 新增控制 delete 的方法。注意這裡的參數除了網址列上的 id，也需傳入 session 以確認是否為該 comment 作者：：

```javascript=
  delete: (req, res) => {
    // 除了網址列上的 id，也需傳入 session 以確認是否為該 comment 作者
    commentModel.delete(req.session.username, req.params.id, err => {
      res.redirect('/');
    })
  }
```

5. 實作 delete 的 commentModel 部分：

```javascript=
  delete: (username, id, cb) => {
    db.query(
      `
        DELETE FROM comments WHERE id=? AND username=?
      `, [id, username], 
      (err, results) => {
        if (err) return cb(err);
        cb(null);
    });  
  }
```

記得做好權限管裡，只有該則留言的’作者才能進行刪除：

![](https://i.imgur.com/L9lV2x9.png)

### Step4. 實作編輯功能

1. 同樣先從 View 部分開始，在 index.ejs 新增編輯按紐：

```htmlmixed=
    <!-- 判斷使用者的 username 和 comment 的 username 是否相同-->
    <% if (username === comment.username) {%>
      <a href="/update_comments/<%= comment.id %>">編輯</a>
      <a href="/delete_comments/<%= comment.id %>">刪除</a>
    <% } %>
```

2. 在 index.js 新增路由：

```javascript=
app.post('/comments', commentController.add, redirectBack)
app.get('/delete_comments/:id', commentController.delete)
// 讀取要編輯的 comment
app.get('/update_comments/:id', commentController.update)
// 執行修改 comment
app.post('/update_comments/:id', commentController.handleupdate)
```

3. 在新增 Controller 控制相對應的路由前，因為 update 需要讀取相對應 id 的 comment，因此需要在 Model 新增讀取單一 comment 的功能：

```javascript=
  // 讀取相對應 id 的 comment
  get: (id, cb) {
    // 資料庫關聯
    db.query(
      `
      SELECT U.nickname, U.created_at, C.content, C.id, C.username
      FROM comments as C
      LEFT JOIN users as U on U.username = C.username
      WHERE C.id = ?     
      `, [id],
      (err, results) => {
        if (err) return cb(err);
        // 如果結果是 undefined 就會傳空物件，可避免程式出現錯誤
        cb(null, results[0] || {});
      }
    );
  },
```

4. 接著就可以繼續編輯 Controller 中的 update：

```javascript=
  update: (req, res) => {
    commentModel.get(req.params.id, (err, result) => {
      res.render('update', {
        comment: result
      });
    });
  },
```

5. 再來實作 update 的 View 部分，因為直接修改網址就能進到這個頁面，要記得做驗證權限，必須是作者本人才能修改留言，這是透過前端處理：

```htmlmixed=
<h1>編輯留言</h1>

<a href="/">返回</a>

<!-- 前端驗證 -->
<% if (username === comment.username) { %>
  <form method="POST" action="/update_comments/<%= comment.id %>">
    <textarea name="content" id="" cols="30" rows="10"></textarea>
    <input type="submit" />
  </form>
<% } else { %>
  <div>ノンノンだよ！你沒有修改權限！</div>
<% } %>
```

- 有修改權限

![](https://i.imgur.com/GyCcRFg.png)

- 沒有修改權限

![](https://i.imgur.com/J1iF7Ri.png)

6. 最後剩下 handelUpdate 處理表單提交部分，首先是控制路由部分，透過比對 session 和 params.id 確認是否為本人，並傳入要修改的留言內容：

```javascript=
  handleUpdate: (req, res) => {
    // 後端驗證: 必須是本人才有權限修改
    commentModel.update(req.session.username, req.params.id, req.body.content, err => {
      res.redirect('/')
    });
  }
```

7. 然後修改 Model 處理資料：

```javascript=
  update: (username, id, content, cb) => {
  db.query(
    `
      UPDATE comments SET content=? WHERE  id=? AND username=?
    `, [content, id, username],
    (err, results) => {
      if (err) return cb(err);
      cb(null);
    });
  }
```

這樣就完成具有新增、刪除和編輯功能的留言板了！再來我們要學習如何美化頁面，也就是 EJS 如何修改 template 來改變畫面。

### Step5. 美化頁面

透過 [EJS]((https://ejs.co/#install)) 提供的語法 `include()` 我們可以建立模版，獨立出每個頁面都有的元素，例如 head 和 navbar 等等，如此就能簡化程式碼，範例如下：

```htmlmixed=
<%- include('header'); -%>
<h1>
  Title
</h1>
<p>
  My page
</p>
<%- include('footer'); -%>
```

此外，include() 也可以傳入參數：

```htmlmixed=
<ul>
  <% users.forEach(function(user){ %>
    <%- include('user/show', {user: user}); %>
  <% }); %>
</ul>
```

認識新語法之後，就來繼續修改畫面吧！

1. 首先在 index.ejs 加入 include() 語法，就像平常寫的 html 的架構。注意這裡的 `<%- %>` 是減號，代表會經過 html 解析：

```htmlmixed=
<html>
<head>
  <%- include('template/head') %>
</head>

  <h1>簡易會員系統</h1>
  
  <h2><%= errorMessage %></h2>
  
  <% if(username) { %>
  <div>Hello, <%= username + '!' %></div>
  <a href="/logout">登出</a>
  
  <form method="POST" action="/comments">
    <textarea name="content" id="" cols="30" rows="10"></textarea>
    <input type="submit" />
  </form>
  
  <% } else { %>
  <a href="/register">註冊</a>
  <a href="/login">登入</a>
  <% } %>
  
  <% comments.forEach(function(comment) { %>
  <div>
    <h2><%= comment.nickname %> <%= moment(comment.created_at).format(shortDateFormat) %></h2>
    <p><%= comment.content %></p>
    <% if (username === comment.username) {%>
    <a href="/update_comments/<%= comment.id %>">編輯</a>
    <a href="/delete_comments/<%= comment.id %>">刪除</a>
    <% } %>
  </div>
  <% }) %>

</html>
```

2. 再來編輯 head.ejs 部分，引入 [Bootstrap](https://getbootstrap.com/docs/4.5/getting-started/introduction/) 提供的樣式和 meta tags：

```htmlmixed=
<!-- Required meta tags -->
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

<!-- Bootstrap CSS -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css"
integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
```

成功引入之後，就會發現畫面套用了 Bootstrap 提供的樣式：

![](https://i.imgur.com/EUjQqMo.png)

3. 再來是 navbar，同樣在 index.ejs 中加上 include() 語法：

```htmlmixed=
<%- include('template/navbar') %>
```

4. 新增模版 navbar.ejs，可在 [Bootstrap/navbar](https://getbootstrap.com/docs/4.5/components/navbar/) 找合適的模版來套用，並且把之前在 index.ejs 做的登入驗證同樣放到這裡，加上對應的路徑：

```htmlmixed=
<nav class="navbar navbar-dark bg-dark">
  <a class="navbar-brand" href="/" >這是一個留言版</a>

  <div>
    <% if(username) { %>
      <div class="">
        <span class="text-light">Hello, <%= username + '!' %></span>
        <a class="btn btn-outline-light my-2 my-sm-0" href="/logout">登出</a>
      </div>
    <% } else { %>
      <a class="btn btn-outline-light my-2 my-sm-0" href="/login">登入</a>
      <a class="btn btn-outline-warning my-2 my-sm-0" href="/register">註冊</a>
    <% } %>
  </div>
</nav>
```

效果會長這樣：

![](https://i.imgur.com/sepdlt7.png)

此外，像 head.ejs、navbar 這些通用模版，可放在 views/template 資料夾統一管理：

![](https://i.imgur.com/2YdWdMx.png)

5. 接著來修改註冊和登入頁面，同樣可在 Bootstrap 找合適的 [forms](https://getbootstrap.com/docs/4.5/components/forms/) 版型：

- register.ejs 註冊頁面

```htmlmixed=
<!DOCTYPE html>
<html>
<head>
  <%- include('../template/head') %>
</head>
  <%- include('../template/navbar') %>
  <div class="container">
    <!-- 若沒有錯誤會回傳一個空陣列 [] -->
    <% if (errorMessage.length > 0) { %>
      <div class="alert alert-danger" role="alert">
        <%= errorMessage %>
      </div>
    <% } %>
    <form method="POST" action="/register">
      <div class="form-group row">
        <label class="col-sm-2 col-form-label">Username: </label>
        <div class="col-sm-10">
          <input class="form-control" name="username">
        </div>
      </div>
      <div class="form-group row">
        <label class="col-sm-2 col-form-label">Nickname: </label>
        <div class="col-sm-10">
          <input class="form-control" name="nickname">
        </div>
      </div>
      <div class="form-group row">
        <label class="col-sm-2 col-form-label">Password: </label>
        <div class="col-sm-10">
          <input class="form-control" name="password" type="password">
        </div>
      </div>
      <button type="submit" class="btn btn-dark mb-2">Submit</button>
    </form>
  </div>
</html>
```

- login.ejs 登入頁面

```htmlmixed=
<!DOCTYPE html>
<html>
<head>
  <%- include('../template/head') %>
</head>
  <%- include('../template/navbar') %>

  <div class="container">
    <!-- 若沒有錯誤會回傳一個空陣列 [] -->
    <% if (errorMessage.length > 0) { %>
    <div class="alert alert-danger" role="alert">
      <%= errorMessage %>
    </div>
    <% } %>
    <form method="POST" action="/login">
      <div class="form-group row">
        <label class="col-sm-2 col-form-label">Username: </label>
        <div class="col-sm-10">
          <input class="form-control" name="username">
        </div>
      </div>
      <div class="form-group row">
        <label class="col-sm-2 col-form-label">Password: </label>
        <div class="col-sm-10">
          <input class="form-control" name="password" type="password">
        </div>
      </div>
      <button type="submit" class="btn btn-dark mb-2">Submit</button>
    </form>
  </div>

</html>
```

兩者寫法其實差不多，只有欄位和路徑需要修改，畫面效果如下：

![](https://i.imgur.com/ySLeqVp.png)

6. 再來是首頁 index.ejs，同樣幫 forms 還有留言卡套用樣式：

```htmlmixed=
<!DOCTYPE html>
<html>
<head>
  <%- include('template/head') %>
</head>
  <%- include('template/navbar') %>

  <div class="container mx-auto mt-3" style="width: 100%;">
    <% if (errorMessage.length > 0) { %>
    <div class="alert alert-danger" role="alert">
      <%= errorMessage %>
    </div>
    <% } %>
    
    <% if(username) { %>
    <form method="POST" action="/comments">
      <div class="form-group">
        <div class="text-dark">留言內容</div>
        <textarea name="content" class="form-control"></textarea>
      </div>
      <button type="submit" class="btn btn-dark">提交</button>
    </form>
    <% } else { %>
      <div class="alert alert-warning" role="alert">留言請先登入</div>
    <% } %>
    
    <div class="card-container d-flex flex-wrap">
      <% comments.forEach(function(comment) { %>
        <div class="card m-2" style="width: 18rem; height: 16rem;">
          <div class="card-header">
            <h5 class="card-title"><%= comment.nickname %></h5>
            <h6 class="card-subtitle text-muted"><%= moment(comment.created_at).format(shortDateFormat) %></h6>
            <% if (username === comment.username) {%>
            <a href="/update_comments/<%= comment.id %>" class="card-link">編輯</a>
            <a href="/delete_comments/<%= comment.id %>" class="card-link">刪除</a>
            <% } %>
          </div>
          <div class="card-body" style="overflow:scroll; overflow-x:hidden;">
            <p class="card-text" ><%= comment.content %></p>
          </div>
        </div>
      <% }) %>
    </div>

  </div>
</html>
```

效果如下：

![](https://i.imgur.com/AVp2KF7.png)

6. 最後是編輯留言頁面 update.ejs，也和其他頁面版型差不多：

```htmlmixed=
<!DOCTYPE html>
<html>

<head>
  <%- include('template/head') %>
</head>
  <%- include('template/navbar') %>

  <div class="container mx-auto mt-4" style="width: 100%;">
    <!-- 若沒有錯誤會回傳一個空陣列 [] -->
    <% if (errorMessage.length > 0) { %>
    <div class="alert alert-danger" role="alert">
      <%= errorMessage %>
    </div>
    <% } %>

    <% if (username === comment.username) { %>
      <form method="POST" action="/update_comments/<%= comment.id %>">
        <div class="form-group">
          <div class="text-dark">編輯留言</div>
          <textarea name="content" class="form-control"></textarea>
        </div>
        <button type="submit" class="btn btn-dark">提交</button>
      </form>
    <% } else { %>
      <div>ノンノンだよ！你沒有修改權限！</div>
    <% } %>
  </div>

</html>
```

![](https://i.imgur.com/1JoojMH.png)

透過這個範例，我們學到該如何使用 include() 語法來引入模板，如此就能簡化程式碼，將重複使用的區塊給模組化；此外，也透過 BootStrap 提供的現成樣式，讓我們能快速修改頁面，達到想要的畫面效果。

## 結語

實際跟著範例操作之後，會發現透過 MVC 模式撰寫程式，思考邏輯會很類似。使用 Express 框架實作網頁時，會依照下方流程進行：

1. 思考產品全貌：哪些功能？哪些路由？
2. 規劃資料庫結構
3. 載入需要的模組，設定 app 路由部分
4. 依照 MVC 架構撰寫程式碼：
    - 設定 Controller：針對不同路由進行控制
    - 設定 Model：如何處理資料
    - 設定 View：如何呈現畫面

也藉這個機會來複習 BootStrap 使用方式，這和之前「先刻版面再加入功能」的模式正好相反，反而是先把功能做好，再套用現有樣式，這也是藉由 MVC 將畫面和功能分割開來才能夠辦到。

在學會如何寫出一個簡單的會員註冊系統和留言版之後，其實就能夠應用到各種網頁，期待之後能夠熟悉 MVC 架構的寫法！
