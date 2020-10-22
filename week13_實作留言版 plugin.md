###### tags: `Back-End` `webpack` `jQuery`
# [week 13] MTR04 - 實作留言版 plugin

> 本篇為 [[MTR04] 第十三週 - 帶著做留言版 plugin](https://lidemy.com/p/mtr04) 的學習筆記。如有錯誤歡迎指正。

## 前置作業

需安裝好 webpack 以及其他套件，可跟著官方教學步驟 [Getting Started](https://webpack.js.org/guides/getting-started/) 進行安裝：

```
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

#### 安裝使用 jquery

```
npm install jquery webpack-cli --save-dev
```

#### 安裝 babel-loader

```
npm install -D babel-loader @babel/core @babel/preset-env
```

## 目標：建立留言版 plugin

也就是修改 week12 實作的留言版，將 index.html 中的內容全部以 JavaScript 形式匯入。

### 步驟一：init() 初始化

```javascript=
commentPlugin.init({
  apiURL: '',
  siteKey: '',
  containerSelector: '#comments'
})
```

### 步驟二：動態新增頁面

將 UI 介面以及 CSS 樣式，同樣使用 JavaScript 來動態新增：

```javascript=
let siteKey = '';
let apiUrl = '';
let containerElement = null;
let commentDOM = null;
let lastId = null;    // before
let isEnd = false;    // 確認是否拿完資料

const css = '.add-comment-form { margin-bottom: 10px; } .card { margin-bottom: 10px; } .card-title {  word-wrap:break-word; } .load-more { margin-bottom: 10px; }'
const loadMoreButtonHTML = '<button class="load-more btn btn-dark">載入更多</button>';

// UI 介面的模板
const formTemplate = `
  <div>
    <form class="add-comment-form">
      <div class="form-group">
        <label for="form-nickname">暱稱</label>
        <input name="nickname" type="text" class="form-control" id="form-nickname" >
      </div>
      <div class="form-group">
        <label for="content-textarea">留言內容</label>
        <textarea name="content" class="form-control" id="exampleFormControlTextarea1" rows="3"></textarea>
      </div>
      <button type="submit" class="btn btn-dark">送出</button>
    </form>
    <div class="comments"></div>
  </div>
`

// 初始化: 動態匯入表單
function init(options) {
  siteKey = options.siteKey;
  apiUrl = options.apiUrl;
  containerElement = $(options.containerSelector);
  containerElement.append(formTemplate);
  // 動態新增 css 樣式
  const styleElement = document.createElement('style');
  styleElement.type = 'text/css';
  styleElement.appendChild(document.createTextNode(css));
  document.head.appendChild(styleElement)

  commentDOM = $('.comments');
  getComments();

  // 載入更多: 以事件代理的方式處理 click 事件 
  $('.comments').on('click', '.load-more', () => {
    getComments();
  });

  // 新增留言 ->  將資料存到後端
  $('.add-comment-form').submit(e => {
    e.preventDefault();             // 取消原生行為 -> 不會送出表單
    const newCommentData = {
      'site_key': siteKey,         // 全域變數的 siteKey
      'nickname': $('input[name=nickname]').val(),
      'content': $('textarea[name=content]').val()
    }
    $.ajax({
      type: 'POST',
      url: `${apiUrl}/api_add_comments.php`,
      data: newCommentData
    }).done(function (data) {        // done(): 以函數處理回傳的 data 資料
      if (!data.ok) {
        alert(data.message);
        return;
      }
      $('input[name=nickname]').val('');
      $('textarea[name=content]').val('');
      // 新增留言後以 JS 動態方式加到最上方
      appendCommentToDOM(commentDOM, newCommentData, true);
    });
  });
}

// 在 DOM 結構準備好後，再進行初始化
$(document).ready(() => {
  init({
    siteKey: 'heidi',
    apiUrl: 'http://localhost/heidi/week13_local/hw2',
    containerSelector: '.comment-area'
  });
});
```

如此即可將 `<script>` 區塊的程式碼全部放到 src\index.js，再利用 webpack 進行下列步驟。

### 步驟三：進行模組化

將不同功能模組化來重構程式碼，以便後續管理。

1. 在 src 資料夾建立 api.js

```javascript=
import $ from 'jquery';

export function getComments(apiUrl, siteKey, before, cb) {
  let showURL = `${apiUrl}/api_comments.php?site_key=${siteKey}`;
  if (before) {
    showURL += '&before=' + before;
  }
  $.ajax({
    url: showURL
  }).done(function (data) {
    cb(data);
  });
}

export function addComments(apiUrl, siteKey, before, cb) {
  $.ajax({
    type: 'POST',
    url: `${apiUrl}/api_add_comments.php`,
    data
  }).done(function (data) {
    cb(data)
  });
}
```

2. 建立 template.js

```javascript=
export const cssTemplate = '.add-comment-form {margin - bottom: 10px; } .card {margin - bottom: 10px; } .card-title {word - wrap:break-word; } .load-more {margin - bottom: 10px; }'
export const loadMoreButtonHTML = '<button class="load-more btn btn-dark">載入更多</button>';

// UI 介面的模板
export const formTemplate = `
  <div>
    <form class="add-comment-form">
      <div class="form-group">
        <label for="form-nickname">暱稱</label>
        <input name="nickname" type="text" class="form-control" id="form-nickname" >
      </div>
      <div class="form-group">
        <label for="content-textarea">留言內容</label>
        <textarea name="content" class="form-control" id="exampleFormControlTextarea1" rows="3"></textarea>
      </div>
      <button type="submit" class="btn btn-dark">送出</button>
    </form>
    <div class="comments"></div>
  </div>
`
```

3. 建立 utils.js

```javascript=
export function escape(toOutput) {
  return toOutput
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#039;');
}

// 渲染 comment: 處理讀取的資料 & 決定加在最前面或最後面
export function appendCommentToDOM(container, comment, isPrepend) {
  const html = `
    <div class="card">
    <div class="card-body">
      <h5 class="card-title">${escape(comment.nickname)}</h5>
      <p class="card-text">${escape(comment.content)}
      </p>
    </div>
    </div>
  `;
  if (isPrepend) {
    container.prepend(html);  // 新增到最上方
  } else {
    container.append(html);   // 新增到最底部
  }
}
```

4. 並在 index.js 引入上述檔案：

```javascript=
import { getComments, addComments } from './api';
import { appendCommentToDOM } from './utils';
import { cssTemplate, loadMoreButtonHTML, formTemplate } from './template';
import $ from 'jquery';

let siteKey = '';
let apiUrl = '';
let containerElement = null;
let commentDOM = null;
let lastId = null;    // before
let isEnd = false;    // 確認是否拿完資料

// 在 DOM 結構準備好後，再進行初始化
$(document).ready(() => {
  init({
    siteKey: 'heidi',
    apiUrl: 'http://localhost/heidi/week13_local/hw2',
    containerSelector: '.comment-area'
  });
});

// 初始化: 動態匯入表單
function init(options) {
  siteKey = options.siteKey;
  apiUrl = options.apiUrl;
  containerElement = $(options.containerSelector);
  containerElement.append(formTemplate);
  // 動態新增 css 樣式
  const styleElement = document.createElement('style');
  styleElement.type = 'text/css';
  styleElement.appendChild(document.createTextNode(cssTemplate));
  document.head.appendChild(styleElement)

  commentDOM = $('.comments');
  getNewComments();

  // 載入更多: 以事件代理的方式處理 click 事件
  $('.comments').on('click', '.load-more', () => {
  getNewComments();
  });

  // 新增留言 ->  將資料存到後端
  $('.add-comment-form').submit(e => {
  e.preventDefault();             // 取消原生行為 -> 不會送出表單
    const newCommentData = {
     'site_key': siteKey,         // 全域變數的 siteKey
      'nickname': $('input[name=nickname]').val(),
      'content': $('textarea[name=content]').val()
    }
    addComments(apiUrl, siteKey, newCommentData, data => {
      if (!data.ok) {
        alert(data.message);
        return;
      }
      $('input[name=nickname]').val('');
      $('textarea[name=content]').val('');
      appendCommentToDOM(commentDOM, newCommentData, true);
    });
  });
}

function getNewComments() {
const commentDOM = $('.comments');
$('.load-more').hide();       
  if (isEnd) {
    return; 
  }
  getComments(apiUrl, siteKey, lastId, data => {
    if (!data.ok) {
      alert(data.message);
      return;
    }
    // 若 request 成功讀取資料
    const comments = data.discussions;
    for (let comment of comments) {
      appendCommentToDOM(commentDOM, comment);
    }

    let length = comments.length;
    // 沒有 lastId: 若初始頁面留言 < 5 直接返回
    if (length < 5) {
      return;
    }
    // 有 lastId: 若拿完資料就隱藏按鈕
    if (length === 0) {
      isEnd = true;
      $('.load-more').hide();
    } else {
      lastId = comments[length - 1].id;
      $('.comments').append(loadMoreButtonHTML);   // 新增 "載入更多" 按鈕
    }
  });
}
```

### 步驟四：使用 webpack 打包

將上述檔案進行打包，會在 dist 資料夾建立 main.js，接著回到 index.html 引入該檔案：

```javascript=
<script src="./dist/main.js"></script>
```

### 步驟五：引入 library

> 可參考官方文件：https://webpack.js.org/guides/author-libraries/

1. 將 index.js 的 `init()` 改為 export：

```javascript=
export function init(options) {
  ...
}
```

2. 改在 index.html 引入 library：

```javascript=
<script>
  $(document).ready(() => {
    commentPlugin.init({
      siteKey: 'heidi',
      apiUrl: 'http://localhost/heidi/week13_local/hw2',
      containerSelector: '.comment-area'
    });
  });
</script>
```

2. 並在設定檔 webpack.config.js 的 output 加上`library: 'commentPlugin',`

### 步驟六：執行 webpack 打包

利用 webpack 將檔案打包成一個 module，即可在 index.html 引入 library。在瀏覽器上開啟頁面，會發現多出全域變數 commentPlugin：

![](https://i.imgur.com/3Vf60Fy.png)

---

## 優化程式碼

接著要優化先前寫的程式碼，例如修改 plugin 的 class 名稱，避免不同使用者（siteKey）發生衝突。

- 修改 template.js 中的 classname 

```javascript=
// 加上不同的 classname，避免不同使用者共用 plugin 發生衝突
export function getLoadMoreButton(classname) {
  return `<button class="${classname} load-more btn btn-dark">載入更多</button>`;
}

// UI 介面的模板
export function getForm(formClassName, commentsClassName) {
  return `
  <div>
    <form class=${formClassName}>
      <div class="form-group">
        <label>暱稱</label>
        <input name="nickname" type="text" class="form-control">
      </div>
      <div class="form-group">
        <label>留言內容</label>
        <textarea name="content" class="form-control" rows="3"></textarea>
      </div>
      <button type="submit" class="btn btn-dark">送出</button>
    </form>
    <div class="${commentsClassName}"></div>
  </div>
  `
}
```

- index.js 中，同樣使用變數來取代字串

```javascript=
import { getComments, addComments } from './api';
import { appendCommentToDOM, appendStyle } from './utils';
import { cssTemplate, getLoadMoreButton, getForm } from './template';
import $ from 'jquery';

// 初始化: 動態匯入表單
export function init(options) {
  let siteKey = '';
  let apiUrl = '';
  let containerElement = null;
  let commentDOM = null;
  let lastId = null;    // before
  let isEnd = false;    // 確認是否拿完資料
  let loadMoreClassName;
  let loadMoreSelector;
  let commentsClassName;
  let commentsSelector;
  let formClassName;
  let formSelector;

  siteKey = options.siteKey;
  apiUrl = options.apiUrl;
  loadMoreClassName = `${siteKey}-load-more`;
  commentsClassName = `${siteKey}-comments`;
  formClassName = `${siteKey}-add-comment-form`;
  loadMoreSelector = '.' + loadMoreClassName;
  commentsSelector = '.' + commentsClassName;
  formSelector = '.' + formClassName;

  containerElement = $(options.containerSelector);
  containerElement.append(getForm(formClassName, commentsClassName));
  appendStyle(cssTemplate)

  commentDOM = $(commentsSelector);
  getNewComments();

  // 載入更多: 以事件代理的方式處理 click 事件
  $(commentsSelector).on('click', loadMoreSelector, () => {
    getNewComments();
  });

  // 新增留言 ->  將資料存到後端
  $(formSelector).submit(e => {
    e.preventDefault();             // 取消原生行為 -> 不會送出表單
    const nicknameDOM = $(`${formSelector} input[name=nickname]`);
    const contentDOM = $(`${formSelector} textarea[name=content]`);
    const newCommentData = {
      site_key: siteKey,         // 全域變數的 siteKey
      nickname: nicknameDOM.val(),
      content: contentDOM.val()
    }
    console.log(formSelector)
    console.log(nicknameDOM, nicknameDOM.val())
    console.log(contentDOM, contentDOM.val())    
    addComments(apiUrl, siteKey, newCommentData, data => {
      if (!data.ok) {
        alert(data.message);
        return;
      }
      nicknameDOM.val('');
      contentDOM.val('');
      appendCommentToDOM(commentDOM, newCommentData, true);
    });
  });

  function getNewComments() {
    const commentDOM = $(commentsSelector);
    $(loadMoreSelector).hide();
    if (isEnd) {
      return;
    }
    getComments(apiUrl, siteKey, lastId, data => {
      if (!data.ok) {
        alert(data.message);
        return;
      }
      const comments = data.discussions;
      for (let comment of comments) {
        appendCommentToDOM(commentDOM, comment);
      }

      let length = comments.length;
      if (length < 5) {
        return;
      }
      if (length === 0) {
        isEnd = true;
        $(loadMoreSelector).hide();
      } else {
        lastId = comments[length - 1].id;
        const loadMoreButtonHTML = getLoadMoreButton(loadMoreClassName);
        $(commentsSelector).append(loadMoreButtonHTML);
      }
    });
  }
}
```
