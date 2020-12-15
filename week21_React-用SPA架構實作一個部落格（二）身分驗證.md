###### tags: `React` `SPA` `useContext` `useHistory`
# [week 21] React：用 SPA 架構實作一個部落格（二）- 身分驗證

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

> 參考文章：[淺談新手在學習 SPA 時的常見問題：以 Router 為例](https://blog.huli.tw/2019/09/18/spa-common-problem-about-router/)

---

## 如何進行身分驗證？

在實作登入功能之前，必須先瞭解下列兩種進行身分驗證的方法有何不同：

- 透過 Cookie 驗證，取得 Session ID
- 把 Session ID 存在瀏覽器的 LocalStorage 裡

### 透過 Cookie 驗證

與本篇要實作的 SPA 不同，過去我們通常是利用 Cookie 來驗證使用者登入狀態，流程大致如下：

![](https://i.imgur.com/giVnm0P.png)

1. 使用者在登入時會打一個 API 給 Server，Server 確認沒問題之後，會回傳包含 Set-Cookie 的 HTTP Response Header
2. 當使用者需要進行身分驗證時，會打一個 GET/me 的 API 給 Server，瀏覽器會自動帶入 Cookie，假如 Session ID 是正確的，Server 就會回傳 data，反之則回傳錯誤

### 把 Session ID 存在 LocalStorage

但是到了 SPA 之後，我們就比較少用 Cookie 來進行驗證，而是把 Session ID 存在瀏覽器的 LocalStorage 裡，每次發 Resquest 時會自動帶入資料，流程如下：

![](https://i.imgur.com/8xvtM4x.png)

1. 使用者在登入後，Server 會回傳一個 JSON Web Token（一種固定格式的資料），並且儲存在瀏覽器的 LocalStorage 裡
2. 當需要進行身分驗證時，就會自動在 header 帶上這個 JWT 給 Serever，確認沒問題後回傳 data

### 打一個 POST API 到 Server

我們可以試著透過 Postman 打 POST API 到 Server 測試：

```
POST/
https://student-json-api.lidemy.me/login

Body
{"username":"user01", "password":"Lidemy"}
```

若 username 和 password 驗證沒問題，Server 就會回傳一個以 base64 編碼的 JWT token：

```json=
{
    "ok": 1,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIwMSIsInVzZXJJZCI6MSwiaWF0IjoxNjA3NzQzMTA5fQ.FgTlsa57WOYNZEBj5HtL74uIVDuKFWErrmQ72qXuHmo"
}
```

結果如下：

![](https://i.imgur.com/QB2O67D.png)

把這段 JWT token 拿到 [jwt 官網](https://jwt.io/) 進行解析，可以轉換成 JSON 格式，因此不建議儲存一些敏感資訊（例如密碼、地址等）在 token：

![](https://i.imgur.com/IVpVipo.png)

### 透過 token 取得使用者資訊

接下來我們打一個 GET API 到 Server，並帶上剛才的 token，Server 就會回傳使用者資訊：

```
GET/
https://student-json-api.lidemy.me/me

Bearer Token
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIwMSIsInVzZXJJZCI6MSwiaWF0IjoxNjA3NzQzMTA5fQ.FgTlsa57WOYNZEBj5HtL74uIVDuKFWErrmQ72qXuHmo
```

結果如下：

![](https://i.imgur.com/QyTsG90.png)

學會如何透過打 API 拿到 token 進行身分驗證後，再來我們要實際應用在部落格的登入機制，那麼開始吧！

---

## 實作：登入功能

### 1. 設定串接 API 的方法：登入 & 身分驗證

同樣參考[ API 文件說明](https://github.com/Lidemy/lidemy-student-json-api-server#%E8%A8%BB%E5%86%8A)，在 WebAPI.js 新增 login 和 getMe 兩個 API：

```javascript=
// 登入
export const login = (username, password) => {
  return fetch(`${BASE_URL}/login`, {
    method: "POST",
    headers: {
      "content-type": "application/json",
    },
    body: JSON.stringify({
      username,
      password,
    }),
  }).then((res) => res.json());
};

// 身分驗證
export const getMe = () => {
  // 從 localStorage 拿取 token
  const token = localStorage.getItem("token");
  return fetch(`${BASE_URL}/me`, {
    headers: {
      authorization: `Bearer ${token}`,
    },
  })
    .then((res) => res.json())
};
```

### 2. 實作 LoginPage.js

可透過 onSubmit 與 onChange 事件機制，拿到 input.value 的值。

在 React 中，value 若為空值（undefined），等同於沒有傳 value，所以這裡初始值要參數設為空字串，也就是 `useState("")`：

```javascript=
export default function LoginPage() {
  // 在 React 中 value 若是 undefined，等同於沒有傳 value
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");

  // 阻止送出表單
  const handleSubmit = (e) => {
    e.preventDefault();
    // 確認是否有抓到 username
    alert(username);
  };

  const handleUsername = (e) => {
    setUsername(e.target.value);
  };

  const handlePassword = (e) => {
    setPassword(e.target.value);
  };
  return (
    <Root>
      <LoginForm onSubmit={handleSubmit}>
        <LoginTitle>登入</LoginTitle>
        <LoginInput>
          username: <input value={username} onChange={handleUsername} />
        </LoginInput>
        <LoginInput>
          password:
          <input type="password" value={password} onChange={handlePassword} />
        </LoginInput>
        <LoginSubmit>
          <button>登入</button>
        </LoginSubmit>
      </LoginForm>
    </Root>
  );
}
```

結果如下，按下 button 成功拿取 input.value：

![](https://i.imgur.com/pJDBs0Z.png)

### 3. 建立 utils.js 管理常用功能

在拿取 token 時，其實可以再進行優化，例如把 token 相關的程式碼獨立到 utils.js 管理，即可避免打錯字：

```javascript=
const TOKEN_NAME = "token";

// 將 token 存到 localStorage
export const setAuthToken = (token) => {
  localStorage.setItem(TOKEN_NAME, token);
};

// 從 localStorage 讀取 token
export const getAuthToken = () => {
  return localStorage.getItem(TOKEN_NAME);
};
```

### 4. 串接 API：login

- WebAPI.js：引入使用 getAuthToken()，從 localStorage 讀取 token 的值：

```javascript=
import { getAuthToken } from "./utils";

// 身分驗證
export const getMe = () => {
  // 從 localStorage 讀取 token
  const token = getAuthToken();
  return fetch(`${BASE_URL}/me`, {
    headers: {
      authorization: `Bearer ${token}`,
    },
  })
    .then((res) => res.json())
};
```

- LoginPage.js：使用 setAuthToken() 儲存 token：

```javascript=
import { login } from "../../WebAPI";
import { setAuthToken } from "./utils";

export default function LoginPage() {
  // 在 React 中 value 若是 undefined，等同於沒有傳 value
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [errorMessage, setErrorMessage] = useState();

  // 阻止送出表單
  const handleSubmit = (e) => {
    e.preventDefault();
    login(username, password).then((data) => {
      // 若 ok 為 0 代表錯誤
      if (data.ok === 0) {
        return setErrorMessage(data.message);
      }
      // 成功的話就把 token 存到 localStorage
      setAuthToken(data.token);
    });
  };
  
  // ...
```

確認是否有成功透過 localStorage 存取 token：

![](https://i.imgur.com/mDMvUs9.png)

### useHistory：跳轉頁面

登入成功之後，接著要把使用者導回首頁，可透過 react-router 提供的 Hooks：[useHistory](https://reactrouter.com/web/api/Hooks/usehistory) 來主動跳轉路由。

- 引入套件：

```javascript=
import { useHistory } from "react-router-dom";
```

- 將程式碼修改如下，即可在登入後跳轉至首頁：

```javascript=
  const history = useHistory();

  const handleSubmit = (e) => {
    e.preventDefault();
    login(username, password).then((data) => {
      if (data.ok === 0) {
        return setErrorMessage(data.message);
      }
      // 成功的話就把 token 存到 localStorage
      setAuthToken(data.token);
      // 並導回首頁
      history.push("/");
    });
  };
```

## 實作：身分驗證

在完成登入功能後，在以 getMe() 拿到使用者資訊之前都還不算登入成功，我們還需要保持登入狀態，也就是把使用者資料透過 Context 存到全域環境中，才能傳給底下的每個 Component 使用。

### 1. createContext：傳入預設值

- contexts.js

為了讓其他 Component 也能讀取 user 資料，可在 src 目錄底下建立一個 contexts.js，即可透過 AuthContext() 來取值：

```javascript=
import { createContext } from "React";

// 初始值為 null
export const AuthContext = createContext(null);
```

- App.js

把登入狀態存在 Component 的最頂端，只要在 App.js 存 user 狀態，即可透過有無 user 判斷是否登入：

```javascript=
export default function App() {
  // user 有東西就代表有登入
  const [user, setUser] = useState(null);
  
  //...
```

### 2. Context Provider：提供子層 value

- App.js

用 Context Provider 包住整個組件，並設定 value，這裡可傳入物件型態的參數，把 `{user, setUser}` 傳給子層：

```javascript=
import AuthContext from "../../contexts";
// ...
  return (
    <AuthContext.Provider value={{user, setUser}}>
      <Root>
       // 路由配置 ...        
      </Root>
    </AuthContext.Provider>
  );
}
```

- LoginPage.js

引入 AuthContext 取得 user 資訊，用 setUser 儲存狀態，並以 getMe 發出 resquest 進行身分驗證：

```javascript=
import { AuthContext } from "../../contexts";

export default function LoginPage() {
  const { setUser } = useContext(AuthContext);
  
// ...

  const handleSubmit = (e) => {
    e.preventDefault();
    login(username, password).then((data) => {
      if (data.ok === 0) {
        return setErrorMessage(data.message);
      }
      setAuthToken(data.token);
      getMe().then((response) => {
        if (data.ok !== 1) {
          // 在 getMe() 出錯代表還沒成功登入，因此要把 token 清空
          setAuthToken(null);
          setErrorMessage(response.toString());
        }
        setUser(response.data);
        // 並導回首頁
        history.push("/");
```

- Header.js

接著就可以根據登入狀態，判斷導覽列是否顯示「登入、註冊」或是「發布文章、登出」。

注意這裡的判斷式裡面，只能包含一個 JSX 標籤，否則會出錯，例如 `{user && <Link>...</Link>}`：

```javascript=
export default function Header() {
  const location = useLocation();
  const { user, setUser } = useContext(AuthContext);
  const history = useHistory();

  const handleLogout = () => {
    setAuthToken("");
    setUser(null);
    if (location.pathname !== "/") {
      history.push("/");
    }
  };

  return (
    <HeaderContainer>
      <Brand>
        {/* 加上 replace: 避免出現錯誤 -> "Hash history cannot PUSH the same path" */}
        <Link to="/" replace>
          React 部落格
        </Link>
      </Brand>
      <NavbarList>
        <StyledLink exact to="/about" replace activeClassName="active">
          關於我
        </StyledLink>
        <StyledLink exact to="/posts" replace activeClassName="active">
          文章列表
        </StyledLink>
        {!user && (
          <StyledLink to="/register" activeClassName="active">
            註冊
          </StyledLink>
        )}
        {!user && (
          <StyledLink to="/login" activeClassName="active">
            登入
          </StyledLink>
        )}
        {user && (
          <StyledLink to="/new-post" activeClassName="active">
            發布文章
          </StyledLink>
        )}
        {user && (
          <StyledLink to="" onClick={handleLogout}>
            登出
          </StyledLink>
        )}
      </NavbarList>
    </HeaderContainer>
  );
}
```

結果如下：

![](https://i.imgur.com/NLbXTNr.png)

但這麼寫還有個問題，就是重新整理之後，又會變成未登入狀態，其實 localStorage 還是有 token 存在。

### 3. useEffect：在 reneder 之後驗證身分

可使用 useEffect 來解決這個問題，如此一來，在畫面 mount 的時候，就會透過 call getMe API 來驗證身分：

```javascript=
export default function App() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // 以 getAuthToken 從 localStorage 讀取 token
    if (getAuthToken()) {
      // 有 token 才 call API
      getMe().then((response) => {
        if (response.ok) {
          setUser(response.data);
        }
      });
    }
  }, []);
  
// ...
```


---

## 補充功能

### 1. styled-reset：CSS Reset

React 也可搭配 styled-reset 套件，進行 CSS Reset 規格化，詳細參考[官方文件](https://github.com/zacanger/styled-reset#readme)。

安裝套件：

```
$ npm i styled-reset
```

引入 styled-reset 套件使用：

```javascript=
import React from 'react'
import { Reset } from 'styled-reset'

const App = () => (
  <React.Fragment>
    <Reset />
    <div>Hi, I'm an app!</div>
  </React.Fragment>
)
```

### 2. 在 Root 設置全域背景圖片

在 App.js 引入 img：

```javascript=
import img from "../../images/bg.jpg";
```

以 styled-component 調整 App 中 Root Component 的 css，就會 render 到所有 pages：

```javascript=
const Root = styled.div`
  font-family: "monospace", "微軟正黑體";
  color: #4a4a4a;
  box-sizing: border-box;
  padding: 80px 10px;
  background: #fff url(${img}) center center fixed no-repeat;
  background-size: cover;
  height: 100%;
`;
```

以 LoginPage.js 為例，可針對不同頁面進行微調：

```javascript=
const Root = styled.div`
  height: 100vh;
  padding-top: 100px;
`;
```

### 3. 優化：畫面閃爍問題

在登入狀態時，重整畫面會出現畫面閃爍的問題，如下圖所示：

![](https://i.imgur.com/bzRXGRP.gif)

之所以會有這個現象，是因為畫面進行了兩次 render：

- 預設為登出狀態（第一次 render）
- 當我們發 API 確認有登入之後，才會顯示登入狀態（第二次 render）

要改善這個問題的核心概念在於：

#### 在確認是否登入之前，不要顯示和登入登出狀態有關的東西。

在 APP 執行開始，可以有新的 state（isLoadingGetMe 或是 isGettingUser），預設值為 ture，也就是不顯示登入登出。

一旦接收到 getMe() 回傳的 response 時，或是發現沒有 token 時，就會改成 false，顯示登入登出。

也可以實作一個 Loading 畫面，等到渲染完成再顯示頁面。

---

## 結語

在實作部落格時可以發現，我們在最一開始雖然擬定了專案架構，實際上還是會在實作的過程進行調整，最後構成的專案架構如下：

- src
  - components 組件
    - App 管理路由、設置全域變數
    - Header 導覽列
    - Footer 置底訊息
  - constants 常數
  - pages 頁面
    - HomePage 首頁（顯示最新五篇）
    - AboutPage 關於我
    - PostListPage 文章列表頁面
    - PostPage 單一文章頁面
    - NewPostPage 發布文章頁面
    - LoginPage 登入頁面
    - Register 註冊頁面
  - WebAPI.js 管理 API
  - utils.js 管理常用方法
  - contexts.js 管理傳給子層的 value

一旦 components 資料夾中，結構變得更複雜時，會再根據不同功能來區分資料夾，例如 common、post，注意當檔案是 compontent 時，才會以大寫英文開頭命名。

