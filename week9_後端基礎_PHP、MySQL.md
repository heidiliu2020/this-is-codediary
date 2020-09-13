###### tags: `Back-End` `php` `資料庫`
# [week 9] 後端基礎 - PHP 語法、資料庫 MySQL

> 本篇為 [[BE101] 用 PHP 與 MySQL 學習後端基礎](https://lidemy.com/p/be101-php-mysql) 這門課程的學習筆記。如有錯誤歡迎指正。

## 「後端」是什麼？

後端實際上包含以下三大項（括弧內為舉例使用的程式）：

1. 網頁伺服器（Apache）：伺服器程式。專門處理 Request 與 Response，是後端最重要的部分。
2. 寫程式（PHP）：接收網頁伺服器的 Request 並執行後端語言，處理成 output，通常是 html。
3. 資料庫系統（MySQL）：資料庫程式。專門用來操作資料庫，能夠更有效率儲存、修改或查詢資料。

## 後端環境建置

以下透過 XAMPP 來進行實作。XAMPP 結合了 Apache + [MariaDB](https://zh.wikipedia.org/zh-tw/MariaDB) + PHP，能夠快速建置後端環境。

> [MySQL](https://zh.wikipedia.org/wiki/MySQL) 為目前市佔率最高的資料庫系統。但 XAMPP 上使用的是 MariaDB 而非 MySQL。這是因為當初 MySQL 被甲骨文公司收購後，開發者擔心會有後患，像是轉為商用等等，於是社群就 clone 出 MariaDB ，因此這兩個系統幾乎完全相同。

### XAMPP

1. 到 [XAMPP 官網](https://www.apachefriends.org/zh_tw/index.html) 下載程式並安裝
2. 開啟 XAMPP 並運行 Apache 和 MySQL

![](https://i.imgur.com/UwHMX0L.png)

3. 點選 Adimn 開啟網頁：`http://localhost/dashboard/`，出現以下畫面就代表安裝成功！

![XAMPP](https://i.imgur.com/5V5s0ra.png)

4. 以 XAMPP 程式而言，PHP 檔案都會放在 htdocs 資料夾內，且`網址路徑 = 檔案路徑`

![htdocs](https://i.imgur.com/0DMgMWL.png)

5. 以下為範例在 hotdocs 另建一個資料夾 heidi，裡面新建 test.php，並在編輯器輸入下列程式碼：

```php
<?php
  echo "I am Heidi, yo!"
?>
```

6. 以瀏覽器運行該網址：`http://localhost/heidi/test.php`

![test.php](https://i.imgur.com/dt6vRqk.png)

---

## 靜態網頁 vs 動態網頁

網頁中的「靜態」與「動態」，並非代表視覺上的移動，兩者差異在於是否以「伺服器程式」去產生網頁上的資料。

### 靜態網頁

- server 收到 request 後，找到相對應的檔案（`.css` 是靜態檔案）後，把檔案直接回傳 response
- 檔案內容是什麼就回傳什麼

![](https://i.imgur.com/p1FRoiG.png)

### 動態網頁
- server 收到 request 後，會將 `.php` 檔案給 php 處理成 output，再傳給 server 後回傳 response
- 可根據用戶的點擊、輸入等操作，迅速產生響應與動態變化，例如部落格、留言板等

![](https://i.imgur.com/Y5BDM4e.png)

## PHP 是什麼？

PHP 全名是 Hypertext Preprocessor（超文本預處理器）。和 ASP、JSP 等都是動態網頁開發語言。

PHP 語言是伺服器端（Server）執行的網頁，因此 PHP 必須在伺服器端執行完後，再將結果回傳給用戶端（Clint）的瀏覽器檢視並渲染。

### Apache、PHP 與資料庫運作原理

![](https://i.imgur.com/XHHg6LO.png)

```
request → apache → php → output → apache → response
　　　　　 (server)       (html)
```

執行步驟大致如下：

1. server 接收 request
2. server 轉給 php 處理
3. php 處理成 html
4. 將 output 傳給 server
5. server 回傳 response

由此可知，後端最重要的是「伺服器程式」。若沒有伺服器程式，網站會直接掛掉；但沒有後端語言，就只是變回靜態網站，檔案內容是什麼就回傳什麼。

參考資料：[PHP新手指南：3分鐘快速認識PHP](https://www.happycoding.today/posts/23)


## PHP 基礎語法

- php 語法以 `<? php` 開頭，`?>` 結尾包起來，沒有包含在其中的程式碼，就是單純的 html，會直接被顯示出來
- 每句結尾須加上分號 `;`

```php
<?php

  echo "Hello World";    // 印出 Hello World
  
?>
```

- 變數均以 `$` 符號開頭
- 不需要宣告變數，直接賦值即可使用
- 字串連接用 `.`，數字相加用 `+`

```php
<?php

  $x = 10000;
  $y = 5.5;
  $str = 'hello';
  
  echo $x + $y . '<br>';  // 印出 10005.5
  echo $x . $str;         //      10000hello
  
?>
```

### 迴圈 

寫法和其他程式語言差不多，惟須注意變數前要加 `$`：

```php
<?php
  for ($i = 1; $i <= 5; $i++) {
    echo $i . '<br>';
  }
?>
```

結果如下：

![迴圈](https://i.imgur.com/RgZfI7h.png)

### 陣列 array

- `sizeof()`：返回單元中的元素數量，和 `count()` 功能相同。

```php
<?php
// 建立陣列
$arr = array(1, 3, 5, 7, 9);

// 印出陣列長度：5
echo $length = sizeof($arr) . '<br>';

// 印出最後一個元素：9
echo $arr[sizeof($arr) - 1];
?>
```

- 輸出完整陣列：
  - `var_dump()`：輸出 index => 型態和值：type(value)
  - `print_r()`：較簡潔，輸出 index => 值：[value]

```php
<?php
$arr = array(1, 'a', 10, 'b', 100);
echo var_dump($arr) . '<br>';
// 印出 array(5) { [0]=> int(1) [1]=> string(1) "a" [2]=> int(10) [3]=> string(1) "b" [4]=> int(100) }

print_r($arr);
// 印出 Array ( [0] => 1 [1] => a [2] => 10 [3] => b [4] => 100 )
?>
```

### 其他函式

- `data("Y-m-d H:i:s")`：輸出現在時間

---

## 資料庫系統 Database System

- 資料庫系統（Database System）：專門管理資料庫的程式
- 資料庫（Database）：儲存資料的地方，可視為多個資料表的集合

之所以要有資料庫系統，利用現成的資料庫介面，我們能夠快速進行新增、修改、查看、刪除資料等操作，方便管理檔案與維護。

### 關聯式資料庫（RDBMS）

- 全名是 Relational Database Management System
- 以 SQL 語言（專門用來管理與查詢關聯式資料庫）操作
- 用不同 table 存取不同類型的內容，但資料之間保有相關性，避免不相關的資料互相干擾
- 常見程式：MySQL、PostgreSQL、Microsoft SQL Server
- 例如：會員資料庫，其中明確關聯是會員 ID

### 非關聯式資料庫（NoSQL）

- 全名是 Not Only SQL
- 以 NoSQL 語言操作
- 彈性較高，沒有形態上的限制，可儲存較複雜的資料
- 常見程式：mongoDB
- 例如：存取 log 日誌、社群上的大量資訊（按讚數）

### 如何管理資料庫

我們可分別利用 CLI 或 GUI 介面來進行管理：

- CLI：mysql connect，在終端機下指令連線
- GUI：phpMyAdmin、Adminer、Sequel Pro 等，利用網頁介面連線

### phpMyAdmin

phpMyAdmin 是一套基於 PHP 寫的 GUI 資料庫管理軟體，其本質就是一個 PHP 檔案，讓管理者能夠以 GUI 介面來管理 MySQL 資料庫。

可從 `http://localhost:8080` 點選 phpmyadmin 進入：

![](https://i.imgur.com/1guMNR9.png)

管理介面如下所示：

![](https://i.imgur.com/hNpYDDJ.png)

## Table schema 結構簡介

Table schema（資料庫結構），當我們在開資料庫以前，要先進行資料型態、有無預設值、是否為唯一等設定，而之後的資料均須符合當初設定的結構，否則會無法成功新增姿笑。

### 資料型態

- id：`int`
  - 勾選 ai（auto increment）為保證遞增，但 id 不一定為連續
  - 設定 primary index：代表是唯一值
- VARCHAR：適合存取較短的資料，可設定長度。通常用於：預期資料有特定長度的情況。
- TEXT：適合存取較長的資料，不可設定長度。通常用於：留言板、部落格文章。
  - 通常建議使用 VARCHAR，查詢速度較快，且能夠有效減少資料庫文件大小。
- 日期：datetime、預設值改成 current timestamp

參考資料：[mysql中char，varchar與text類型的區別和選用](https://read01.com/OyxeGy.html)

### 索引 index

可作為書本的目錄，建立某欄位的索引，目的是加快搜尋速度。

#### Index：索引鍵

- 可多欄位設定 Index

#### Primary Key（PK）：主鍵

- 每個 table 只能有一個 Primary Key
- 不能為空值、不能重複，是 table 裡面最主要的欄位
- 當設置某欄位為 Primary Key 時，該欄位會自動加上 unique index

#### Unique Index：不重複鍵

- 和 Index 相同，差異在於不能允許重複值

參考資料：[[SQL 基本觀念] primary Key / Index / Unique 差別](https://blog.niclin.tw/2018/06/09/sql-%E5%9F%BA%E6%9C%AC%E8%A7%80%E5%BF%B5-primary-key-/-index-/-unique-%E5%B7%AE%E5%88%A5/)

---

## MySQL 基礎語法 

### 查詢資料 `SELECT`

- 使用 `*` 查詢全部資料：`SELECT * FROM 資料夾名稱`

```php=
SELECT * FROM users
// 從資料夾 users 裡面找到所有列，並取出所有欄位的值。
```

- 使用 `where` 條件設定：`SELECT * FROM 資料夾名稱 WHERE 條件`

```php=
SELECT * FROM users WHERE username=heidi
// 從資料夾 users 裡面，找到 username 是 heidi 的那列，並取出該欄位的所有值。
```

### 新增資料 `INSERT`

- 使用 `INSERT INTO` 指令可新增資料：

```ph
INSERT INTO "表格名" ("欄位1", "欄位2", ...) VALUES ("值1", "值2", ...);
```

```php=
INSERT INTO users(username, content) values (Peter, 1234)
// 新增一筆記錄，username 是 Peter；content 是 1234。
```

### 修改資料 `UPDATE`

```ph
UPDATE "表格名" SET "欄位1"=[新值] WHERE "條件";
// 若沒有加上 WHERE，就會更新表中的所有行
```

```php=
UPDATE users SET username = "user02", content = "content02" WHERE id =2
// 修改 id 為 2 那列，username 為 user02；content 為 content02
```

### 刪除資料 `DELETE`

```php=
DELETE FROM users WHERE id = 2
// 若沒有加上 WHERE，系統會選取所有行，即刪除所有資料。
```

## 從前端傳資料給後端

前端通常利用 `form` 來傳送資料給後端：

- form 要設定 method 和 action 屬性
  - method：方法，`GET` 或 `POST`
  - action：接收資料的程式

1. 先在 index.php 檔案建立表單

```htmlmixed=
<form method="GET" action="test.php">
  name: <input type="text" name="name"/>
  age: <input type="text" name="age"/>
  <input type="submit"/>
</form>
```

在瀏覽器開啟表單如下：

![](https://i.imgur.com/Dl9UlBX.png)

2. 在接收資料的 test.php 檔案撰寫 php 語法

```php=
<?php
  // 用 empty 檢查表單是否為空
  if (empty($_GET['name']) || empty($_GET['age'])){
      echo '資料有缺，請再次填寫<br>';
      exit();   // 終止程序
  };
  // 接收 method 為 GET 的 From input
  echo "Hello!" . $_GET['name'] . " <br>";
  echo "Your age is" . $_GET['age'] . " <br>";

  print_r($_GET);
?>
```

在前端以 GET 方法提交表單，資料會以 Query String 的形式將加在網址後方：

![](https://i.imgur.com/nEr23zE.png)

## 用 PHP 操作 MySQL

### 從 PHP 連線到 MySQL 資料庫

#### conn.php
- 存放帳密等重要資料，需加入 git.ignore 不進行版本控制

```php=
<?php
  $server_name = 'localhost';
  $username = 'heidi';
  $password = 'h232425123';
  $db_name = 'heidi';

  // mysqli 的四個參數分別為：伺服器名稱、帳號、密碼、資料庫名稱
  $conn = new mysqli($server_name, $username, $password, $db_name);

  if (!empty($conn->connect_error)) {
    die('資料庫連線錯誤:' . $conn->connect_error);    // die()：終止程序
  }
?>
```

#### 讀取資料

```php=
<?php
  // 連線到資料庫
  require_once('conn.php');

  // 用 empty 檢查表單是否為空
  if (empty($_GET['name']) || empty($_GET['age'])){
      echo '資料有缺，請再次填寫<br>';
      exit();   // 終止程序
  };
  // 接收 method 為 GET 的 From input
  echo "Hello! " . $_GET['name'] . " <br>";
  echo "Your age is " . $_GET['age'] . " <br>";

  print_r($_GET);
?>
```

#### 新增資料

```php=
<?php
  // 連線到資料庫
  require_once('conn.php');

  // $_POST['key-name'] 取得輸入的資料
  // 以 empty() 判斷值是否為 null
  if (empty($_POST['username'])) {
    // 中斷後面程序並顯示內容，() 內可輸入訊息字串
    die('請輸入 username'); 
  }
 
  $username = $_POST['username']; 
         // sprintf() 裡面可以放入替代字元
  $sql = sprintf(  
     // 插入新欄位，%s 代表字串，值是第二個參數
    "INSERT INTO users(username) VALUES('%s')",
    $username
  );
  // 執行結果存在 $result 這個變數中
  $result = $conn->query($sql);
  // 確認是否有拿到結果
  if (!$result) {
    die($conn->error);
  }

  header("Location: index.php"); // 自動跳轉回 index.php
?>
```

#### 刪除資料

```php=
<?php
  // 連線到資料庫
  require_once('conn.php');

  if (empty($_GET['id'])) {
    die('請輸入 id');
  }

  $id = $_GET['id'];
  $sql = sprintf(
    "delete from users where id = %d",
    $id
  );
  echo $sql . '<br>';
  $result = $conn->query($sql);
  if (!$result) {
    die($conn->error);
  }

  if ($conn->affected_rows >= 1) { // 判斷影響 1 列以上資料
    echo '刪除成功';
  } else {
    echo '查無資料';
  }

  // header("Location: index.php");
?>
```

#### 編輯資料

```php=
<?php
  require_once('conn.php');

  if (empty($_POST['id']) || empty($_POST['username'])) {
    die('請輸入 id 與 username');
  }

  $id = $_POST['id'];
  $username = $_POST['username'];
  $sql = sprintf(
    // 帶入兩個參數：%s（字串）、%d
    "update users set username='%s' where id=%d",
    $username,
    $id
  );
  echo $sql . '<br>';
  $result = $conn->query($sql);
  if (!$result) {
    die($conn->error);
  }

  header("Location: index.php");
?>
```

---

參考資料：
1. [[第九週]後端基礎— 基本運作原理(搭配 XAMPP) - Mia](https://medium.com/@miahsuwork/%E7%AC%AC%E4%B9%9D%E9%80%B1-%E5%BE%8C%E7%AB%AF%E5%9F%BA%E7%A4%8E-%E5%9F%BA%E6%9C%AC%E9%81%8B%E4%BD%9C%E5%8E%9F%E7%90%86-%E6%90%AD%E9%85%8D-xampp-1593f9817377)
2. [[第九週]後端基礎 — PHP 基礎語法- Mia](https://medium.com/@miahsuwork/%E7%AC%AC%E4%B9%9D%E9%80%B1-%E5%BE%8C%E7%AB%AF%E5%9F%BA%E7%A4%8E-php-%E5%9F%BA%E7%A4%8E%E8%AA%9E%E6%B3%95-a632878bf8ea)
3. [後端基礎 - PHP、SQL、Database - RZ-Huang](https://hackmd.io/bS-2evlATceVc-di-RMxVA)
4. [後端基礎 PHP 與 MySQL - Ruofan Wei](https://hackmd.io/HSgibRj2R22eo7bk-qbJsw?view- )
