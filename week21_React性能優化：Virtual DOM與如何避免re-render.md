###### tags: `React` 
# [week 21] React 性能優化：Virtual DOM & 如何避免 re-render

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

## 前言

在 React 課程最一開始，也就是[實作 Todo List](https://hackmd.io/@Heidi-Liu/note-fe302-review) 作為範例有提到，新增 todo 時「透過更改資料，畫面再根據資料進行渲染」，可確保兩者永遠一致：

![](https://i.imgur.com/k1STU0n.png)

那時採取的是簡單暴力的作法，也就是當 State 改變，我們就清空畫面並重新渲染 DOM 元素：

![](https://i.imgur.com/vf17UN9.png)

但這其實會有效能問題，假如我們今天總共有 100 個 DOM，即使只修改其中一個，卻還是必須清空並重新 Render 這 100 個 DOM。

這樣其實非常沒有效率，應該只需要重新 Render 有更動的部分就好。那麼 React 是如何解決這個問題呢？其實核心概念就是「如何快速找到要改變的地方」。

## Reconciliation：React 的渲染機制

在 React 當中，透過 Reconciliation 渲染機制可以快速找出 State 改變的地方：

- 透過 State 的改變，render 出 Virtual DOM
- 透過比對 Virtual DOM 的不同，再更新真的 DOM 到畫面

![](https://i.imgur.com/ApLV4M6.png)

### 什麼是 Virtual DOM？

Virtual DOM 實際上就是用 JavaScript 物件來描述 DOM 結構。

當 DOM 的節點需要更動時，不會直接修改 DOM，而是透過 DOM diff 演算法比較 Virtual DOM 修改前與修改後的樹狀結構，再批次更新真實的 DOM 節點。

![](https://i.imgur.com/ADMTzi4.png)
（圖片來源：https://tutorialslink.com/Articles/What-is-Virtual-DOM/1580）

比如下方這段 DOM 結構：

```htmlmixed=
<div className="APP">
  <TodoItem>
    Watch a Movie
  </TodoItem>
</div>
``` 

若以 JavaScript 物件形式寫成 Virtual DOM：

```javascript=
{
  tag: 'div',
    props: {
      className: 'APP'
    }
  children: [
    {
      tag: 'TodoItem',
      children: 'Watch a Movie'
    }
  ]
}    
```

### 為什麼需要 Virtual DOM？

因為操作 DOM 這件事，其實會耗費很大的成本；而 React 背後運行機制，就是透過比對 Virtual DOM 來避免直接操作 DOM，藉此來提升效能。

除了提升效能這個優點，因為建立 Virtual DOM 這一虛擬層，我們能夠對程式碼進行更多操作，例如：

- 轉成真的 DOM 結構
- 轉成 Markdown 語法
- 轉成 mobile APP 語法

簡單總結 Virtual DOM 帶來的兩大優點：
- 在 State 改變時，透過比對 Virtual DOM 來判斷是否更新、建立真的 DOM，藉此優化效能
- 可透過 Virtual DOM 建立更多操作

參考文章：
- [Virtual DOM 概述](https://cythilya.github.io/2017/03/31/virtual-dom/)
- [Virtual DOM | 為了瞭解原理，那就來實作一個簡易 Virtual DOM 吧！](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/build-a-simple-virtual-dom-5cf12ccf379f)
- [從頭打造一個簡單的 Virtual DOM](https://blog.techbridge.cc/2019/02/04/vdom-from-scratch/)

---

## 如何避免 re-render？

透過 React 的 Function Component，我們能將頁面切割成許多 Component 來方便管理。

但需注意這樣的結構，一但 props 或 state 改變時就很容易觸發 re-render（重新渲染），這如果發生在大型專案，不斷重新渲染可能產生效能問題，加重伺服器的負擔。

### memo：Higher Order Component

React.memo 是一個 HOC（Higher Order Component），根據[官網說明](https://zh-hant.reactjs.org/docs/higher-order-components.html)：

> HOC: a higher-order component is a function that takes a component and returns a new component.
（把一個元件丟進 HOC 函數時，會經過一些處理並返回新元件）

用法如下：

```javascript=
const MyComponent = React.memo(function MyComponent(props) {
    ...
});
```

比如說，當子元件依賴於父元件的狀態（state）或事件（event），在父元件中宣告狀態與事件方法，可透過 props 將傳遞到子元件中。

但如果是在父元件的狀態被改變，props 結果不變的情況下，子元件即使沒有改變，仍然會被重新渲染，像這樣多餘的渲染就會造成效能浪費。

而 React.memo 這個 HOC 就負責比較元件當中的 props 有沒有改變，減少元件不必要的渲染。

以下方 input 作為範例：

<iframe height="265" style="width: 100%;" scrolling="no" title="React memo" src="https://codepen.io/heidiliu2020/embed/preview/rNMOpLY?height=265&theme-id=dark&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/heidiliu2020/pen/rNMOpLY'>React memo</a> by Heidi-Liu
  (<a href='https://codepen.io/heidiliu2020'>@heidiliu2020</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### useCallback

而 memo 與 useCallback 常會搭配使用，useCallback 讓 props 的 Object 在父元件重新渲染時，不重新分配記憶體位址，讓 memo 不會因為重新分配記憶體位址造成渲染。

### useMemo

useMemo 的用法則是無關於父元件，主要用在當元件重新渲染時，減少在元件中複雜的程式重複執行。

### 什麼情況需要進行優化

性能優化總是會有成本，但並不總是帶來好處。具體來說，使用這類型的 hooks 可能會複雜化程式碼；也可能反而使性能下降。因此，除非需要進行昂貴的運算，否則必要的情況下不建議使用。

參考資料：
- [React 性能優化那件大事，使用 memo、useCallback、useMemo](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/react-optimize-performance-using-memo-usecallback-usememo-a76b6b272df3)
- [關於props的記憶，React Memo (新增範例及說明)](https://ithelp.ithome.com.tw/articles/10240296?sc=iThomeR)
- [【譯】什麼時候使用useMemo 和useCallback](https://jancat.github.io/post/2019/translation-usememo-and-usecallback/)
- [React Hooks 第一期：聊聊useCallback](https://zhuanlan.zhihu.com/p/56975681)

---

## React 事件機制

在 React 的事件機制，其實是透過 root 這層進行事件代理，而不是綁在 button 等 DOM 節點上。

當我們開啟開發者工具，查看修改 todo 狀態 button 的監聽事件，會是以下畫面：

![](https://i.imgur.com/YKxqY4S.png)

但如果把 button 的 click 監聽事件給 remove，還是能夠更改狀態：

![](https://i.imgur.com/7jftRiR.png)

這是因為，React 是由上層的節點 root 進行事件監聽，也就是以 `<div id="root"></div>` 作為事件代理：

![](https://i.imgur.com/7I8Yekk.png)

React 透過這樣的事件機制能夠提升效能，也確保動態新增的元素能夠捕捉到事件機制。

---

## 補充：Virtual DOM 的起源

推薦這篇：[了不起的Virtual DOM(一)：起源](https://github.com/MrErHu/blog/issues/26)，有談到 Virtual DOM 的起源，其實任何技術或工具的誕生都是有原因的，例如框架是因應逐漸擴大的程式規模，為了可維護性與便於開發而出現。

前端在 MVC 模式中，隨著應用規模增加，Model 與 View 之間的複雜關係使得開發難度增加。

![](https://i.imgur.com/09TAbTq.png)

而為了改善 MVC 模式，之後就出現了 MVP 模式，將 MVC 中的 Controller 改為 Presenter，使 Model 和 View 兩者之間相互獨立，但這同樣有個缺點，就是 Presenter 需要承擔的功能過多，難以後續維護。

![](https://i.imgur.com/UOlvxGZ.png)

MVVM 結構是以 MVP 作為基礎進行改良，將原來的 Presenter 進化為 View Of Model（VM：視圖模型），負責 View 與 Model 的雙向綁定，將 View與 Model 之間的手動維護更新改為自動更新，這麼做的好處是大幅提高可維護性。但也需注意 ViewModel 構建與維護成本相對較高，複雜的視圖也帶來性能成本，因此不適用較簡單的頁面。

![](https://i.imgur.com/XaC6n6i.png)

直到 React 出現，解決思路就是「當 Model 發生改變時直接重新渲染 View」，引入 Virtual DOM 的概念。透過 JavaScript 物件來描述 DOM 結構，產生當下 Model 對應的 Virtual DOM 結構，再和上次的 Virtual DOM 結構透過 Diff 演算法比較，再將兩者差異建立在真實 DOM 上。


## 結論

其實關於 React 優化，還是不太瞭解該如何使用 hooks 來避免重新渲染，只有大概瞭解原理，還有並不是所有情況都是用性能優化。除非是在大型專案，需要進行昂貴的計算才可能會用到，因此這部分等之後有機會再來深入研究。
