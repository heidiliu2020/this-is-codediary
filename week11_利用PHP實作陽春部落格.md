# [week 11] 利用 PHP 實作陽春部落格

可以將部落格想成「只有一人」能新增文章的留言版。

## Step1. 規劃產品路由與功能

### 頁面

- 部落格首頁 `index.php`
- 註冊頁面 `register.php`
- 登入頁面 `login.php`
- 導覽列 `navbar.php`：獨立出 navbar，可避免重複撰寫
- 管理後台頁面 `admin.php`
- 新增文章頁面 `create_post.php`
- 新增文章頁面 `update_post.php`
- 瀏覽全文頁面 `post.php`

### 功能

- 常用函式 `utils.php`：`getUserFromUsername()`、`escape()`

#### 使用者相關

- 註冊功能 `handle_register.php`
- 登入功能 `handle_login.php`
- 登出功能 `logout.php`
- 驗證權限功能 `check_permission.php`

#### 文章相關

- 新增文章功能 `handle_create_post.php`
- 編輯文章功能 `handle_update_post.php`
- 刪除文章功能 `handle_delete_post.php`

## Step2. 規劃資料結構：建置資料庫

- blog_users：使用者相關資料
  - id
  - role
  - username
  - nickname
  - password
  - created_at

- blog_posts：文章相關資料
  - id
  - username
  - title
  - content
  - is_deleted
  - created_at 

---

## Step3. 實作部落格

### PHP 相關語法

- `var_dump()`：印出變數的相關訊息
- `strpos()`: 查找該字串首次出現的位置
```php=
<?php
  // 獲得回傳的網址列
  $url = $_SERVER['REQUEST_URI'];
  // strpos(): 查找該字串首次出現的位置
  $isAdminPage = (strpos($url, 'admin.php') !== false);
  $isLoginPage = (strpos($url, 'login.php') !== false);
?>
```

- `substr()`：取得部分字串，可設定字串長度，共有三個參數：
  - `$strting`：原始的字串
  - `$start`：開始擷取的位置
  - `$length`：要擷取的字串長度
- `mb_substr()`：和 `substr()` 用法相同，但多了第四個參數：
  - `$encoding`：判斷的編碼，通常使用 `"UTF-8"`
```php=
substr($string, $start, $length);
mb_substr($string, $start, $length, $encoding);
```
- `intval()`: 將字串轉換為數字
```php=
$id = intval($_GET['id']);
```
- `$_SERVER['HTTP_REFERER']`：獲取前一頁面的 URL 地址
```php=
<!-- 透過 POST 來獲得前一頁面的 URL -->
<form action="handle_update_post.php" method="POST">
  <input type="hidden" name="page" value="<?php echo $_SERVER['HTTP_REFERER'] ?>">
</form>
```

### `include`、`require`、`include_once`、`require_once` 的差別

`include` 和 `require` 達成的效果幾乎相同，均為「引入」外部檔案，差別在於「執行原理」以及「報錯程度」。

#### `include`
- 執行到 `include()` 時，每次皆會讀取檔案
- 常用於流程控制，例如：條件判斷、迴圈
- 適合引入動態的程式碼，會在用到時加載
- 執行時，若引入的檔案發生錯誤，會顯示警告（Warning），但不會立刻停止程式

#### `require`
- 執行到 `require()` 時，只會讀取一次檔案
- 常放在程式開頭，檔案引入後 PHP 會將網頁重新編譯
- 適合引入靜態的程式碼，會在一開始就加載
- 執行時，若引入的檔案發生錯誤，會直接報錯（Fatal error）並終止程式

後面若加上 `_once`，代表「只會引入一次」。PHP 會檢測檔案是否有重複引入，避免重複執行浪費效能，因此較推薦使用。

#### `include_once` 
- 和 `include` 用法基本相同
- 若發生錯誤，`include_once` 不會停止程式的執行，只會丟出警告訊息
- 避免重複引入
- 以引入 navbar 為例：

```htmlmixed=
<!-- 把 header 獨立出來，就不需每個檔案都重複撰寫 -->
<?php include_once('navbar.php')?>
```

#### `require_once`
- 和 `require` 用法基本相同
- 若發生錯誤，`require_once` 會立刻中止程式
- 避免重複引入

參考資料：
1. [PHP：require V.S. include « 程式初學者](http://code-beginner.logdown.com/posts/389687-phprequire-vs-include)
2. [[PHP]include 與require 的差別 - Syun](http://syunguo.blogspot.com/2013/04/phpinclude-require.html)
