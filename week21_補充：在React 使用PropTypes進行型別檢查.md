###### tags: `React` `PropTypes`
# [week 21] 補充：在 React 使用 PropTypes 進行型別檢查

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

之前介紹過 Prettier 和 eslint 這兩個前端工具，能夠分別檢查程式碼格式，以及檢查語法：

- [[week 21] 補充：Prettier 套件 - 自動整理程式碼格式](https://hackmd.io/@Heidi-Liu/note-prettier)
- - [[week 3] 設定 eslint：用來檢查語法的工具](https://hackmd.io/@Heidi-Liu/note-eslint)

在 VSCode 可以針對錯誤進行快速修復，也可以選擇加入註解，忽略 eslint 的錯誤訊息：

![](https://i.imgur.com/NPyX5Mj.png)

除此之外，React 有另一個能夠提升程式碼品質的功能，也就是透過 PropTypes 來驗證 Props 型態。

## 利用 PropTypes 驗證 Props

> 詳細可參考官方文件：[Typechecking With PropTypes](https://zh-hant.reactjs.org/docs/typechecking-with-proptypes.html)

在使用前，需要先修改 eslint 規則，也就是在 react 專案新增 .eslintrc.json 檔案。


內容如下，或是參考 [yannickcr/eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)，意思是專案中的每個 component 都要加上 props，否則會出現警告訊息：

```json=
{
  "extends": ["react-app"],
  "rules": {
    "react/prop-types": "warn"
  }
}
```

之後輸入 npm start 指令運行 React，就會在終端機介面看到警告訊息。

接下來會以 TodoItem component 作為範例。

### 引入使用 ProtoTypes

首先要在 TodoItem.js 引入使用 ProtoTypes：

```javascript=
import PropTypes from 'prop-types';
```

接著幫 TodoItem component 的 props 加上 PropTypes，注意 function 在這裡是縮寫 func：

```javascript=
TodoItem.propTypes = {
  todo: PropTypes.object,
  handleDeleteTodo: PropTypes.func,
  handleToggleIsDone: PropTypes.func,
};
```

也可以將 object 寫得更詳細，描述物件中的 key-value 以及型別：

```javascript=
TodoItem.propTypes = {
  todo: PropTypes.shape({
    id: PropTypes.number,
    content: PropTypes.string,
    isDone: PropTypes.bool,
  }),
  handleDeleteTodo: PropTypes.func,
  handleToggleIsDone: PropTypes.func,
};

```

加上 PropTypes 進行修改的好處在於：

- Console 不會再出現 warning
- 未來若有其他人接手專案時，能夠快速瀏覽這個 Component 有哪些參數，以及瞭解 PropTypes 型別
- 也能透過一些 Library 將 PropTypes 產生一份文件
