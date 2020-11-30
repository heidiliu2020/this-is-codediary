###### tags: `React` `state` `props`
# [week 21] React Hooks API：useState & 再戰 Todo List

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 我知道 React 的目的以及原理
 P1 我知道我們為什麼需要 React
 P1 我知道使用 React 跟之前使用 jQuery 的區別
 P1 我理解 state 跟 props 的不同
```

---

## React Hooks API

根據[官網介紹](https://zh-hant.reactjs.org/docs/hooks-intro.html?no-cache=1)：

> Hook 是 React 16.8 中增加的新功能。它讓你不必寫 class 就能使用 state 以及其他 React 的功能。

先前提到，React 的核心概念之一是 component，而根據寫法又可分為兩種：

- class compent：透過 class 寫成，可以控制 state 和生命週期
- function compent：透過 function 寫成，主要用來呈現 UI

因為加入了 React hooks，結合 props、state、context、refs 以及 lifecycle 等概念，提供了更直接的 API 使用，讓我們能在 functional component 管理狀態和使用生命周期等功能，藉此簡化程式碼與提高重用性。

引入 hook 的語法如下，原本要寫成 React.useState，透過解構語法即可直接使用 useState：

```javascript=
import { useState, useEffect, useRef } from 'react'; 
```

### hooks 只能寫在 component 第一層

因為 React 背後的機制，hooks 只能寫在 component 第一層。

也就是說，不能把 hooks 寫在 if - else 等條件判斷句裡面，像是符合 XX 條件才使用，否則會出現錯誤訊息：

> React Hook "useRef" is called conditionally. React Hooks must be called in the exact same order in every component render    react-hooks/rules-of-hooks

只能自行在 hooks 裡面進行判斷是否使用，例如下方寫法：

```javascript=
useEffect(() => {
  // 若不符合就直接 return
  if (!todos) return;
  // 若符合要做的事情
})
```

### useState：用來設定 react 中的 state

語法如下：

```javascript=
const [currentValue, setCurrentValue] = useState(initialValue);
```

- currentValue：存放 state 的值
- setCurrentValue：用來設定 state 值
- initialValue：state 的初始值

還記得我們前面舉的 Counter component 範例嗎？簡單來說，就是在 `useState([初始值])` 傳入初始值，然後回傳一個陣列：

```javascript=
function App(){
const [todos, setTodos] = useState([1])
}
```

其中 todos 具有 immutable 特性，也就是無法再賦值，必須用 setTodos 來產生新的 state，才能在每次 render 畫面時改變 todos 的值。

透過這個概念，我們也能實作出簡單的 Todo List：

```javascript=
// 解構語法
import { useState } from 'react';

function App() {
  const [todos, setTodos] = useState([
    1
  ]);

  const handleButtonClick = () => {
    // 傳入參數: 新的 todo + 解構 todos
    setTodos(["new todo", ...todos]);
  }

  return (
    <div className="App">
      /* 在 JSX 中，單標籤必須 /> 結尾 */
      <input type="text" placeholder="Add todo..." />
      <button onClick={handleButtonClick}>Add Todo</button>
      {
        todos.map((todo, index) => <TodoItem key={index} content={todo} /> ) 
      }
    </div>
  );
}
```

![](https://i.imgur.com/f6EsvhC.png)

#### controlled vs uncontrolled

> 詳細可參考[官方文件](https://zh-hant.reactjs.org/docs/forms.html#controlled-components)。

在 React 中，所有在 UI 上會動的東西幾乎都是 state，我們也能夠進行控制。

---

## 再戰 Todo List

### 新增 Todo

透過下方程式碼，可存取 input 的值，並藉由 setValue 來更新 todos 狀態：

```javascript=
import TodoItem from './TodoItem'
// 解構語法
import { useState, useRef } from 'react';

