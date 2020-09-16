###### tags: `Back-End` `php` `資訊安全`
# [week 11] 資訊安全 - 雜湊與加密 & 常見攻擊：SQL Injection、XSS

> 本篇為 [[BE101] 用 PHP 與 MySQL 學習後端基礎](https://lidemy.com/p/be101-php-mysql) 這門課程的學習筆記。如有錯誤歡迎指正。

課程筆記：[[week 9] 後端基礎 - PHP 語法、資料庫 MySQL](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week9_%E5%BE%8C%E7%AB%AF%E5%9F%BA%E7%A4%8E_PHP%E3%80%81MySQL.md)

## 資訊安全

我們先前[利用 PHP 實作的初階留言板](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week9_%E5%88%A9%E7%94%A8PHP%E5%AF%A6%E4%BD%9C%E7%95%99%E8%A8%80%E6%9D%BF-%E5%88%9D%E9%9A%8E%E5%AF%A6%E4%BD%9C%E7%AF%87.md)，其實暗藏許多安全性漏洞，接下來的重點，就是說明這些漏洞如何產生，以及該如何解決這些問題。

## 用雜湊保護密碼

當我們以「明文」方式將密碼存在資料庫時，可能面臨下列資安風險：

- 網站管理者隨時能夠查看使用者帳密
- 如果有駭客成功入侵資料庫，即可竊取所有使用者資料

因此我們需要透過「雜湊」來保護密碼。這也是為什麼，當我們忘記密碼時，網站會要我們重設密碼，而不是得到原先的密碼，因為雜湊不可逆，無法回推得到原碼。

## 加密？雜湊？

兩者最大差別在於：「加密可逆，雜湊不可逆」。

![](https://i.imgur.com/ymLoNYw.png)

### 加密（Encrypt）
- 一對一，可逆
- 加密與解密必須要有金鑰（key）才能進行
- 對稱式加密
  - 常見演算法：AES、DES、3DES
  - 加密解密都使用同一個 key，速度快，通常用於加密大量資料
  - 資料傳輸前，雙方必須先講好密碼，不易日後管理，因此有了非對稱式加密
- 非對稱式加密
  - 常見演算法：RSA、DSA、ECC
  - 加密和解密使用不同的金鑰，速度慢，通常用於少量數據加密
  - 一對金鑰由公鑰（可交給任何請求方）和私鑰（由一方保管）組成，可互相解密加密
  - 可使用數位簽章（Digital Signature）確認是否為本人傳遞訊息，形成雙重保障

### 雜湊（Hash）

- 多對一，不可逆，因此可有效保護密碼
- 將`不定長度、無窮可能`的輸入，透過雜湊演算法，轉換成`固定長度`的雜湊值
- 雖然機率極低，但不同輸入可能產生相同的雜湊值，這種情形稱為碰撞（collision）
- 對伺服器來說，安全性越高的雜湊函數也代表速度越慢
- 用途：
  - 檔案校驗碼（Checksum）：檢查檔案是否正確
  - 不需要被還原的資料：避免明文儲存密碼
- 常見雜湊演算法
  - MD5：已被證實不夠安全
  - SHA-1：已被證實不夠安全
  - SHA-256
  - RIPEMD-160
- 常見攻擊方法
  - 暴力破解（brute-force）：嘗試所有數列組合，非常耗時
  - 字典法（Dictionary Attacke）：嘗試常見的密碼組合，為主流方法
  - 彩虹表（rainbow table）：類似暴力破解，使用預先計算的雜湊演算法，以查表方式破解
- 解決方式：將密碼加鹽（salt）形成新的雜湊值，即可提高安全性
  - 例如：1234-(加鹽)→1234XXX

### PHP 內建雜湊函式：`password_hash()`

[PHP 提供內建函式 ](https://www.php.net/manual/en/function.password-hash.php)`password_hash()` 能夠實現加密， 指令如下：

```php
 <?php
$hash = password_hash("要加密的字串", PASSWORD_DEFAULT);
?>
```

驗證指令：`password_verify("要驗證的字串", "加密過的字串")`，會回傳一個 boolean

```php=
// 使用範例
<?php 
$password = '1234';    // 原始密碼
$hash_password = password_hash($password, PASSWORD_DEFAULT);

if (password_verify($password , $hash_password)){
  echo "密碼正確";  // true
} else {
  echo "密碼錯誤";  // false
}
?> 
```

參考資料：
1. [[資訊安全] 密碼存明碼，怎麼不直接去裸奔算了？淺談 Hash , 用雜湊保護密碼](https://tinyurl.com/y65pgo4w)
2. [一次搞懂密碼學中的三兄弟 — Encode、Encrypt 跟 Hash](https://medium.com/starbugs/what-are-encoding-encrypt-and-hashing-4b03d40e7b0c)
3. [加密和雜湊有什麼不一樣？ | Just for noting](https://blog.m157q.tw/posts/2017/12/25/differences-between-encryption-and-hashing/)
4. [現代PHP password_hash 雜湊加密採用隨機SALT 使用方式](https://adon988.logdown.com/posts/2513623-modern-php-password-hash-hash-encryption-using-random-salt-uses)
5. [PHP password_hash() 函数| 菜鸟教程](https://www.runoob.com/php/php-password_hash.html)

---
## 常見資安攻擊

以下介紹幾種常見的資安攻擊，其原理與防範方法。

## SQL injection

是一種注入式攻擊。發生於應用程式與資料庫層的安全漏洞。

### 攻擊原理

在輸入資料時，夾帶不正當的 SQL 指令，若網頁忽略字元檢查，資料庫將會視為正常的 SQL 指令並執行惡意程式碼，進而破壞或入侵。

#### 以輸入帳號密碼進行驗證為例：

- 正常的 SQL 語法
  - 帳號：123
  - 密碼：456
```php=
SELECT * FROM users WHERE username='123' AND password='456';
```

- SQL injection
  - 帳號：`' or 1=1#`
  - 密碼：可輸入任意值（甚至不需輸入）
```php=
SELECT * FROM users WHERE username='' or 1=1# AND password='';
```
- `'`：將 username 的 input 關閉
- `or`：條件式的「或是」
- `#`：代表註解，後面的條件均會被忽略
- 由於 `WHERE` 條件式中的 `1=1` 恆正，如此即可略過權限檢查，沒有密碼也能成功登入帳號

#### 以之前實作的初階留言版為例：

1. 登入後在新增留言輸入：`'), ('admin', 'Hacking!')#`
2. SQL 指令就變成：
```php=
INSERT INTO comments(nickname, content)
VALUES('aaa', ''), ('admin', 'Hacking!')#
```
3. 執行後會新增兩筆留言，nickname 分別是 aaa 和 admin

![](https://i.imgur.com/zLfxN7g.png)

### 防範方法：Prepare Statement 預處理

透過 Prepare Statement 進行跳脫，以防止 SQL 注入。

步驟如下：

1. 將 SQL 語法儲存在變數 `$sql`
2. 使用 `prepare()` 預處理 `$sql`：此時只傳送佔位符號 `?`，可避免利用拼接 SQL 字串語句的攻擊
4. 使用 `bind_param()` 帶入參數。常用參數類型：
   - `s`：string（字串）
   - `i`：integer（整數）
5. 呼叫 `execute()` 執行 SQL 語法：此時才會將參數傳送給資料庫
6. 若執行成功，則使用 `get_result()` 取出執行結果

程式碼範例：

```php=
<?php
  // SQL 語法
  $sql = "INSERT INTO comments(nickname, content) VALUES(?, ?)";
  // prepare() 預處理 
  $stmt = $conn->prepare($sql);
  // bind_param() 帶入參數
  $stmt->bind_param('ss', $nickname, $content);
  // execute() 執行 SQL 語法
  $result = $stmt->execute();
  // 確認是否執行成功
  if (!$result) {
    die($conn->error);
  // 取出執行結果
  $result = $stmt->get_result();
}
```

參考資料：
1. [[Postx1] 攻擊行為－SQL 資料隱碼攻擊 SQL injection](https://ithelp.ithome.com.tw/articles/10189201)
2. [Php中用PDO查詢Mysql來避免SQL隱碼攻擊風險的方法](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/240808/)
3. [PreparedStatement 如何防止SQL注入](https://www.itread01.com/p/1403324.html)

---

## XSS 攻擊

全名是 Cross Site Scripting（跨網站指令碼攻擊），為了不與 CSS 混淆，故簡稱 XSS。

是一種注入式攻擊。通常是透過 JavaScript 或 HTML，因此又被稱作 JavaScript Injection 攻擊。

### 攻擊原理

攻擊者利用網頁開發時留下的安全漏洞，在網頁插入惡意程式碼，讓使用者載入並執行惡意程式，攻擊者可藉此取得更高的權限，或是竊取使用者的 Cookie 等資料。

XSS 常見的攻擊種類大致上可分為兩種：
- Reflected XSS（反射型）

![](https://i.imgur.com/zNQGEPN.png)

以網頁輸入欄位為例。若在輸入欄位內刻意植入 Javascript 語法，例如下列程式碼，即可竊取使用者的 cookie 資料：

```=
<script>alert(document.cookie)；</script>
```

- Stored XSS（儲存型）

![](https://i.imgur.com/A5qvwBe.png)

以留言板為例。攻擊者將惡意程式碼透過「新增留言」寫入資料庫，當使用者瀏覽到該頁面時，就會觸發程式碼，達到攻擊目的。

### 防範方法：過濾特殊字元

責任主要還是歸咎於網頁開發時留下的安全漏洞，但 Server 端與 Client 端可以藉由下列方法，來避免遭受攻擊：

#### Client 端
1. 定期更新瀏覽器至最新版本
2. 避免點擊來路不明的網址

#### Server 端
1. 過濾特殊字元：過濾所有 Client 端提供的內容，並轉譯成純文字。例如：使用 PHP 內建函式 `htmlentities（）`或 `htmlspecialchars（）`

```php=
<?php
// utils.php
  function escape($str) {
    return htmlspecialchars($str, ENT_QUOTES, 'UTF-8');
  }
?>
```

2. 限制輸入內容長度或型態。雖然可藉由引入外部 JavaScript 來繞過，但還是能提高 XSS 攻擊難度

參考資料：
1. [Code Injection Attack – Cross-Site Scripting Attack – 三甲科技](https://cms.aaasec.com.tw/index.php/2018/09/21/code-injection-attack-cross-site-scripting-attack/)
2. [前端安全系列（一）：如何防止XSS攻擊？](https://www.itread01.com/content/1544575149.html)
3. [[Day24] 攻擊行為－反射式跨網站指令碼 Reflected XSS](https://ithelp.ithome.com.tw/articles/10188646)

---

## CSRF

全名是  Cross Site Request Forgery（跨站請求偽造）。也被稱為 one-click attack 或 session riding。

和 XSS 同樣是跨站式的請求攻擊，兩者卻有明顯區別，不同之處在於：

- XSS：透過在網頁輸入惡意程式碼的方式來進行攻擊
  - 使用者對目標網站的信任
- CSRF：攻擊者利用網站使用者的身分發送請求，去執行一些未經授權的操作
  - 目標網站對該使用者的信任

### 攻擊原理

要完成一個 CRSF 攻擊，必須條件是「使用者仍保持登入狀態」。

假設 A 為目標網站，B 為惡意網站，步驟大致如下：

1. 使用者登入網站 A
2. 網站 A 返回 session id 等資訊（使用 cookie 儲存）
3. 使用者訪問網站 B，攻擊者利用隱藏圖片或表單，讓使用者在不知情的情況發送 request 到網站 A
5. 由於瀏覽器的機制，發送 request 給某個網域時，會附帶關聯的 cookie
6. 網站 A 誤以為是合法請求，攻擊者即可假冒使用者身分進行操作

簡言之，CSRF 就是「在不同 domain 下，偽造使用者本人發出的 request」。

### 防範方法

#### Client 端

由於 CSRF 攻擊的必要條件，是使用者在被攻擊的網頁處於「已登入狀態」。使用者在每次使用完網站就登出，即可避免 CSRF 攻擊。

#### Server 端

1. 加上圖形驗證碼（或簡訊驗證碼等）

在網站加上驗證碼，可多一道檢查程序，常用於金流相關操作。

- 缺點：每次都要進行驗證，可能造成使用者體驗不佳

2. 加上 CSRF token

- 產生：Server
- 儲存：Server

在 `form` 裡面加上一個 `hidden` 的欄位，叫做 `csrftoken`，填入的值為 Server 隨機產生的亂碼，並且存在 Server 的 session 中。

按下 submit 後，Server 會比對表單中的 csrftoken 與存在 Server 端的是否相同。

```php=
// 範例程式碼
<form action="https://myblog.com/delete" method="POST">
  <input type="hidden" name="id" value="3"/>
  <input type="hidden" name="csrftoken" value="<亂碼>"/>
  <input type="submit" value="刪除文章"/>
</form>
```

- 缺點：若攻擊者先發出 request，還是能取得 csrftoken 來攻擊目標網站

3. Double Submit Cookie

- 產生：Server
- 儲存：Client

與前一種解法相似，好處是不需要存任何東西在 Server 端。

而是在 cookie 與 form 設置相同的 csrftoken，利用「cookie 只會從相同 domain 帶上來」機制，使攻擊者無法從不同 domain 戴上此 cookie。

```php=
// 範例程式碼
Set-Cookie: csrftoken=<亂碼>

<form action="https://myblog.com/delete" method="POST">
  <input type="hidden" name="id" value="3"/>
  <input type="hidden" name="csrftoken" value="<亂碼>"/>
  <input type="submit" value="刪除文章"/>
</form>
```

- 缺點：攻擊者若掌握使用者底下任何一個 subdomain，就能夠幫使用者寫 cookie，藉此進行攻擊

4. Client 端生成的 Double Submit Cookie

- 產生：Client
- 儲存：Client

和前面提的 Double Submit Cookie 核心概念相同。不同之處在於「改由 Client 端」生成 csrf token。

生成之後放到 form 裡面以及寫到 cookie，其他流程就和之前相同。
此 cookie 只是確保攻擊者無法取得，不含任何資訊，因此由 Client 或 Server 生成都是一樣的。

- 例如：library axios 就有提供此功能。只要設定好 header 與 cookie 名稱，設定好之後的每一個 request，即可自動在 header 統一加上 cookie 值

```php=
// 範例程式碼
// `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
xsrfCookieName: 'XSRF-TOKEN', // default

// `xsrfHeaderName` is the name of the http header that carries the xsrf token value
xsrfHeaderName: 'X-XSRF-TOKEN', // default
```

#### 瀏覽器端的防禦：SameSite cookie

CSRF 之所以能夠成立，是瀏覽器的機制所導致，但我們也能從瀏覽器著手進行防禦，也就是透過 SameSite cookie。其原理是「幫 Cookie 再加上一層驗證」，防止來自不同 domain 的請求。

做法是在 Set-cookie 的 session_id 後加上 SameSite 即可啟用此功能。由於這是較新的功能，目前瀏覽器中只有 Chrome 支援，使用前須注意。

使用範例如下：

```php=
Set-Cookie: session_id=<亂碼>; SameSite
```

SameSite 有分 Strict（預設）與 Lax 兩種模式：

- Strict 模式（嚴格）
  - 使用 `<a href="">`, `<form>`, `new XMLHttpRequest` 等標籤
  - 只要瀏覽器驗證不是同一個 domain 發出的 request，就不會帶上此 cookie
  - 由於連結不會帶上 cookie，對於使用者體驗並不佳
- Lax 模式（寬鬆）
  - 上述標籤都還是會帶上 cookie
  - 除了 Get 刑事，其他方法如 POST、DELETE、PUT 等都不會帶上 cookie
  - 意即 Lax 模式無法擋掉 GET 形式的 CSRF

參考資料：
1. [讓我們來談談CSRF - TechBridge 技術共筆部落格](https://blog.techbridge.cc/2017/02/25/csrf-introduction/)
2. [[技術分享] Cross-site Request Forgery (Part 1)](https://cyrilwang.pixnet.net/blog/post/31813568-%5B%E6%8A%80%E8%A1%93%E5%88%86%E4%BA%AB%5D-cross-site-request-forgery-(part-1))
3. [[第十二週] 資訊安全 - 常見攻擊：CSRF](https://yakimhsu.com/project/project_w12_Info_Security-CSRF.html)
4. [程式猿必讀-防範CSRF跨站請求偽造](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/109775/)
