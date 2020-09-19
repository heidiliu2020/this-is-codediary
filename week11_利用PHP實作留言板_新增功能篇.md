###### tags: `Back-End` `php` `資料庫`
# [week 11] 利用 PHP 實作留言板 - 新增功能篇

> 本篇為 [[BE101] 用 PHP 與 MySQL 學習後端基礎](https://lidemy.com/p/be101-php-mysql) 這門課程的學習筆記。如有錯誤歡迎指正。

相關筆記：
- [[week 9] 後端基礎 - PHP 語法、資料庫 MySQL](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week9_%E5%BE%8C%E7%AB%AF%E5%9F%BA%E7%A4%8E_PHP%E3%80%81MySQL.md)
- [[week 9] 利用 PHP 實作留言板 - 初階實作篇](https://github.com/heidiliu2020/This-is-Codediary/blob/week11/week9_%E5%88%A9%E7%94%A8PHP%E5%AF%A6%E4%BD%9C%E7%95%99%E8%A8%80%E6%9D%BF-%E5%88%9D%E9%9A%8E%E5%AF%A6%E4%BD%9C%E7%AF%87.md)
- [[week 11] 資訊安全 - 雜湊與加密 & 常見攻擊：SQL Injection、XSS](https://github.com/heidiliu2020/This-is-Codediary/blob/week11/week11_%E8%B3%87%E8%A8%8A%E5%AE%89%E5%85%A8_%E9%9B%9C%E6%B9%8A%E8%88%87%E5%8A%A0%E5%AF%86%E3%80%81%E5%B8%B8%E8%A6%8B%E6%94%BB%E6%93%8A.md)

---

## 規劃產品路由與功能

在開始實作新功能前，同樣先進行前置作業：

### 新增功能

- 編輯暱稱功能 `handle_update_user.php`
- 編輯留言功能 `handle_update_comment.php`
- 刪除留言功能 `handle_delete_comment.php`
- 分頁功能 `offset` & `limit`

### 新增頁面

- 編輯留言 `update_comment.php`

---

## 編輯暱稱

作法有兩種：

1. 在首頁建立「編輯暱稱」按鈕，點擊可連至新頁面，可進行編輯功能
2. 若不想多寫一個頁面，也可直接在首頁放表單

在登入狀態下，才能看到下列程式碼：

```php=
<?php if ($username) { ?>
  <div class="board__user-block">
    <h4>你好！<?php echo escape($username); ?></h4>
    <form class="board__new-comment-form" method="POST" action="handle_update_user.php">
      <div class="board__nickname">
        <span>編輯暱稱：</span>
        <input type="text" name="nickname">
        <input class="board__submit-btn" type="submit">
      </div>
    </form>
  </div>
<?php } ?>
```

---

## 編輯留言

在實作編輯留言之前，我們可發現當初建立的 comments 資料庫欄位是有問題的，因為一旦 user 修改 nickname，就會影響其他 table。

### 資料庫正規化

因此我們必須進行「資料庫正規化」，也就是以 username 來代替 nickname，如此即可和 users 資料庫產生關聯性。

修改後欄位如下：

- id
- username（產生關聯）
- content
- creat_at

### SQL JOIN 語法

那麼該如何利用 username 欄位，把 comments 和 users 資料庫關聯起來呢？就是使用 SQL JOIN 語法。

SQL JOIN（連接）是利用不同資料表之間，各欄位的「關連性」來結合多資料表之檢索。

透過結合多個資料表，組成一抽象的暫時性資料表以供資料查詢，原各資料表中之紀錄及結構，皆不會因此連接查詢而改變。

語法可參考 [Visual join](https://joins.spathon.com/)：

![](https://i.imgur.com/i67eLkk.png)

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- OUTER JOIN

其中最常用到的是 LEFT JOIN 語法：
- LEFT JOIN 左側資料表 （table_name1）的所有記錄都會加入到查詢結果中
- 即使右側資料表（table_name2）中的連接欄位沒有符合的值也一樣

```sql=
SELECT table_column1, table_column2...
FROM table_name1
LEFT JOIN table_name2 
ON table_name1.column_name=table_name2.column_name;
```

以先前資料庫的 username 欄位為例，實作留言與會員的 JOIN：

```sql
SELECT * 
FROM comments                  -- 將左側資料表 comments 的
LEFT JOIN users                -- 所有記錄加入查詢結果
ON comments.username = users.username  -- 與右側資料表 users 進行關聯
ORDER BY comments.id DESC      -- 並以倒敘排列
```

而在實際操作中，為了避免資料被覆蓋，我們可在 SQL 語法中幫 table 補上別名，並直接指定要取出的資料：

- `comments AS C`
- `users AS U`

```sql
SELECT
  C.id as id, C.content AS content, 
  C.created_at AS created_at, U.nickname AS nickname, U.username AS username 
FROM comments AS C
LEFT JOIN users AS U ON C.username = U.username 
ORDER BY C.id DESC
```

參考資料：
- [SQL JOIN 合併資料表- SQL 語法教學Tutorial](https://www.fooish.com/sql/join.html)

---

## 刪除留言

### hard delete 硬刪除

使用 SQL 刪除語法，將留言從資料庫中刪除。

```sql
$sql = "DELETE FROM comments WHERE id=?";
```

### soft delete 軟刪除

為了避免誤刪重要資料，我們可利用 soft delete（軟刪除），也可稱為「邏輯刪除」或「標記刪除」。

- 和硬刪除不同之處：並不會真正從資料庫中刪除該筆記錄
- 作法：設定一個欄位，例如 `is_deleted` 來標記刪除狀態，預設值為 `NULL`
- 優點：保證資料的完整性，避免誤刪重要資料，隨時能夠進行復原
- 缺點：儲存的資料會不斷佔據空間。因此若確定某些資料不再需要時，即可使用硬刪除。例如：驗證碼

在 comments 資料庫新增 is_deleted 欄位，再把上面的 SQL 指令改為下列語法：

```sql
-- handle_delete_comment.php
$sql = "UPDATE comments SET is_deleted=1 WHERE id=?";
```

再回到 `index.php` 修改 SQL 語法，也就是加上 `"WHERE C.is_deleted IS NULL ".`，只抓取 `is_deleted` 為 `NULL` 的資料：

```php=
$stmt = $conn->prepare(
  "SELECT ".
    "C.id as id, C.content AS content, ".
    "C.created_at AS created_at, U.nickname AS nickname, U.username AS username ".
  "FROM comments AS C ". 
  "LEFT JOIN users AS U ON C.username = U.username ".
  "WHERE C.is_deleted IS NULL ".
  "ORDER BY C.id DESC"
```

參考資料：
1. [軟刪除和刪除](https://www.itread01.com/content/1546740906.html)

---

## 分頁功能 offset & limit

### LIMIT 語法

- 限制傳回的資料筆數
- 通常會伴隨著 `ORDER BY` 使用
- LIMIT: `$item_per_page`

```sql
-- 用 ORDER BY 以 id 欄位做排序，回傳最新的 5 筆資料
SELECT * FROM comments ORDER BY id DESC LIMIT 5
```

### OFFSET 語法

- 跳過的資料筆數
- OFFSET: `(page-1)*item_per_page`

```sql
-- 跳過 5 筆資料，回傳接下來的 5 筆資料
SELECT * FROM comments ORDER BY id DESC LIMIT 5 OFFSET 5
```

### 應用在實作留言版

```php=
$page = 1;
// 確認網址是否有帶到 $page 參數
if (!empty($_GET['page'])) {
  // intval() 將字串轉成數字
  $page = intval($_GET['page']);
}
$items_per_page = 5;                      // LIMIT 限制回傳資料筆數
$offset = ($page -1) * $items_per_page;   // OFFSET 跳過的資料筆數

$stmt = $conn->prepare(
  // 在 table 補上別名，避免資料覆蓋，並直接指定要取出的資料
  "SELECT ".
    "C.id as id, C.content AS content, ".
    "C.created_at AS created_at, U.nickname AS nickname, U.username AS username ".
  "FROM comments AS C ". 
  "LEFT JOIN users AS U ON C.username = U.username ".
  "WHERE C.is_deleted IS NULL ".
  "ORDER BY C.id DESC ".
  "LIMIT ? OFFSET ? "
);
$stmt->bind_param("ii", $items_per_page, $offset);
$result = $stmt->execute();
if (!$result) {
  die('Error:' . $conn->error);
}
$result = $stmt->get_result();
```

把抓到的資料，在前端頁面顯示之程式碼如下：

```htmlmixed=
<div class="page-info">
  <span>總共 <?php echo $count ?> 筆 - 頁數：</span>
  <span><?php echo $page ?> / <?php echo $total_page ?></span>
</div>
<div class="paginator">
  <?php if ($page != 1) { ?>
    <a href="index.php?page=1">首頁</a>
    <a href="index.php?page=<?php echo $page - 1 ?>">上一頁</a>
  <?php } ?>
  <?php if ($page != $total_page) { ?>
    <a href="index.php?page=<?php echo $page +1 ?>">下一頁</a>
    <a href="index.php?page=<?php echo $total_page ?>">最末頁</a>
  <?php } ?>
</div>
```

### 修正問題：權限管理

到這邊留言版的功能就完成得差不多了，但其實還有個重大安全問題。就是任何人都能藉由更改「網址列」，來編輯或刪除其他人的留言。

解決方法，就是透過「設立權限管理」，來驗證使用者是否和該留言為同一人：

```php=
$id = $_GET['id'];
$username = $_SESSION['username'];

// 判斷留言 id 和從 SESSION 抓取的 username 是否相同
$sql = "UPDATE comments SET is_deleted=1 WHERE id=? AND username=?";
$stmt = $conn->prepare($sql);
$stmt->bind_param('is', $id, $username);
$result = $stmt->execute();
if (!$result) {
  die($conn->error);
}
```

