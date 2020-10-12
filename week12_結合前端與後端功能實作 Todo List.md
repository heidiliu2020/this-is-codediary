###### tags: `Back-End` `php` `API` `Front-End`
# [week 12] 結合前端與後端功能實作 Todo List

> 本篇為 [[BE101] 用 PHP 與 MySQL 學習後端基礎](https://lidemy.com/p/be101-php-mysql) 這門課程的學習筆記。如有錯誤歡迎指正。

> hw2：[Todo List](http://mentor-program.co/mtr04group2/Heidi/week12/hw2/index.html)

## 前端實作

### 目標功能：

- 新增功能：add
- 刪除功能：delete
- 編輯功能：update
- 更新狀態功能：checked / unchecked
- 切換列表：All / Active / Completed 
- 清除所有 todo：Clear

以上功能只要利用前端就能達成，程式碼如下：

### 前端介面

```htmlmixed=
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Week12 Todo List</title>
  <script src="https://code.jquery.com/jquery-3.5.1.js"></script>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
    integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
  <link rel="stylesheet" href="css/style.css">
</head>

<body>

  <div class="wrapper">
    <div class="todo__header">
      <button type="button" class="btn btn-save" href="#">Save</button>
      <button type="button" class="btn clear-all" href="#">Reset</button>
    </div>
    <h1>Todo List</h1>
    <div class="todo__input-block">
      <input class="todo__input" type="text" placeholder="Add New Todo Here..." minlength="1" maxlength="128">
      <button class="btn-new"></button>
    </div>
  
    <ul class="nav nav-middle justify-content-center todo__status">
      <li class="nav-item">
        <a class="nav-link active" href="#" data-filter="all">All</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" data-filter="in-progress">In Progress</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#" data-filter="completed">Completed</a>
      </li>
    </ul>

    <ul class="todo__list">
      <!-- 要新增 template 的區塊 -->
      <li class="todo">
        <input class="todo__check" type="checkbox" id="todo-0">
        <label class="todo__title" for="todo">Coding</label>
        <button class="btn-delete"></button>
      </li>
    </ul>
  </div>

 
</body>
</html>
```

### 前端 JavaScript

```javascript=
<script>
  let id = 1; 

  const template = `
    <li class="todo">
      <input class="todo__check" type="checkbox" id="todo-{id}">
      <label class="todo__title" for="todo-{id}">{content}</label>
      <button class="btn-delete"></button>
    </li>
  `
  // 新增功能: 點擊
  $('.btn-new').click(() => {
    addTodo()
  });
  // 新增功能: 按 Enter
  $('.todo__input').keydown(e => {
    if (e.key === 'Enter') {
      addTodo()
    }
  });

  // 刪除功能: 利用事件代理
  $('.todo__list').on('click', '.btn-delete', (e) => {
    $(e.target).parent().remove();
  });

  // 標記狀態: 已完成 / 未完成
  $('.todo__list').on('change', '.todo__check', (e) => {
    const target = $(e.target);
    const isChecked = target.is(":checked");
    if (isChecked) {
      target.parents('.todo').addClass('checked');
    } else {
      target.parents('.todo').removeClass('checked');
    }
  });

  // 篩選 todo 狀態
  $('.todo__status').on('click', 'a', e => {
    const target = $(e.target);
    const filter = target.attr('data-filter');
    $('.todo__status a.active').removeClass('active');
    target.addClass('active');

    if (filter === 'all') {
      $('.todo').show();
    } else if (filter === 'in-progress') {
      $('.todo').show();
      $('.todo.checked').hide();
    } else {        // completed
      $('.todo').hide();
      $('.todo.checked').show();
    }
  });

  // 清除所有 todo
  $('.clear-all').click(() => {
    $('.todo').remove();
  });

  // 儲存 todo
  $('.btn-save').click(() => {
    let todos = [];
    $('.todo').each((i, element) => {
      const input = $(element).find('.todo__check');
      const label = $(element).find('.todo__title');
      todos.push({
        id: input.attr('id').replace('todo-', ''),      // 把 todo-id 的 todo- 換成空字串
        content: label.text(),
        isDone: $(element).hasClass('checked')
      });    
    });
    JSON.stringify(todos);       // 將 JS 物件轉換成 JSON 字串
  });

  function addTodo() {
    const value = $('.todo__input').val();
    if (!value) return;
    $('.todo__list').prepend(
      template
        .replace('{content}', escape(value))
        .replace(/{id}/g, id)
    );
    id += 1;
    $('.todo__input').val('');
  }

  function escape(toOutput) {
    return toOutput
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#039;');
  }
</script>
```

## 前後端串接

### 目標功能

- 儲存功能：Save

### PHP API

將前端得到的資料 `JSON.stringify(todos)`，利用 Ajax 方式 POST 到資料庫：

```javascript=
// 儲存 todo
$('.btn-save').click(() => {
  let todos = [];
  $('.todo').each((i, element) => {
    const input = $(element).find('.todo__check');
    const label = $(element).find('.todo__title');
    todos.push({
      id: input.attr('id').replace('todo-', ''),      // 把 todo-id 的 todo- 換成空字串
      content: label.text(),
      isDone: $(element).hasClass('checked')
    });    
  });
  const data = JSON.stringify(todos);       // 將 JS 物件轉換成 JSON 字串
  $.ajax({
    type: 'POST',
    url: 'http://localhost/heidi/week12_local/hw2/api_add_todo.php',
    data: {
      todo: data
    },
    success: function(resp) {
      const respId = resp.id
      window.location = 'index.html?id=' + respId;
    },
    error: function () {
      alert('Error!');
    }
  });
});
```

如此即可利用前端 JavaScript 從後端拿取 JSON 格式的資料，並用 `JSON.parse()` 將 JSON 字串轉換成 JavaScript 物件：

```javascript=
// URLSearchParams(): 解析網址參數
const serchParams = new URLSearchParams(window.location.search);  // ?id=...
const todoId = serchParams.get('id');

if (todoId) {
  $.getJSON('http://localhost/heidi/week12_local/hw2/api_get_todo.php?id=' + todoId, function (data) {
    const todos = JSON.parse(data.data.todo);
    restoreTodos(todos);
  });
}
```

將拿到的資料再以 JS 處理新增到頁面，把模版 template 加上 content、id、todoClass：

```javascript=
function restoreTodos(todos) {
  if (todos.length === 0) return;
  // id 要從讀取的最後一個 todo id 繼續增加
  id = todos[todos.length - 1].id + 1;
  for(let i = 0; i < todos.length; i++) {
    const todo = todos[i];
    $('.todo__list').prepend(
      template
        .replace('{content}', escape(todo.content))
        .replace(/{id}/g, todo.id)
        .replace('{todoClass}', todo.isDone ? 'checked' : '')
    );
  }
}
```

---

## Single Page Application

Single Page Application（單頁面應用程式），簡稱 SPA。是前端利用 Ajax 以非同步方式串接後端 API，如此可將前後端分離，在交換資料時不需換頁，可透過動態方式更新部分頁面。

而早期的網頁主要採用 Multiple Page Application（多頁式應用程式）設計，與 SPA 概念相對應，每次交換資料時都需換頁。
 

## SPA 的優缺點

### 優點

1. 增進使用者體驗
不需換頁即可載入新的資訊。例如 Gmail 或影音播放網站，可以在播放音樂的同時，繼續瀏覽網站其他資訊。
2. 前後端分離
後端只需負責制定 API 文件，提供前端資料。前端則利用 Ajax 從後端拿取資料，並以 JavaScript 在 html 動態產生內容。

### 缺點

1. SEO（搜尋引擎最佳化）較差
由於 SPA 是利用 JavaScript 動態產生內容，檢視原始碼會發現原始內容是空的，
解決方法：第一次頁面由 Server side render，之後的操作都改用 Client side render，就可以保證搜尋引擎也能爬到完整的 HTML。
2. 前端工作複雜化
原先是利用不同路由處理不同功能，改成由單一頁面統一管理，就像在網頁上實作 APP。
3. 初次載入頁面費時
初次瀏覽頁面時會需要下載 JavaScript 或是其他頁面的 template。

## 由後端負責提供只輸出資料的 API vs PHP 直接輸出內容

### 後端負責提供只輸出資料的 API
- Server 端接收到請求，會回傳 JSON 或其他特定格式的資料給前端，瀏覽器再將資料動態更新至頁面
- 因為是動態產生資料，檢視原始碼會發現動態更新的內容是空的

### PHP 直接輸出內容
- Server 端接收到請求，會將所需資料與頁面經處理後回傳 html 檔給前端，瀏覽器透過重整頁面顯示
- 因此回傳的頁面，檢視原始碼是有包含資料的

參考資料：
- [跟著小明一起搞懂技術名詞：MVC、SPA 與 SSR](https://medium.com/@hulitw/introduction-mvc-spa-and-ssr-545c941669e9)
- [前後端分離與 SPA](https://blog.techbridge.cc/2017/09/16/frontend-backend-mvc/)
- [Day20– 前端小字典三十天【每日一字】– SPA](https://ithelp.ithome.com.tw/articles/10160709)

