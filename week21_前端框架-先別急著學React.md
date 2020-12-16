###### tags: `React` `state` `props`
# [week 21] 前端框架 - 先別急著學 React

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 我知道 React 的目的以及原理
 P1 我知道我們為什麼需要 React
 P1 我知道使用 React 跟之前使用 jQuery 的區別
 P1 我理解 state 跟 props 的不同
```

---

## 先別急著學 React

在開始之前，我們先來複習如何用 jQuery 來做出一個簡單的 Todo List 吧！

1. 以下是套用 Bootstrap 切的版型，不過切版部分不是這次要探討的重點：

```htmlmixed=
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css"
    integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
```

<iframe height="324" style="width: 100%;" scrolling="no" title="[week21] Todo List template" src="https://codepen.io/heidiliu2020/embed/preview/vYKoNJK?height=324&theme-id=dark&default-tab=html,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/vYKoNJK'>[week21] Todo List template</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

2. 接著引入使用 jQuery：

```javascript=
<script src="http://code.jquery.com/jquery-latest.js"></script>
```

3. 就可以來新增功能了：
- 新增
- 切換已完成/未完成
- 刪除

<iframe height="265" style="width: 100%;" scrolling="no" title="[week21] Todo List template" src="https://codepen.io/heidiliu2020/embed/preview/vYKoNJK?height=265&theme-id=dark&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/vYKoNJK'>[week21] Todo List template</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 初探 Component

根據 [React](https://zh-hant.reactjs.org/docs/components-and-props.html) 官網定義：

> Component 使你可以將 UI 拆分成獨立且可複用的程式碼，並且專注於各別程式碼的思考。

簡單來說，component（元件、組件）其實就像 JavaScript 的 function，可接收任意的 props（參數， property 的簡寫）並且回傳描述畫面的 React element。

以我們剛才實作的 todolist 來說，就可以根據功能分成不同的 component：

![](https://i.imgur.com/XYSTyfM.png)

### Component & Props

再來我們可試著用 component 的概念改寫程式碼，例如切割出 Todo 和 Button component。

1. 必須用英文大寫開頭命名來辨別是 component：
　
```javascript=
// Todo component: 用來回傳 html 內容, 用大寫英文開頭
function Todo(content) {
  return `
    <li class="todo list-group-item d-flex justify-content-between align-items-center">
      <div class="todo-title ">${content}</div>
      <div class="btn-group">
        ${Button('btn-undone btn-outline-secondary', '未完成')}
        ${Button('btn-delete btn-outline-danger', '刪除')}
      </div>
    </li>
  `
}
// Button component
function Button(className, content) {
  return `
    <button class="btn ${className}" type="button">${content}</button>
  `
}
```

2. 監聽事件就可改成 Button component 寫法，比起原本的 HTML 程式碼，用 component 寫法會更容易閱讀：

```javascript=
$('.todos').on('click', '.btn-undone', e => {
  const todo = $(e.target).parent();
  e.target.remove();
  todo.prepend(
    Button('btn-done btn-outline-success', '已完成')
  );
}); 

