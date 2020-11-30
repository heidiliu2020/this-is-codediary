###### tags: `React` `useEffect` `hooks`
# [week 21] React Hooks API：useEffect & 實作一個自己的鉤子

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

在 React Hooks 當中，最重要的就是 useState 和 useEffect，若能學會如何使用這兩個 hook，對於 React 應用也會更容易上手。

---

## 初探 useEffect

> 詳細可參考官方文件：[使用 Effect Hook](https://zh-hant.reactjs.org/docs/hooks-effect.html)。

簡單來說，就是透過 useEffect 這個 hook，告訴 React「component 在 render 之後要做的事情」。

有別於一般的 hook 是傳值進去，userEffect 傳入的是 function，使用方法如下：

```javascript=
// 從 react 引入使用useEffect
import { useEffect } from "react";

function App() {
  useEffect(() => {
    alert("執行完畢!");
  });
}
```

就會在每次畫面 render 結束後執行 useEffect 傳入的 function：

![](https://i.imgur.com/QJJIuOp.png)

但通常我們不會想要在每次 render 後都執行 function，像是設定在某些 state 改變時才會執行。

### 範例：把資料同步到 LocalStorage

以把 todo APP 同步到 LocalStorage 這個功能為例：

```javascript=
function writeTodosToLocalStorage(todos) {
  // localStorage 只能存字串
  window.localStorage.setItem("todos", JSON.stringify(todos));
}
```

### setState()：非同步更新狀態

此外，還有很重要的一點，就是之前實作的 setTodos() 功能其實是非同步行為。

如果在新增 todo 的同時進行 `console.log(todos)`，會發現畫面 render 了，todos 卻還沒有更新：

![](https://i.imgur.com/ATpucjM.png)

因此不能直接在 function 中寫入 todos，而是要直接寫入更新過的狀態，其他功能也以此類推，在每次改變 todo 時都要執行 writeTodosToLocalStorage()：

```javascript=
  const handleButtonClick = () => {
    setTodos([
      {
        id: id.current,
        content: value,
      },
      ...todos,
    ]);
    // 因為 setTodos 非同步，不能直接傳入 todos
    writeTodosToLocalStorage([
      {
        id: id.current,
        content: value,
      },
      ...todos,
    ]);
    setValue("");
    id.current++;
  };
```

上述這種做法，其實是我們過去利用 jQury 實作的想法，在變動資料的同時進行其他動作。

但其實進行新增、編輯、刪除 todo 時有個共通點，就是會「todos 會改變」，接著就是 useEffect 登場的時候了！

因為 useEffect() 會在每次 render 後執行，有 render 就代表 state 有變動。一旦有變動就執行同步 function，可把程式碼改寫如下：

```javascript=
// 每次 render 後會執行 useEffect 中的 function
useEffect(() => {
  writeTodosToLocalStorage(todos);
  console.log(JSON.stringify(todos));
});
```

這樣就成功在每次 render 後，都把最新的 todos 狀態同步到 localStorage：

![](https://i.imgur.com/UKSkzM7.png)

但這樣做其實有個缺點，透過執行的 console.log()，可發現連在輸入 input 時也會執行 render，應該只需要在 todos 有改變時才進行 render。

### useEffect()：可接收兩個參數

而 useEffect 的第二個參數可以解決這個問題，需傳入一個陣列，用來放想要關注的資料，當變數改變時才會執行 useEffect：

```javascript=
useEffect(() => { code }, [array]);
// 第一個參數：一個函式，表示要做什麼事
// 第二個參數：一個陣列，定義哪寫變數改變時，才會重新執行 useEffect
```

可改寫如下，代表在 todos 改變時才會重新執行 useEffect()：

```javascript=
useEffect(() => {
writeTodosToLocalStorage(todos);
// 傳入第二個參數 [todos]
}, [todos]);
```

透過 localStorage 的記憶功能，我們就能在頁面第一次 render 結束後，把 localStorage 中的 todos 同步到頁面上。

### 第二個參數是空陣列：不會重新執行

在第二個參數傳入空陣列，就只有第一次 render 會執行這個 useEffect，可用來進行初始化：

```javascript=
// 進行初始化: setTodos 或是拿 API
useEffect(() => {
  // 拿取資料，沒有資料的話就是空字串（進行錯誤處理）
  const todoData = window.localStorage.getItem("todos") || "";
  if (todoData) {
    // 把 todoData 放回 state
    setTodos(JSON.parse(todoData));
  }
// 傳入空陣列: 代表只有第一次 render 才會執行這個 useEffect
}, []);
```

### useEffect 會遇到的問題

但是在重整頁面瞬間，會發現畫面閃了一下，這是因為第一次 render 畫面顯示的是 useState 初始設定，第二次 render 才是放入 todoDate：

![](https://i.imgur.com/asdZh1d.gif)

那麼該如何解決 useEffect 這個問題呢？接下來會繼續介紹其他功能來改善。

## useLayoutEffect：render 時同步執行

我們在開頭提到，可透過 useEffect 這個 hook，告訴 React「component 在 render 之後要做的事情」。

但其實更精確的，應該是「在 render 完，瀏覽器 paint 以後要做的事情」，所以才會有 render 後畫面閃一下的情況發生。

而 useLayoutEffect 這個 hook，則是「在 render 完，瀏覽器 paint 以前要做的事情」。

也就是說，和 useEffect 功能其實很類似，差別在於同步與非同步：

- useEffect：非同步函式，等 UI 渲染完才會執行
- useLayoutEffect：同步函式，UI 會等 useLayoutEffect 中做的事情結束才會渲染

實際修改剛才的程式碼：

```javascript=
// 從 react 引入 hook
import { useState, useRef, useEffect, useLayoutEffect } from "react";

// 把讀取 todoData 的 useEffect 改用 useLayoutEffect
  useLayoutEffect(() => {
    const todoData = window.localStorage.getItem("todos") || "";
    if (todoData) {
      setTodos(JSON.parse(todoData));
    }
  }, []);
```

如此畫面就不會再閃一次初始的資料了：

![](https://i.imgur.com/HNXymGK.gif)

至於為什麼會產生這個情況，可從 React 的 Hook Flow 談起。

### Hook Flow 流程圖

![](https://i.imgur.com/usOw4li.png)
（圖片來源：https://github.com/donavon/hook-flow/blob/master/README.md）

Hook 執行流程可分為三個部分：
- Mount：把 component 放到畫面上
- Update：更新 state 流程
- Unmount：清除 effect

原本是在瀏覽器 paint 之後才 run effects，若能提早改變 state 並更新畫面，就會直接顯示最新的 state，而不會出現初始 state。

除了透過 useLayoutEffect，還有另一種做法，同樣能解決畫面閃一下的問題，也就是接下來要介紹的 lazy initializer。

因為 useState 可以傳入初始值，那就直接把要更新的 todoDate 作為 state 初始值：

```javascript=
function App() {
  // 從 localStorage 拿取資料
  const todoData = window.localStorage.getItem("todos") || "";
  // 直接把 todoData 設為 state 初始值，沒有資料就設為空陣列
  const [todos, setTodos] = useState(JSON.parse(todoData) || []);
  // 略
```

但這麼會產生另一個問題，就是只有第一次 render 才會執行 useState 初始值，但後續 render 還是會進行撈取 todoData 的動作，又因為 useState 已經有值了，React 就會忽略裡面的東西，這其實會造成效能上的浪費。

### lazy initializer

useState 除了設定初始值，其實可以傳入一個 function，經由 function return 的值就會是 state 的初始值：

```javascript=
function App() {
  // 在 useState 傳入 function，會把回傳值設為初始值
  const [todos, setTodos] = useState(() => {
    // 用來檢測 useState 是否只執行一次
    console.log("init");
    const todoData = window.localStorage.getItem("todos") || "";
    return JSON.parse(todoData) || [];
  });
// 略
```

又因為初始值改變了，也要重新設定 todo id，修改後如下：

```javascript=
function App() {
  // 因為初始值改變了，也要重新設定 todo id
  const id = useRef(1);
  const [todos, setTodos] = useState(() => {
    let todoData = window.localStorage.getItem("todos") || "";
    if (todoData) {
      todoData = JSON.parse(todoData);
      id.current = todoData[0].id + 1;
    } else {
      todoData = [];
    }
    // 把 return 的值設定為初始值
    return todoData;
  });
```

像這樣在 useState 透過傳入 function 來設定初始值，就是 run lazy initializer 的過程。因為只有第一次會執行，適合用於一些複雜的運算，這樣 function 就只會被執行一次，避免每次 render 產生的效能問題。

## 再探 useEffect：cleanup effect

在 Hook Flow 中，有個步驟其實是先 cleanup effect，然後再 run effect，這是什麼意思呢？

繼續用剛才的 todos 為範例，以下程式碼代表「每當 todos 改變，就會執行 useEffect 中的 function」：

```javascript=
  useEffect(() => {
    writeTodosToLocalStorage(todos);
  }, [todos]);
```

但其實在這個 function 中可以 return 另一個 function，又稱為 cleanup function，代表「在這個 effect 被清掉之前要做的事情」：

```javascript=
  useEffect(() => {
    // 每當 todos 改變，effect 要做的事
    writeTodosToLocalStorage(todos);

    return () => {
    // effect 被清掉前要做的事
    }
  }, [todos]);
```

每次畫面渲染時，其實就是執行一次 APP() 這個 function，可透過這段程式碼來模擬流程：

```javascript=
function APP() {
  // ...
  useEffect(() => {
    writeTodosToLocalStorage(todos);
    console.log("useEffect: todos", JSON.stringify(todos));
    // clean up
    return () => {
      console.log("clearEffect: todos", JSON.stringify(todos));
    };
  }, [todos]);
  // ...
```

#### 1. 進行第一次 render，執行 APP()，呼叫 useEffect()

```javascript=
  useEffect(() => {
    writeTodosToLocalStorage(todos);
    console.log("useEffect: todos", JSON.stringify(todos));
// useEffect: todos [{"id":2,"content":"render!"}]
```

#### 2. 點擊已完成，進行第二次 render，執行 APP()，先清除上一個 effect，再執行第二次 useEffect

```javascript=
1. 先清除上一個 effect
// clearEffect: todos [{"id":2,"content":"render!"}]
2. 再進行第二次 useEffect
// useEffect: todos [{"id":2,"content":"render!","isDone":true}]
```

### cleanup function 執行時機

結合上述範例，cleanup function 執行的時間點有兩個：

- 要執行下一個 useEffect 的時候，要先清除上一個 effect
- component unmount 的時候，會清除 effect

那我們可以透過 useEffect 的 cleanup function 做什麼呢？例如：
- 用來清除訂閱操作，避免記憶體洩漏，可參考[官網範例](https://zh-hant.reactjs.org/docs/hooks-effect.html)
- 當 component 被 unmount 時要執行的事情

以下方範例來說，代表「只有在這個 component 被 unmount 會執行 cleanup function」，又因為第二個參數是空陣列，所以這個 useEffect 只會執行一次：

```javascript=
  useEffect(() => {
    console.log("mount");
    return () => {
      console.log("unmount");
    };
  }, []);
```

## 實作一個自己的鉤子

接下來要談談 hooks 最強大的地方，就是我們其實能寫一個自己 hook，又稱作 custom hook，命名開頭必須是 use 開頭，詳細內容可參考[官方文件](https://zh-hant.reactjs.org/docs/hooks-custom.html)。

### 實作一個 useInput

以 input 元素為例，我們可以把 value 和 handleInputChange 等行為包在 useInput.js 檔案，寫法和之前的 APP.js 很類似：

```javascript=
// 從 react 引入 useState
import { useState } from "react";

// 匯出 useInput()
export default function useInput() {
  const [value, setValue] = useState("");
  const handleChange = (e) => {
    setValue(e.target.value);
  };
  return {
    value,
    setValue,
    handleChange,
  };
}
```

就可以用從 useInput.js 讀取到的 handleChange，取代原本的 handleInputChange：

```javascript=
// APP.js
import useInput from "./useInput";

function APP {
  // ...
  // 從 useInput 讀取 value 資料
  const { value, setValue, handleChange } = useInput();
  // ...
  return (
  <div className="App">
    <input
      type="text"
      placeholder="Add todo..."
      value={value}
      // 改為 handleChange
      onChange={handleChange}
      onKeyDown={handleKeyDown}
    />
```

修改完程式也能正常運行，這樣寫的好處就是，如果有第二個 input 時，也能使用共通的邏輯，例如：

```javascript=
// 第一個 input
const { value, setValue, handleChange } = useInput();
// 第二個 input
const { value: todoName, setValue:setTodoName , handleChange: handleTodoName } = useInput();
```

### 實作一個 useTodos

我們也可以把 todos 的邏輯獨立成一個 hook，也就是 useTodos.js：

```javascript=
// useTodo.js
import { useState, useEffect, useRef } from "react";

function writeTodosToLocalStorage(todos) {
  window.localStorage.setItem("todos", JSON.stringify(todos));
}

export default function useTodos() {
  const id = useRef(1);
  const [todos, setTodos] = useState(() => {
    let todoData = JSON.parse(window.localStorage.getItem("todos")) || "";
    if (todoData) {
      todoData = JSON.parse(todoData);
      id.current = todoData[0].id + 1;
    } else {
      todoData = [];
    }
    return todoData;
  });

  useEffect(() => {
    writeTodosToLocalStorage(todos);
  }, [todos]);

  return {
    todos,
    setTodos,
    id,
  };
}
```

並引入 APP.js 使用：

```javascript=
import useInput from "./useInput";
import useTodos from "./useTodos";

function App() {
  // 從 useTodos 讀取 todos 資料
  const { todos, setTodos, id } = useTodos();
  // 從 useInput 讀取 value 資料
  const { value, setValue, handleChange } = useInput();
  // ...
```

### 將 UI 與邏輯分開寫

若再繼續細分功能，甚至可以做到把 UI 和 todos 邏輯完全分開，改寫如下：

- App.js

```javascript=
import TodoItem from "./TodoItem";
import useTodos from "./useTodos";

function App() {
  // 從 useTodos 讀取 todos 資料
  const {
    todos,
    setTodos,
    id,
    handleButtonClick,
    handleKeyDown,
    handleTogglerIsDone,
    handleDeleteTodo,
    value,
    setValue,
    handleChange,
  } = useTodos();

  // 剩下 UI 畫面
  return (
    <div className="App">
      <input
        type="text"
        placeholder="Add todo..."
        value={value}
        onChange={handleChange}
        onKeyDown={handleKeyDown}
      />
      <button onClick={handleButtonClick}>Add Todo</button>
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          handleDeleteTodo={handleDeleteTodo}
          handleTogglerIsDone={handleTogglerIsDone}
        />
      ))}
    </div>
  );
}

export default App;
```

- useTodo.js

```javascript=
import { useState, useEffect, useRef } from "react";
import useInput from "./useInput";

function writeTodosToLocalStorage(todos) {
  window.localStorage.setItem("todos", JSON.stringify(todos));
}

export default function useTodos() {
  const id = useRef(1);
  // 從 useInput 讀取 value 資料
  const { value, setValue, handleChange } = useInput();
  const [todos, setTodos] = useState(() => {
    let todoData = JSON.parse(window.localStorage.getItem("todos")) || "";
    if (todoData.length) {
      id.current = todoData[0].id + 1;
    } else {
      todoData = [];
    }
    return todoData;
  });

  // 點擊按鈕新增 todo
  const handleButtonClick = () => {
    addTodo();
  };
  // enter 新增 todo
  const handleKeyDown = (e) => {
    if (e.keyCode !== 13) return;
    addTodo();
  };

  const addTodo = () => {
    // 檢查輸入欄位是否為空值，trim() 可清除字串前後空白
    if (value.trim().length === 0) return;
    setTodos([
      {
        id: id.current,
        content: value,
      },
      ...todos,
    ]);
    setValue("");
    id.current++;
  };

  const handleTogglerIsDone = (id) => {
    setTodos(
      todos.map((todo) => {
        if (todo.id !== id) return todo;
        return {
          ...todo,
          isDone: !todo.isDone,
        };
      })
    );
  };

  const handleDeleteTodo = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  useEffect(() => {
    writeTodosToLocalStorage(todos);
  }, [todos]);

  return {
    todos,
    setTodos,
    id,
    handleButtonClick,
    handleKeyDown,
    handleTogglerIsDone,
    handleDeleteTodo,
    value,
    setValue,
    handleChange,
  };
}
```

其實和之前寫前後端分離的時候很類似，寫成自訂 hook 的過程，就像是把不同邏輯的 function 給模組化，這麼說似乎也沒錯，畢竟 hook 就是 fucntion。

透過抽出共同邏輯的方式，可將功能包裝在 hooks，就算是在不同 UI，也同樣能利用 return 的值，在畫面上呈現想要的資料。

## hooks 觀念總結

hooks 基本上可以分成下列幾種：
- 內建 hooks
  - useState：讓 function component 擁有 state，可以管理內部狀態
  - useEffect：在 render 完、瀏覽器 paint 畫面之後要做什麼事
  - useLayoutEffect：在 render 完、瀏覽器 paint 畫面之前要做什麼事
- 自訂 hooks：把邏輯從 UI 抽出來寫一個 hook
- 參考別人寫好的 hooks：[useHooks](https://usehooks.com/)

## 補充資料

推薦閱讀 Dan Abramov 所撰寫有關 React 的系列文章，裡面對於 useEffect 的原理有更詳細敘述：
- [A Complete Guide to useEffect](https://comus.dev/zh-hant/a-complete-guide-to-useeffect/)
- [How Are Function Components Different from Classes?](https://comus.dev/zh-hans/how-are-function-components-different-from-classes/)

第 11 屆 iT 邦幫忙鐵人賽有關 React 的系列文章：
- [從 Hooks 開始，讓你的網頁 React 起來](https://ithelp.ithome.com.tw/users/20103315/ironman/2668)



參考資料：

- [【Day.26】React進階 - useEffect v.s useLayoutEffect](https://ithelp.ithome.com.tw/articles/10252118)
- [Overreacted - Dan Abramov.](https://comus.dev/)
- [[ReactDoc] React Hooks - useEffect](https://pjchender.github.io/2019/08/07/reactdoc-react-hooks-useeffect/)
