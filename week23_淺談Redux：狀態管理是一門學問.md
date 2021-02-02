###### tags: `Redux`
# [week 23] 淺談 Redux：狀態管理是一門學問

> 本篇為 [[FE303] React 的好夥伴：Redux](https://lidemy.com/p/fe303-react-redux) 這門課程的學習筆記。如有錯誤歡迎指正！

- 推薦閱讀：[Redux 官方文件](https://redux.js.org/introduction/getting-started)

## 什麼是 Redux？

根據 [Redux 官網](https://redux.js.org/)說明，可知 Redux 是一個「用於 JavaScript 應用程式的**狀態管理**工具」：

> A Predictable State Container for JS Apps

雖然 Redux 經常與 React 搭配使用，但其實 Redux 是一種前端的「架構模式」。簡單來說，就是用來實現「狀態管理機制」的套件，並且能套用在任何程式語言。

## 認識 Redux 之前

再進一步認識 Redux 之前，可先從 Redux 的歷史演化來瞭解。

最早是在 Facebook 提出 React 時，所提出的 Flux 開發架構，目的是解決 MVC 在大型商業網站所存在的問題，例如管理 UI 畫面與資料之間的對應關係。

### 「狀態管理」是一門學問

從以前到現在，關於「狀態管理」，在不同框架也會有不同處理方式，舉例來說：

- jQuery：資料與畫面分離，重點放在「畫面」上
- Vue 與 Angular：資料與畫面「雙向綁定」

以 [Vue.js](https://vuejs.org/v2/guide/) 為例，是透過 `v-model` 語法進行雙向綁定：
```htmlmixed=
<!-- 畫面 -->
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
```javascript=
// 資料
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
- React：只需要管資料，藉由「狀態」渲染出畫面

### Flux 簡介

根據 [Flux](https://facebook.github.io/Flux/) 官網說明：

> Application architecture for building user interfaces

在傳統的 MVC 架構，Model 和 View 之間可能會呈現複雜關係：

![](https://i.imgur.com/Ourrrv7.png)
（圖片來源：[Facebook 介紹影片](https://www.youtube.com/watch?v=nYkdrAPrdcw)）

Facebook 當初之所以會提出 React 和 Flux，就是為了解決當 APP 架構變更大，功能變更複雜時遇到的問題。例如 FB 中的 Messager 的提醒通知，程式碼中可能有許多部分會操控到同一 Model，使得狀態過於複雜，不易進行後續追蹤。

Flux 架構流程如下，若想要改變 Store（資料）或 View（畫面），都必須透過 Dispatcher 發出 Action（指令），呈現單向資料流：

![](https://i.imgur.com/vArLgx3.png)

上述架構，在小型專案中其實是多此一舉，直接透過修改 Store 去渲染 View 即可；但在大型專案中，透過像這樣「集中」管理的方式，會更容易進行複雜的狀態管理。

## Redux 簡介

以下是 Redux 中的 data flow 示意圖：

![](https://i.imgur.com/5a6tNaA.gif)

在 React 當中，其實有個和 Redux 功能類似的內建 Hook：[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)，同樣可用來管理複雜的狀態。

### React Hook：useReducer

useReducer 可接受三個參數：

```javascript=
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

以下是官網提供的範例：

```javascript=
// 初始狀態為 count: 0
const initialState = {count: 0};

// 由 reducer 回傳 state
function reducer(state, action) {
  switch (action.type) {
    // return 新的 state 並取代原本的
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    // 非預期指令時則丟出 Error
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      // 由 dispatch 發送指令，{type: 'decrement'} 這個物件就代表一個動作
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

- reducer 是一個 function，可接收兩個參數：目前的狀態和要執行的操作 
- initialState 代表初始狀態
- 使用 useReducer 會得到 state, dispatch 兩個值，可對應到 Redux 中「Store 裡面的狀態」和「由 dispatch 指定 Store 執行哪些事」

## Redux 實際操作

透過上述簡介，我們可以瞭解到 React 和 Redux 其實並無相依性，兩者均可進行狀態管理，差別在於：
- React：把 state 放在 component 裡面
- Redux：把 state 放在 Store 裡面，Store 是一個 JavaScript 物件

### 安裝套件

首先依照[官方文件](https://redux.js.org/introduction/getting-started)安裝 Redux：

1. 初始 npm 專案

```
$ npm init
```
2. 安裝 redux

```
$ npm install redux
```

3. 新增 app.js 並使用 require 引入

> 官方文件是使用 import，需注意在 node.js 較舊版本無法使用，因此這裡使用 require。

```javascript=
const { createStore } = require("redux");
```

### 建立 Store 存放 state

透過以下程式碼，可創建 Redux 中的 Store：

```javascript=
const { createStore } = require("redux");

const initialState = {
  value: 0,
};

// reducer 決定狀態要如何變化
function counterReducer(state = initialState, action) {
  return state;
}

// 把 reducer 存入 store
let store = createStore(counterReducer);

console.log(store);
```

在終端機執行 `node app.js`，可知 store 其實是一個物件：

![](https://i.imgur.com/kjulolO.png)

若改城呼叫物件裡面的函式 `getState()`，會顯示目前的 state，也就是 initialState：

```javascript=
console.log(store.getState());
// { value: 0 }
```

### 透過 disptch 指定要做的事

接著可透過 store.dispatch() 指定要做的事，傳入參數為物件，慣例寫法是 `type: '...'`：

```javascript=
const { createStore } = require("redux");

const initialState = {
  value: 0,
};

function counterReducer(state = initialState, action) {
  console.log("receive action", action);
  return state;
}

let store = createStore(counterReducer);

store.dispatch({
  type: 'plus'
})

console.log(store.getState());
```

結果如下：

![](https://i.imgur.com/7TA6D2C.png)

- `{ type: '@@redux/INIT0.1.0.m.r.p' }`：初始化時 redux 自動建立的 dispatch
- `{ type: 'plus' }`：印出 dispatch 的 action

也可以透過 `setTimeout()` 驗證：

```javascript=
// 延遲 1 秒
setTimeout(() => {
  store.dispatch({
    type: "plus",
  });
}, 1000);
```

結果如下，1 秒後 reducer 才印出傳入的 action：

![](https://i.imgur.com/eZliFre.gif)

### 透過 dispatch 改變 state

改寫如下，傳入 action 會改變 state：

```javascript=
function counterReducer(state = initialState, action) {
  console.log("receive action", action);
  if (action.type === "plus") {
    return {
      value: state.value + 1,  // 回傳新的 state
    };
  }
  return state;    // 回傳原本的 state
}

let store = createStore(counterReducer);

console.log("first state", store.getState());

// 傳入 action 改變 state
store.dispatch({
  type: "plus",
});

console.log("second state", store.getState());
```

印出結果：

![](https://i.imgur.com/qliRszN.png)

### Reducer 中的判斷式

- if...else 條件式：當 type 變多時不易管理

```javascript=
function counterReducer(state = initialState, action) {
  if (action.type === "plus") {
    return {
      value: state.value + 1,
    };
  } else if (action.type === "minus") {
    return {
      value: state.value - 1,
    };
  }
  return state;
}
```

- switch 條件式：較推薦使用

```javascript=
function counterReducer(state = initialState, action) {
  switch (action.type) {
    case "plus": {
      return {
        value: state.value + 1,
      };
    }

    case "minus": {
      return {
        value: state.value - 1,
      };
    }

    // 非預期 type 時直接回傳 state
    default: {
      return state;
    }
  }
}
```

### store.subscribe()：當 store 改變時觸發執行

store 其實也有提供類似 addEventLister 的功能，也就是 subscribe()，傳入一個 function 作為參數：

```javascript=
// 當 store 改變時執行
store.subscribe(() => {
  console.log("change!", store.getState());
});
```

再改寫上述範例：

```javascript=
let store = createStore(counterReducer);

store.subscribe(() => {
  console.log("change!", store.getState());
});

store.dispatch({
  type: "plus",
});
```

結果如下：

![](https://i.imgur.com/wcSbCsN.png)

## 實作簡易的 todolist

以實作新增和刪除功能為例：

### 新增功能

和 React 的 useState 用法類似，因為 reducer 回傳新的 state 會直接覆蓋原有的，必須加上 `...state` 保存原本的 state：

```javascript=
const { createStore } = require("redux");

let todoId = 0;

const initialState = {
  email: "123@123",
  todos: [],
};

function counterReducer(state = initialState, action) {
  console.log("receive action", action);
  switch (action.type) {
    case "add_todo": {
      return {
        // 保存原本的 state
        ...state,
        todos: [
          ...state.todos,
          {
            id: todoId++,
            name: action.payload.name,
          },
        ],
      };
    }

    default: {
      return state;
    }
  }
}

let store = createStore(counterReducer);

store.subscribe(() => {
  console.log("change!", store.getState());
});

store.dispatch({
  type: "add_todo",
  payload: {
    name: "todo0",
  },
});

store.dispatch({
  type: "add_todo",
  payload: {
    name: "todo1",
  },
});
```

### 刪除功能

新增 delete_todo 這個 dispatch：

```javascript=
store.dispatch({
  type: "delete_todo",
  payload: {
    id: 0,
  },
});
```

並在 reducer 新增條件，寫入要執行的動作：

```javascript=
    case "delete_todo": {
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.payload.id),
      };
    }
// ...
```

結果如下：

![](https://i.imgur.com/cuoNgBT.png)

### Reducer 的優點：方便寫測試

像這樣分開撰寫的好處，就是方便對 reducer 寫測試，可藉由比對結果，驗證邏輯是否正確，如以下範例：

```javascript=
expect(
  counterReducer(initialState, {
    type: "add_todo",
    payload: {
      name: "123",
    },
  })
).toEqual({                  // 比對結果
  todos: [{ name: "123" }], 
});
```

## 優化：避免程式開發時出錯

當程式變複雜時，我們可透過「action constants」和「action creator」進行管理，可減少開發時出錯：

### action constants：以物件集中管理 Action Types

在上述範例中，我們是以「字串」來表示 type，但這麼做有個缺點，就是打錯字或發生錯誤時不易 debug，可透過 ActionTypes 物件集中管理：

```javascript=
// action constants
const ActionTypes = {
  ADD_TODO: "add_todo",
  DELETE_TODO: "delete_todo",
};
```

將原本的字串改用 ActionTypes 物件表示：

```javascript=
function counterReducer(state = initialState, action) {
  switch (action.type) {
    case ActionTypes.ADD_TODO: {
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            id: todoId++,
            name: action.payload.name,
          },
        ],
      };
    }

    case ActionTypes.DELETE_TODO: {
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.payload.id),
      };
    }

    default: {
      return state;
    }
  }
}

store.dispatch({
  type: ActionTypes.ADD_TODO,
  payload: {
    name: "todo1",
  },
});

store.dispatch({
  type: ActionTypes.DELETE_TODO,
  payload: {
    id: 0,
  },
});
```

### action creator：透過 function 建立 action

```javascript=
function addTodo(name) {
  return {
    type: ActionTypes.ADD_TODO,
    payload: {
      name,
    },
  };
}

function deleteTodo(name) {
  return {
    type: ActionTypes.DELETE_TODO,
    payload: {
      id: 0,
    },
  };
}

store.dispatch(addTodo("todo0"));
store.dispatch(addTodo("todo1"));
store.dispatch(addTodo("todo2"));
store.dispatch(deleteTodo(1));
```