$('.todos').on('click', '.btn-done', e => {
  const todo = $(e.target).parent();
  e.target.remove();
  todo.prepend(
    Button('btn-undone btn-outline-secondary', '未完成')
  );
}); 
```

3. 但其實這個寫法還能再做優化，像是 Button() 如果要再傳入更多參數，就會很難快速判斷。因此我們可以改成傳入物件，也就是 props（參數）來簡化：

```javascript=
// Todo component: 傳入物件作為參數
function Todo(props) {
  return `
    <li class="todo list-group-item d-flex justify-content-between align-items-center">
      <div class="todo-title ">${props.content}</div>
      <div class="btn-group">
        ${Button({
          className: 'btn-undone btn-outline-secondary',
          content: '未完成'
          })}
        ${Button({
          className: 'btn-delete btn-outline-danger',
          content: '刪除'
          })}
      </div>
    </li>
  `
}
// Button component: 傳入物件作為參數
function Button(props) {
  return `
    <button class="btn ${props.className}" type="button">${props.content}</button>
  `
}
```

監聽事件也同樣以物件的形式修改，完整程式碼與執行畫面如下：

<iframe height="265" style="width: 100%;" scrolling="no" title="[week21] Todo List template_component" src="https://codepen.io/heidiliu2020/embed/preview/dypbKLw?height=265&theme-id=dark&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/dypbKLw'>[week21] Todo List template_component</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

透過這種寫法，我們能夠將重複的區塊變成模板，建立統一的規則來封裝，也會提高程式碼的可讀性。

但 component 其實是一種抽象的概念，還有另外一種較為標準的寫法 [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)，類似建立新的 HTML 標籤來使用，只是目前瀏覽器支援度還不高，這部分我們並不進行討論。

## 資料 vs 畫面（UI）

根據前面的範例，是把資料放到畫面上來呈現，也就是在新增、修改 todo 時，同時去更動資料和畫面。因此我們可從 UI 去抽取資料，但我們無法直接把 HTML 程式碼存到資料庫，而是要去除標籤，轉換成 JSON 等最原始的資料形式。

此外，透過這種方式其實有個壞處，假如有部分資料沒有更動到，就可能導致資料與畫面不一致。

![](https://i.imgur.com/TKkUPnR.png)

至於要如何解決這個問題，我們可以改成直接修改資料，然後畫面從資料產生，即可保持資料與畫面的一致性。

![](https://i.imgur.com/k1STU0n.png)

這其實就是 React 第二個重要概念 State，畫面永遠由 state 產生。用數學式可表示成 `UI = f(state)`，代表 state 不變的話，透過 state 產生的 UI 也不會改變。

## 畫面永遠都由 state 產生

根據 [React](https://zh-hant.reactjs.org/docs/react-component.html#state) 官網定義：

> State 包含了某個 component 內特定的、會隨時間改變的資料，這個 state 是由使用者定義的。它應是一個簡單的 JavaScript object。

> 如果某個值並沒有在 render 或資料流中被使用（例如計時器的 ID），你不需要將它放在 state 內。

繼續舉剛才的範例，我們可以把 todos 放到 state 物件中，並且將 Todo component 中的 todo 加上 id：

```javascript=
  let id = 0;
  let state = {
    todos: []
  }
  
  function Todo(props) {
    // 自訂屬性通常以 data- 開頭
    return `
      <li class="data-id="${id}">
       ...
    `
```

- 新增 todo：透過 updateState() 來更新資料

```javascript=
  $('.btn-add').click(() => {
    const content = $('.input-todo').val();
    if (!content) return;
    $('.input-todo').val('');
    // 更新 state
    updateState({
      todos: [...state.todos, {
        id,
        content,
        isDone: false
      }]
    });
    id++;
  });
```

- 呼叫 updateState() 來更新資料，再根據資料 render 出畫面：

```javascript=
// 更新 state
function updateState(newState) {
  state = newState;
  render();
}

function render() {
  // 先把畫面清空
  $('.todos').empty();
  $('.todos').append(
    // 把每個 todo 的 HTML 集合起來放到畫面上
    state.todos.map(todo => Todo(todo)).join('')
  );
}
```

- 刪除 todo：透過篩選掉資料中相對應的 todo 來更新 state

```javascript=
$('.todos').on('click', '.btn-delete', e => {
  // 讀取該 todo id
  const id = Number($(e.target).parents('.todo').attr('data-id'));
  // 更新 state: 篩選掉資料中相對應的 todo
  updateState({
    todos: state.todos = state.todos.filter(todo => todo.id !== id)
  });
});
```

修改 todo 狀態也是類似概念，完整程式碼如下：

<iframe height="265" style="width: 100%;" scrolling="no" title="[week21] Todo List template_component &amp; state" src="https://codepen.io/heidiliu2020/embed/preview/wvzvBbY?height=265&theme-id=dark&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/wvzvBbY'>[week21] Todo List template_component &amp; state</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

---

## 結論

在剛接觸 React 時，覺得和之前實作的 [SPA 留言版](https://hackmd.io/@Heidi-Liu/note-be101-php-todolist)概念很類似，以非同步方式串接後端 API，透過將前後端分離，以動態方式來更新頁面，同樣是直接更新後端資料來顯示前端畫面。

我們也從 Todo List 範例中，學到 React 最重要的兩個概念 Component 和 State：
- 透過 Component 將畫面與功能模組化，並以傳入的參數 props 來設定屬性或是資料
- 因為畫面永遠由 State 產生，我們會直接更改資料，再由資料去顯示畫面

瞭解到這些概念後，我們就能正式入門 React 這套 JavaScript Library。
