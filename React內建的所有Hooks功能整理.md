###### tags: `React` `Hooks`
# [學習筆記] React 內建的所有 Hooks 功能整理

> 本篇是參考[官方文件](https://zh-hant.reactjs.org/docs/hooks-reference.html)，整理 React 內建的 Hooks API 功能。

- Basic Hooks
  - useState
  - useEffect
  - useContext
- Additional Hooks
  - useReducer
  - useCallback
  - useMemo
  - useRef
  - useImperativeHandle
  - useLayoutEffect
  - useDebugValue

## Basic Hooks

### useState：用來設定 component 的 state

- 第一次 render 時，回傳的 state 值會和 initialState 參數相同
- 可透過 setState 來更新 state，state 一旦改變，就會觸發 React 去重新渲染畫面

```javascript=
const [state, setState] = useState(initialState

setState(newState);
```

### useEffect：用來告訴 React component 在 render 之後要做的事情

- 第一個參數帶入的 function 會在「畫面渲染完成後」被呼叫
- 第二個參數傳入一個陣列，用來放想要關注的資料，當變數改變時才會呼叫 useEffect

```javascript=
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source],
);
```

### useContext：解決跨多層傳遞資料的問題，就像建立了全域變數

- 讓父層的資料能夠被底下的任意子層存取，不需要像 props 一層一層傳遞下去，避免造成 Props drilling
- 用 `React.createContext` 建立一個 context 物件，並由 `<MyContext.Provider value={}>` 存取該物件的值，底下的子層就可以直接透過 useContext 來存取 MyContext

```javascript=
const value = useContext(MyContext);
```

<br/>

## Additional Hooks

### useReducer：是 useState 的替代方案，當 state 邏輯變得複雜，需要操作多種 state 時可使用

- state：當前的 state 值
- dispatch：透過參數來和 function 溝通，藉此控制處理方式
- reducer： 用來接受一個 `(state, action) => newState`，並回傳當前的 state 和對應的 dispatch 方法
- initState：設定 state 的初始值

```javascript=
const [state, dispatch] = useReducer(reducer, initState);
```

### useCallback：用來記憶父元件的記憶體位置，避免在重新渲染時被重新分配

- 第一個參數是 function，第二個參數是其依賴陣列，會回傳一個 memoized 的 callback
- 在重新渲染時，只會在依賴改變時才更新，防止不必要的渲染，減少效能上的消耗

```javascript=
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### useMemo：用途是當 component 重新渲染時，能避免複雜的程式被重複執行

- 第一個參數是 function，第二個參數是其依賴陣列，會回傳一個 memoized 的值
- 在重新渲染時，傳到 useMemo 的 function 就只會在依賴改變時才執行，將 memoized 更新成回傳的值

```javascript=
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### useRef：用來抓取 DOM 節點，存放的值不會受到 render 影響

- 會回傳一個 mutable 的 ref object，其 `.current` 屬性會被初始為傳入的參數 initialValue
- 當 `.current` 屬性有變動時不會觸發重新 render，而每次 render 時都會給同一個 ref object

```javascript=
const refContainer = useRef(initialValue);

console.log(refContainer.current)
// initialValue
```

### useImperativeHandle：可以在父層調用子層中 ref，選取指定的 DOM 節點

- 第一個參數是接收的 ref
- 第二個參數是傳給父層的方法

```javascript=
useImperativeHandle(ref, createHandle, [deps])
```

### useLayoutEffect：功能與 useEffect 相似，差別在於 useLayoutEffect 會在 render 之前執行

### useDebugValue：可用來在 React DevTools 中顯示自訂義 hook 的標籤

```javascript=
useDebugValue(value)
```
