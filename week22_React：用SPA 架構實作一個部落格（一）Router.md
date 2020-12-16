###### tags: `React` `SPA` `React Router` `API` `useParams`
# [week 22] React：用 SPA 架構實作一個部落格（一）- Router

> 本篇為 [[FE302] React 基礎 - hooks 版本](https://lidemy.com/p/fe302-react-hooks) 這門課程的學習筆記。如有錯誤歡迎指正！

> 參考文章：[淺談新手在學習 SPA 時的常見問題：以 Router 為例](https://blog.huli.tw/2019/09/18/spa-common-problem-about-router/)

之前實作的留言板只有單一頁面，但隨著專案規模越大，需要藉由路由來渲染不同頁面時，就需要路由進行配置與管理，而 react-router-dom 套件就有提供這個功能。

接下來我們使用 React 搭配 Router，實作一個 SPA（Single Page Application）架構的部落格。

---

## React Router：管理專案路由

可參考[官方文件](https://reactrouter.com/web/guides/quick-start)，我們可透過 React Router 套件來管理 URL 路由。

### 安裝 react-router-dom

```
$ npm install react-router-dom
```

### 用 Component 的概念設計 Router

React Router 同樣是要用寫 React 方式去理解，也就是以 Component 的概念去設計一個 Router。

這其實和我們之前寫 Back-End 時很不一樣，例如 `app.get('/comment')` ，代表讀取留言的路由。

可參考[官方](https://reactrouter.com/web/example/basic)提供的範例：

<iframe src="https://codesandbox.io/embed/react-router-basic-bnpsd?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:300px; border:0; border-radius: 4px; overflow:hidden;"
     title="React Router - Basic"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

以下是上方範例的程式碼，透過連結改變的網址，由 Router 決定要 render 的畫面：

```javascript=
export default function BasicExample() {
  return (
    <Router>
      <div>
<!--    點選連結改變網址    -->
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <Link to="/dashboard">Dashboard</Link>
          </li>
        </ul>

        <hr />
<!-- 根據不同網址 render 相對應頁面 -->
        <Switch>
          <Route exact path="/">
            <Home />
          </Route>
          <Route path="/about">
            <About />
          </Route>
          <Route path="/dashboard">
            <Dashboard />
          </Route>
        </Switch>
      </div>
    </Router>
```

### BrowserRouter vs HashRouter

而在引入 Router 時，其實有兩種方式：

- BrowserRouter：直接在網址帶入路徑，但這種方式在 GitHub Pages 上其實會出現問題

```javascript=
import {
  BrowserRouter as Router,
  Switch,
  Route,
  Link
} from "react-router-dom";
```

如果是從首頁點選 dashboard，前端就會透過 JavaScript 提供的 API 把網址改成 /dashboard，能夠正常 render 畫面。

但如果是直接在網址後帶上 /dashboard，GitHub Pages 會去找 dashboard 資料夾底下的 index.html，此時瀏覽器會直接發 request 到該頁面，發生不如預期的錯誤。

- HashRouter：會在網址加上 `/#/`，瀏覽器就會去載入 `#` 符號之前的網址，即可改善上述問題

```
Home  https://bnpsd.csb.app/#/
dashboard  https://bnpsd.csb.app/#/dashboard
```

有了以上關於 Router 的基本概念後，就來繼續實作專案吧！

### 實作：管理專案 Router

#### 1. App.js：根據不同 Component 管理路由

```javascript=
import React, { useState, useEffect } from "react";
import styled from "styled-components";
import PropTypes from "prop-types";
import LoginPage from "../../pages/LoginPage";
import HomePage from "../../pages/HomePage";
import Header from "../Header";

import { HashRouter as Router, Switch, Route } from "react-router-dom";

const Root = styled.div``;

export default function App() {
  return (
    <Root>
      {/* Router: 包在最外層 */}
      <Router>
        {/* 導覽列: 共同區塊 */}
        <Header />
        {/* Switch: 確保只會匹配第一個符合網址列的路由 */}
        <Switch>
          {/* exact path: 代表完整匹配；若只有 path 是部分匹配 */}
          <Route exact path="/">
            <HomePage />
          </Route>
          <Route exact path="/login">
            <LoginPage />
          </Route>
        </Switch>
      </Router>
    </Root>
  );
}
```

2. 整理專案結構

- src
  - components
    - App
    - Header 導覽列（共同區塊）
  - pages
    - HomePage 首頁
    - LoginPage 登入頁面

以建立 HomePage Component 為例，Header 和 LoginPage 也是用這個模式：

```javascript=
// HomePage.js
import React, { useState, useEffect } from "react";

export default function HomePage() {
  return <div>Home Page</div>;
}
```

而為了調整專案結構，需在 index.js 引入並引出 HomePage.js，可參考[上篇筆記](https://hackmd.io/@Heidi-Liu/note-fe302-react-board)：

```javascript=
// index.js
export { default } from "./HomePage";
```

執行結果如下，可透過不同路由 render 相對應的頁面，其中 Header 是共同區塊不會變動：

![](https://i.imgur.com/ff3aVd1.png)


參考資料：
- [淺談新手在學習 SPA 時的常見問題：以 Router 為例](https://blog.huli.tw/2019/09/18/spa-common-problem-about-router/)
- [[React] 搭配 React Router 打造一個動態麵包屑（dynamic breadcrumb）](https://pjchender.blogspot.com/2018/11/react-react-router-dynamic-breadcrumb.html)

---

## 實作：從切板開始！

## 1. 切板與整合 react router

瞭解到如何管理路由之後，再來就是透過 component 切出想要的畫面。

首先進行 Header component 導覽列連結的部分。

### 方法一：透過 Link、useLoction

- 使用 useLocation 讀取當前位置，再透過 $active 這個屬性判斷符合哪個路徑，render 出相對應畫面：

```javascript=
// 引入使用 Link, useLocation
import { Link, useLocation } from "react-router-dom";

const location = useLocation();

<Nav to="/" $active={location.pathname === "/"}>
```

程式碼如下：

```javascript=
import React, { useState, useEffect } from "react";
import styled from "styled-components";

import { Link, useLocation } from "react-router-dom";

const HeaderContainer = styled.div`
  height: 58px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
  box-shadow: 0px 0px 6px rgb(199, 197, 197);
  padding: 0px 32px;
`;

const Brand = styled.h1`
  margin: 0;
`;

const NavbarList = styled.ul`
  display: flex;
  align-items: center;
  list-style-type: none;
  text-decoration: none;
  margin: 0;
  padding: 0;
`;

const Nav = styled(Link)`
  height: 58px;
  width: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-decoration: none;
  color: #666;

  ${(props) =>
    props.$active &&
    `
      background: #eee;
      color: #222;
  `}
`;

const LeftNavbar = styled.div`
  display: flex;
  align-items: center;

  /* 代表在 LeftNavbar 底下的 NavbarList */
  ${NavbarList} {
    margin-left: 32px;
  }
`;

export default function Header() {
  // 可拿到目前路徑
  const location = useLocation();

  return (
    <HeaderContainer>
      <LeftNavbar>
        <Brand>
          <Link exact to="/">
            React 部落格
          </Link>
        </Brand>
        <NavbarList>
          <Nav to="/" $active={location.pathname === "/"}>
            首頁
          </Nav>
          <Nav to="/new-post" $active={location.pathname === "/new-post"}>
            發布文章
          </Nav>
        </NavbarList>
      </LeftNavbar>
      <NavbarList>
        <Nav to="/login" $active={location.pathname === "/login"}>
          登入
        </Nav>
      </NavbarList>
    </HeaderContainer>
  );
}
```

### 方法二：NavLink

除了使用 useLocation 來判斷當前路徑，React Router 還有提供 [NavLink](https://reactrouter.com/web/api/NavLink) 這個特殊的 Component，具有以下屬性：

- activeClassName(string)：設置選中樣式，預設為 active
- activeStyle(object)：當元素被選中時，為此元素添加樣式
- exact(bool)：為 true 時，只有當完全符合時才會應用
- isActive(func)：判斷連結是否執行額外功能

參考資料：
- [<NavLink>的介紹與使用](https://www.jianshu.com/p/fcb87e3b4da4)
- [React手册之Link和NaviLink区别](https://blog.csdn.net/weixin_43732512/article/details/103182831?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control)

可透過 activeClassName 屬性或 inline style 行內樣式，來表示 NavLink 有無被選取，官方提供的範例如下：

#### 引入使用 NavLink

```javascript=
import { NavLink } from 'react-router-dom'
```

#### 1. 一般寫法

```javascript=
<NavLink to="/about">About</NavLink>
```

#### 2. activeClassName: string

```javascript=
<NavLink to="/faq" activeClassName="selected">
  FAQs
</NavLink>
```

#### 3. activeStyle: object

```javascript=
<NavLink
  to="/faq"
  activeStyle={{
    fontWeight: "bold",
    color: "red"
  }}
>
  FAQs
</NavLink>
```

但在實際應用的時候，有遇到個問題，就是如果想搭配 style-component 使用，會不知該如何傳入 activeClassName 這個 props！

- [How do I add an active class to a Link from React Router?](https://stackoverflow.com/questions/34418254/how-do-i-add-an-active-class-to-a-link-from-react-router)


針對如何在 NavLink 組件中使用 activeClassName 屬性，參考一些網路上的範例進行改寫，以下示範兩種作法：

#### 1. 透過 styled component 在 NavLink component，使用 .attr() 自訂屬性 activeClassName：

```javascript=
const activeClassName = 'nav-item-active'

const StyledLink = styled(NavLink).attrs({ activeClassName })`

  &.${activeClassName} {
    background: #eee;
  }
`;

// ...
      <NavbarList>
        <StyledLink exact to="/">首頁</StyledLink>
        <StyledLink to="/new-post">發布文章</StyledLink>
      </NavbarList>
    </LeftNavbar>
    <NavbarList>
      <StyledLink to="/login">登入</StyledLink>
    </NavbarList>
```

可參考下方範例：

<iframe src="https://codesandbox.io/embed/34z5152z1q?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:300px; border:0; border-radius: 4px; overflow:hidden;"
     title="Styling React Router - NavLink using Styled-Components "
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

#### 2. 將 activeClassName 視為一個 props，當 NavLink 被選中時，才會加上 activeClassName 屬性：

```javascript=
const StyledLink = styled(NavLink)`

&.${(props) => props.activeClassName} {
    background: #eee;
  }
`;

export default function Header() {
  return (
    <HeaderContainer>
      <LeftNavbar>
        <Brand>
          <Link exact to="/">
            React 部落格
          </Link>
        </Brand>
        <NavbarList>
          <StyledLink exact to="/" activeClassName="active">
            首頁
          </StyledLink>
          <StyledLink to="/new-post" activeClassName="active">
            發布文章
          </StyledLink>
        </NavbarList>
      </LeftNavbar>
      <NavbarList>
        <StyledLink to="/login" activeClassName="active">
          登入
        </StyledLink>
      </NavbarList>
    </HeaderContainer>
  );
}
```

其他參考資料：

- [React Router - Basic](https://codesandbox.io/s/zr8xm0910p?file=/example.js)：使用 isActive 屬性來判斷要 render 哪個 component
- [React 中 Link 和 NavLink 组件 activeClassName、activeStyle 属性不生效的问题](https://www.cnblogs.com/wenruo/p/10321456.html)

## 實作：文章列表頁面

測試用的 API 同樣參考：Lidemy 學生專用 API Server，部落格要串接的是 [Posts API](https://github.com/Lidemy/lidemy-student-json-api-server#posts)，資料結構如下：

URL：https://student-json-api.lidemy.me/posts?userId=1

![](https://i.imgur.com/B6N7n53.png)

### 1. 串連 API：拿取所有 posts

通常會在 src 路徑底下，新增一個 WebAPI.js 專門用來管理串連 API 相關程式碼。

如下方程式碼，使用 fetch 串接 API 再進行資料處理：

```javascript=
const BASE_URL = "https://student-json-api.lidemy.me";

export const getPosts = () => {
  return fetch(`${BASE_URL}/posts?_sort=createdAt&_order=desc`).then((res) =>
    res.json()
  );
};
```

### 2. 顯示文章標題 & 時間

在文章列表頁面，我們希望能夠顯示文章標題（title）和時間（createdAt）這兩個資訊：

```javascript=
// HomePage.js

import React, { useState, useEffect } from "react";
import styled from "styled-components";
import PropTypes from "prop-types";

import { Link } from "react-router-dom";
import { getPosts } from "../../WebAPI";

const Root = styled.div`
  max-width: 80%;
  margin: 0 auto;
`;

const PostContainer = styled.div`
  border-bottom: 1px solid rgba(0, 0, 0, 0.3);
  padding: 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
`;

const PostTitle = styled(Link)`
  font-size: 24px;
  color: #333;
  text-decoration: none;
`;

const PostDate = styled.div`
  color: rgba(0, 0, 0, 0.8);
`;

// PostList component: 顯示文章列表
function PostList({ post }) {
  return (
    <PostContainer>
      <PostTitle to={`/posts/${post.id}`}>{post.title}</PostTitle>
      <PostDate>{new Date(post.createdAt).toLocaleDateString()}</PostDate>
    </PostContainer>
  );
}

PostList.propTypes = {
  post: PropTypes.object,
};

export default function HomePage() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    getPosts().then((posts) => setPosts(posts));
  }, []);

  return (
    <Root>
      {posts.map((post) => (
        <PostList post={post} />
      ))}
    </Root>
  );
}

```

- 轉換時間格式

```javascript=
new Date(post.createdAt).toLocaleDateString()
```

- 用 map() 處理 List 結構的資料：

```javascript=
  {posts.map((post) => (
    <Post post={post} />
  ))}
```

- title 改用連結，引入 Link component 使用

```javascript=
import { Link } from "react-router-dom";

const PostTitle = styled(Link)`
  font-size: 24px;
  color: #333;
  text-decoration: none;
`;

// ...
  // 用 to={} 設定點擊導向的路由
  <PostTitle to={`/posts/${post.id}`}>{post.title}</PostTitle>
```

結果如下：

![](https://i.imgur.com/dny0n1U.png)

## 實作：單一文章頁面

### 1. 串聯 API：根據不同 id 拿取 post

接著是單一文章頁面，當我們在 Router 使用動態參數來讀取個別資料時，會需要取得 URL 上的 id 值。

在 WebAPI.js 中，根據路由上不同 id 來拿取相對應的 post：

```javascript=
export const getPost = (id) => {
  return fetch(`${BASE_URL}/posts?id=${id}`).then((res) => res.json());
};
```

### 2. useParams：抓取 URL 上的指定值

透過 react-router 提供的 Hooks：[useParams](https://reactrouter.com/web/api/Hooks/useparams) 就能更方便取得 id 值，而不需再透過 `props.match.params` 抓取 URL 路由的參數值。

以下是官方文件提供的範例，這裡指定的值就是 `{slug}`：

```javascript=
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Switch,
  Route,
  useParams
} from "react-router-dom";

function BlogPost() {
  // 指定參數值
  let { slug } = useParams();
  return <div>Now showing post {slug}</div>;
}

ReactDOM.render(
  <Router>
    <Switch>
      <Route exact path="/">
        <HomePage />
      </Route>
      // 抓取 URL 上的指定參數值
      <Route path="/blog/:slug">
        <BlogPost />
      </Route>
    </Switch>
  </Router>,
  node
);
```

#### 3. App.js 設定路由

新增 PostPage 和 NewPostPage 的路由，並在 src\pages 資料夾建立 pages component 引入使用：

```javascript=
// 統一從 pages 的 index.js 引入
import {
  HomePage,
  NewPostPage,
  PostPage,
  LoginPage,
} from "../../pages";

// ...

  <Switch>
    {/* exact path: 完整匹配 */}
    <Route exact path="/">
      <HomePage />
    </Route>
    <Route exact path="/posts/:id">
      <PostPage />
    </Route>
    <Route exact path="/new-post">
      <NewPostPage />
    </Route>
    <Route exact path="/login">
      <LoginPage />
    </Route>
  </Switch>
```

稍微整理專案結構，在 `src\pages` 建立 index.js 來統一處理 pages 的引入引出動作：

```javascript=
import HomePage from "./HomePage";
import LoginPage from "./LoginPage";
import PostPage from "./PostPage";
import NewPostPage from "./NewPostPage";

export { HomePage, LoginPage, PostPage, NewPostPage };
```

#### 4. 實作 PostPage.js

```javascript=
import React, { useState, useEffect } from "react";
import styled from "styled-components";

import { useParams } from "react-router-dom";
import { getPost } from "../../WebAPI";

const PostContainer = styled.div`
  padding: 0 30px;
  max-width: 960px;
  margin: 8px auto;
`;

const PostHeader = styled.div`
  margin-bottom: 16px;
  padding: 16px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
`;

const PostTitle = styled.div`
  font-size: 36px;
  font-weight: 700;
`;

const PostDate = styled.div`
  font-size: 16px;
  color: rgba(0, 0, 0, 0.4);
  margin-top: 16px;
`;

const PostBody = styled.div`
  font-size: 20px;
  letter-spacing: 3px;
  line-height: 1.5;
`;

export default function PostPage() {
  const [post, setPost] = useState(null);
  const { id } = useParams();

  useEffect(() => {
    getPost(id).then((post) => setPost(post[0]));
  }, [id]);

  return (
    <PostContainer>
      <PostHeader>
        {/* post &&: 確認陣列裡面有東西才會執行 */}
        <PostTitle>{post && post.title}</PostTitle>
        <PostDate>
          {post && new Date(post.createdAt).toLocaleString()}
        </PostDate>
      </PostHeader>
      <PostBody>{post && post.body}</PostBody>
    </PostContainer>
  );
}
```

- 用 useParams() 讀取網址列上的 id 值，並透過 useEffect 在 render 之後拿取資料，再以 setPost 來改變狀態：

```javascript=
  const [post, setPost] = useState(null);
  const { id } = useParams();

  useEffect(() => {
    getPost(id).then((post) => setPost(post[0]));
  }, [id]);
```

- 需以 `post &&` 確認陣列裡面有東西才會執行，以 `post.title` 為例：

```javascript=
<PostTitle>{post && post.title}</PostTitle>
```

- 顯示日期時間：

```javascript=
new Date(post.createdAt).toLocaleString()
// "2020/12/12 上午10:48:39"

new Date(post.createdAt).toLocaleDateString()
// "2020/12/12"
```

結果如下：

![](https://i.imgur.com/AromKKu.png)

---

## 結語

到這邊我們已經完成專案基本架構，設定路由，以及顯示全部文章、顯示單篇文章的功能，下一篇要繼續學習如何在 React 實作登入機制。

- 傳送門：[[week 21] React 實戰篇：用 SPA 架構實作一個部落格（二）](https://hackmd.io/@Heidi-Liu/note-fe302-react-blog-login)