function App() {
  const [todos, setTodos] = useState([
    1
  ]);

  const [value, setValue] = useState('');

  const handleButtonClick = () => {
    // 傳入參數: 新的 todo + 解構 todos
    setTodos([value, ...todos]);
    // 新增完 todo 後清空 value
    setValue('');
  }

  const handleInputChange = (e) => {
    // 拿到 input 的 value
    setValue(e.target.value);
  }

  return (
    <div className="App">
      <input type="text" placeholder="Add todo..." value={value} onChange={handleInputChange} />
      <button onClick={handleButtonClick}>Add Todo</button>
      {
        todos.map((todo, index) => <TodoItem key={index} content={todo} /> ) 
      }
    </div>
  );
}
```

![](https://i.imgur.com/EAY1tzO.png)

### useRef：用來抓取 DOM 節點的 hook

透過宣告 `id = useRef(2)`，可以在每次 render 時，更改每個 todo 中 id.current 的值：

```javascript=
function App() {
  const [todos, setTodos] = useState([
    {id: 1, content: 'todo1'}
  ]);

  const [value, setValue] = useState('');
  // useRef: 能我們抓取到 DOM 節點的 hooks
  // 會回傳一個物件，要以 id.current 讀取
  const id = useRef(2);

  const handleButtonClick = () => {
    setTodos([
      {
        id: id.current,
        content: value
      }, ...todos]
    );
    setValue('');
    id.current++;
  }

  const handleInputChange = (e) => {
    setValue(e.target.value);
  }

  return (
    <div className="App">
      <input type="text" placeholder="Add todo..." value={value} onChange={handleInputChange} />
      <button onClick={handleButtonClick}>Add Todo</button>
      {
        todos.map(todo => <TodoItem key={todo.id} todo={todo} /> ) 
      }
    </div>
  );
```

傳入 todo 這個參數，並放到 TodoItem：

```javascript=
export default function TodoItem({ content, todo }) {
  return (
    <TodoItemWrapper data-todo-id={todo.id}>
      <TodoContent>{todo.content}</TodoContent>
      <TodoButtonWrapper>
        <Button>未完成</Button>
        <RedButton>刪除</RedButton>
      </TodoButtonWrapper>
    </TodoItemWrapper>
  );
}
```

### 刪除 todo

#### 1. 把要做的 function 寫在 Parent，並傳入參數給 Children

把 handleDeleteTodo 這個 function 當作 props 傳給 TodoItem：

```javascript=
  const handleDeleteTodo = id => {

  }

  return (
    <div className="App">
      <input type="text" placeholder="Add todo..." value={value} onChange={handleInputChange} />
      <button onClick={handleButtonClick}>Add Todo</button>
      {
        todos.map(todo => <TodoItem key={todo.id} todo={todo} handleDeleteTodo={handleDeleteTodo} /> ) 
      }
    </div>
  );
```

#### 2. 再由 Children 呼叫 function

TodoItem 就可以接收這個 funtcion，並透過刪除按鈕的監聽事件，來呼叫 handleDeleteTodo 以及回傳該 `todo.id`：

```javascript=
export default function TodoItem({ content, todo, handleDeleteTodo }) {
  return (
    <TodoItemWrapper data-todo-id={todo.id}>
      <TodoContent>{todo.content}</TodoContent>
      <TodoButtonWrapper>
        <Button>未完成</Button>
        <RedButton onClick={() => {
          handleDeleteTodo(todo.id)
        }}>刪除</RedButton>
      </TodoButtonWrapper>
    </TodoItemWrapper>
  );
}
```

#### 3. 接著在 Parent 處理 function

```javascript=
  const handleDeleteTodo = id => {
    // 若用 splice() 會改到原本的 todo，因此要用 filter()，留下該 id 以外的 todo
    setTodos(todos.filter(todo => todo.id !== id))
  }
```

### 編輯 todo

在實作編輯 todo 之前，要先來檢視資料結構，也就是把 todo 的狀態加上 isDone，用來判斷是否已完成：

```javascript=
function App() {
  const [todos, setTodos] = useState([
    { id: 1, content: 'done', isDone: true },
    { id: 2, content: 'not done', isDone: false }
  ]);

  const [value, setValue] = useState('');
  const id = useRef(3);
```

接著在 TodoItem 的按鈕加上三元運算子，用來判斷 todo 狀態，再藉由 $isDone 這個參數，判斷式是否執行後面的 JSX 語法：

```javascript=
const TodoContent = styled.div`
  font-size: 26px;
  color: ${props => props.theme.colors.primary_300};

  // 若 isDone 這個參數為 true(已完成) 則接續後面的 JSX 語法
  ${props => props.$isDone && `
    text-decoration: line-through;
  `}
`

export default function TodoItem({ content, todo, handleDeleteTodo }) {
  return (
    <TodoItemWrapper data-todo-id={todo.id}>
      // 傳入 isDone 這個參數
      <TodoContent $isDone={todo.isDone}>{todo.content}</TodoContent>
      <TodoButtonWrapper>
        <Button>
          // 透過三元運算子判斷 todo 狀態
          {todo.isDone ? '已完成' : '未完成'}
        </Button>
        <RedButton onClick={() => {
          handleDeleteTodo(todo.id)
        }}>刪除</RedButton>
      </TodoButtonWrapper>
    </TodoItemWrapper>
  );
}
```

除了三元運算子，也可改寫成邏輯運算子 && 的寫法，適用於多種可能的情況：

```javascript=
  <Button onClick={handleTogglerClick}>
    {todo.isDone && '已完成'}
    {!todo.isDone && '未完成'}
  </Button>
```

接著實作 handleToggleIsDone 修改 todo 功能：

```javascript=
  const handleToggleIsDone = id => {
    setTodos(todos.map(todo => {
      // 如果不是要修改的 todo id 就直接回傳
      if (todo.id !== id) return todo;
      // 要修改的 todo id
      return {
        // todo 原本的東西
        ...todo,
        // 要修改的屬性
        isDone: !todo.isDone
      }
    }));
  }
```

傳入參數到 TodoItem：

```javascript=
  {
    todos.map(todo => <TodoItem key={todo.id} todo={todo} handleDeleteTodo={handleDeleteTodo} handleTogglerIsDone={handleTogglerIsDone}/> ) 
  }
```

由 TodoItem 接收參數，可以把 click 事件抽出來寫，相較於原本的 inline function，能夠提高程式碼的可讀性：

```javascript=
export default function TodoItem({ todo, handleDeleteTodo, handleTogglerIsDone }) {
  const handleTogglerClick = () => {
    handleTogglerIsDone(todo.id);
  }

  const handleDeleteClick = () => {
    handleDeleteTodo(todo.id);
  }

  return (
    <TodoItemWrapper data-todo-id={todo.id}>
      <TodoContent $isDone={todo.isDone}>{todo.content}</TodoContent>
      <TodoButtonWrapper>
        <Button onClick={handleTogglerClick}>
          {todo.isDone ? '已完成' : '未完成'}
        </Button>
        <RedButton onClick={handleDeleteClick}>刪除</RedButton>
      </TodoButtonWrapper>
    </TodoItemWrapper>
  );
}
```

### Transient props：`$<props>`

在上方程式碼中，加在 TodoContent 的 $isDone 這個 props，會被視為 style component props，不會被繼續傳到下一個 DOM 元素，也就不會顯示在 TodoContent 標籤上。

如果沒有加上 $ 符號，這個 props 就會被直接加在 TodoContent 這個 DOM 結構上。

再以下方程式碼為例：

```javascript=
<TodoContent id="abc" $isDone={todo.isDone}>{todo.content}</TodoContent>
```

可以發現經過 render 之後，在 DOM 元素只會出現 `id="abc"` 這個屬性，而不會有 `$isDone`，這是因為 Transient props 不會被往下傳：

![](https://i.imgur.com/K4ROwak.png)

## Todo List 總結

透過實作簡單的 Todo List，其實我們就差不多學會了有關 React 的基礎：

1. Component 組件

開發 React 很重要的一點，就是去思考在頁面有哪些重複性高或相似的 Element，再透過 JSX 將這些 Element 建立成一個 Components，讓每個 Components 擁有重複性及可擴充性。

2. Props 參數

Props 主要提供值給 Component，用來設定屬性或資料，因此就算是同一個組件，也會根據提供的 Props 而有所不同。

3. Style 樣式

可透過幾種方式撰寫 React 中的 CSS，目前主流方法是透過 styled-components 這個套件來撰寫 CSS 語法。

4. Event handler 事件機制

和過去在網頁添加事件監聽不同，必須先以 `querySelector()` 選取 DOM 元素：

```javascript=
function sayHello() {
  alert('Hello!');
}
document.querySelector('.sayHello').addEventListener('click', sayHello);
```

而 React 把 DOM 和 JavaScript 程式碼寫在一起，因此可直接在 DOM 元素加上 onClick、onSubmit、onKeyDown 等事件監聽：

```javascript=
function TodoItem({ todo, handleDeleteTodo }) {
  const handleDeleteClick = () => {
    handleDeleteTodo(todo.id);
  }
  return (
    <RedButton onClick={handleDeleteClick}>刪除</RedButton>
  );
}

      /* 也可簡化成箭頭函式 */

function TodoItem({ todo, handleDeleteTodo }) {
  return (
    <RedButton onClick={() => {
      handleDeleteTodo(todo.id)
    }}>刪除</RedButton>
  );
}
```

5. JSX 語法

透過 JSX 語法，即可將 HTML 語法轉成 JavaScript 的形式，讓我們用來建立 React elements。

使用 JSX 時需注意下列幾點：

- class 是保留字，必須改寫成 className
- 可在大括號內寫程式碼，例如：`{ JS code }`，也因此 inline-style 需改為駝峰式命名
- 沒有迴圈的概念，也沒有 if-else 判斷式
  - 解決方法：透過三元運算子，或是邏輯運算子 && 進行判斷

此外，當我們要 render 一系列 list 的時候，會使用 `map()` 把資料變成一個陣列，然後 render 需要提供 key：

```javascript=
{
   todos.map(todo => <TodoItem key={todo.id} todo={todo} /> ) 
}
```

6. State 狀態

是 React 中最重要的觀念，可透過 useState 設定 state 初始值，再以 setState 去改變 state。state 會對應到一個 UI，一旦 state 有變動，就會自動呼叫 render()。

state 最基本的語法如下：

```javascript=
const [currentValue, setCurrentValue] = useState(initialValue);
```

在 React 當中，若要進行新增、編輯、刪除功能，雖然有許多方法能夠達成，但基本上有固定的作法：

- 新增功能：解構語法

```javascript=
  const handleButtonClick = () => {
    setTodos([
      {
        // 要新增的 todo
        id: id.current,
        content: value
        // 解構語法
      }, ...todos]
    );
    setValue('');
    id.current++;
  }
```

- 編輯功能：map()

```javascript=
  const handleToggleIsDone = id => {
    setTodos(todos.map(todo => {
      // 如果不是要修改的 todo id 就直接回傳
      if (todo.id !== id) return todo;
      // 要修改的 todo id
      return {
        // todo 原本的東西
        ...todo,
        // 要修改的屬性
        isDone: !todo.isDone
      }
    }));
  }
```

- 刪除功能：filter()

```javascript=
  const handleDeleteTodo = id => {
    // 留下該 id 以外的 todo
    setTodos(todos.filter(todo => todo.id !== id))
  }
```


---

## 結論

這和以往的思考模式其實很不一樣，像是在切好的 UI 畫面上新增各種功能；而 React 則是先思考 state 狀態，再去想會如何改變畫面。

記住一個重點，就是 Component 之間可透過 props 把 state 傳遞下去。並且，只要 state 所有變動，就會觸發 render() 來更新 UI 畫面。

參考資料：

- [常見的幾個 React hooks 教學-useState、useEffect、useRef](https://snh90100.medium.com/%E5%B8%B8%E8%A6%8B%E7%9A%84%E5%B9%BE%E5%80%8B-react-hooks-%E4%BB%8B%E7%B4%B9-usestate-useeffect-useref-40c9acd0cc4c)
- [React 性能優化大挑戰：一次理解 Immutable data 跟 shouldComponentUpdate](https://blog.techbridge.cc/2018/01/05/react-render-optimization/)