###### tags: `Back-End` `php` `資料庫`
# [week 9] 利用 PHP 實作留言板 - 初階實作篇

> 本篇為 [[BE101] 用 PHP 與 MySQL 學習後端基礎](https://lidemy.com/p/be101-php-mysql) 這門課程的學習筆記。如有錯誤歡迎指正。

課程筆記：[[week 9] 後端基礎 - PHP 語法、資料庫 MySQL](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week9_%E5%BE%8C%E7%AB%AF%E5%9F%BA%E7%A4%8E_PHP%E3%80%81MySQL.md)

## 前置作業

在開始實作留言板之前，需先進行前置作業：

- 規劃產品路由與功能
- 規劃資料結構：建立資料庫

### Step1. 規劃產品路由與功能

![](https://i.imgur.com/HKuwCYA.png)

#### 頁面

- 留言板首頁 `index.php`
- 註冊頁面 `register.php`
- 登入頁面 `login.php`

#### 功能

- 新增留言 `handle_add_post.php` → comments 資料庫
- 註冊 `handle_register.php` → users 資料庫
- 登入 `handle_login.php`
- 登出 `logout.php`

### Step2. 規劃資料結構：建置資料庫

#### 建立 comments 資料庫

- id
- nickname
- content
- creat_at

![](https://i.imgur.com/4WWjEaA.png)

#### 建立 users 資料庫

- id
- nickname
- username
- password
- creat_at

![](https://i.imgur.com/w8cOsfs.png)

---

## 實作留言板

可分為前後端。通常會先切出前端頁面，再加入功能，從資料庫取出並資料串聯到頁面。

### Step3. 實作前端頁面

依照設計稿切出所需頁面，可參考[留言板 DEMO](http://mentor-program.co/mtr04group2/Heidi/week9/index.php)。

### Step4. 實作目標功能

接著就進入重頭戲，也就是替靜態網頁加上各種功能。

### PHP 常用函式

- 用 `->` 符號：取用物件中的變數
  - 例如：`$conn->error`
- `echo`：印出變數、字串等
  - 例如: `echo "hello World"`
- `print_r()`;：印出物件
  - 例如：`print_r($row);`
- `require_once();`：取用資料夾中其他 library

```php=
require_once("conn.php");      // 連線到資料庫
require_once("utils.php");     // 導入常用函式
```

- 讀取資料
  - `fetch_array()`：讀取資料同時，以數字與欄位名稱各存一次在陣列中
  - `fetch_assoc()`：讀取的資料 Key 值設定為欄位名稱的陣列
  - `fetch_row()`：讀取的資料 Key 值設定為依序的數字
- `isset()`：檢查是否有此變數
- `empty()`：檢查是否有值

```php=
$username = NULL;
if(!empty($_SESSION['username'])) {
  $username = $_SESSION['username'];
}
```

- `query()`：判斷資料庫查詢是否成功
  - 順利執行回傳 true
  - 查詢的帳密有誤、查詢的指定資料庫、資料表欄位有誤等，均回傳 false
- `exit()` 和 `die()`：輸出消息後退出程式

```php=
// 以 id 進行 DESC（遞減）排序："後新增的留言"會排在前面
$result = $conn->query("SELECT * FROM comments ORDER BY id DESC");
// 檢查是否查詢成功
if (!$result) {
  die('Error:' . $conn->error);
}
```

- `sprintf()`：裡面可放入替代字元

用 `sprintf()` 做 SELECT：

```php=
// handle_login.php
  $sql = sprintf(
    "SELECT * FROM users WHERE username='%s' AND password='%s'",
    $username,
    $password
  );

// 把執行結果存在 $result 這個變數中
$result = $conn->query($sql);
// 確認是否有拿到結果
if (!$result) {
  die($conn->error);
}
```

用 `sprintf()` 做 INSERT INTO：

```php=
// handle_add_comment.php
$sql = sprintf(
  "INSERT INTO comments(nickname, content) VALUES('%s', '%s')",
  $nickname,
  $content
  );

// handle_register.php
$sql = sprintf(
  "INSERT INTO users(nickname, username, password) VALUES('%s', '%s', '%s')",
  $nickname,
  $username,
  $password
);
```

### `conn.php` 連線資料庫

> 【注意】由於 `conn.php` 放有帳號密碼等重要資料，因此在 commit 前需加入 `git.ignore`，不進行版本控制。

程式碼如下：

```php=
<?php
  $server_name = 'localhost';
  $username = 'heidi';
  $password = '1234';
  $db_name = 'heidiDB';

  // `mysqli` 的四個參數分別為：伺服器名稱、帳號、密碼、資料庫名稱
  $conn = new mysqli($server_name, $username, $password, $db_name);

  // 確認是否出現連線錯誤
  if (!empty($conn->connect_error)) {
    die('資料庫連線錯誤:' . $conn->connect_error);
  }

  // 設定編碼，避免出現亂碼
  $conn->query('SET NAMES UTF8');
  // 設定成臺灣時區
  $conn->query('SET time_zone = "+8:00"');
?>

```

### `index.php` 顯示所有留言

```php=
<?php
  session_start();
  // 連線到資料庫
  require_once("conn.php");
  require_once("utils.php");

  $username = NULL;
  if(!empty($_SESSION['username'])) {
    $username = $_SESSION['username'];
  }

  // 以 id 進行 desc（遞減）排序，也就是"後新增的留言"會排在前面
  $result = $conn->query("SELECT * FROM comments ORDER BY id DESC");
  // 檢查是否有資料
  if (!$result) {
    die('Error:' . $conn->error);
  }
?>
```

---

## 儲存狀態的方式：Cookie & Session

### 使用 Cookie 記住 HTTP 狀態

首先要了解 Cookie 是什麼：

- 是一種小型純文字檔案，
- 網站伺服器會將其儲存在 client 端，以記錄使用者的相關資訊。
- 例如：會員登入狀態、瀏覽紀錄、購物車等。

由於 HTTP 是一個無狀態協議，會把每一次收到的請求都視為獨立的行為。但伺服器能透過 response header 的 `Set-Cookie` 屬性，將使用者狀態記錄在 Cookie。

瀏覽器會在每次發送請求時，自動在 request header 帶上 Cookie 資料；伺服器即可藉由檢視 Cookie 內容，得知瀏覽器使用者的狀態。

![](https://i.imgur.com/WsBvdz5.png)

但這麼做有個缺點，儲存在 client 端的 Cookie 是能夠被竄改的，因此不適合放機密或重要的資訊。這時有兩種解法：

#### 1. 將 Cookie 內容加密

也就是 Cookie-based session，把狀態加密後存在 Cookie。但如果加密方式以及密鑰被破解，往後仍有安全疑慮。

#### 2. 透過 Session ID 辨識身分

全名是 Session Identifier。如此 Server 只需在 Cookie 儲存一組亂數產生的 Session ID，其餘狀態資訊則存在 Server 端。

因此，Session 其實就是一種讓 Request 變成 stateful 的機制。

![](https://i.imgur.com/Nbk8TCj.png)

### 如何使用 Session 

- 儲存 Session

```php=
// 使用 Session 時，均需在開頭加上 session_start()
session_start();

$username = htmlspecialchars($_POST['username']);

// 把資料存在 Session 對應的 key 裡面
$_SEESION['username'] = $username;
```

- 取出 Session

```php=
session_start();

// 若 Session 內有存過 username，則 $username 為剛才存的 $_SESSION['username'] 
if(isset($_SESSION['username'])) {
  $username = $_SESSION['username'];
}
```

- 清除 Session

```php=
session_start();

// 直接清除所有 session
session_destroy();
```

參考資料：
1. [27. [WEB] Cookie & Session 是什麼？](https://ithelp.ithome.com.tw/articles/10227602)
2. [白話 Session 與 Cookie：從經營雜貨店開始](https://medium.com/@hulitw/session-and-cookie-15e47ed838bc)

