# [week 14] 網站部署 - 設定 AWS EC2 遠端主機 +  Ubuntu LAMP 環境 + phpMyAdmin

在練習這週的網站部署時，主要參考下列幾篇筆記：

- [[ 紀錄 ] 部屬 AWS EC2 雲端主機 + LAMP Server + phpMyAdmin](https://mtr04-note.coderbridge.io/2020/09/15/-%E7%B4%80%E9%8C%84-%08-%E9%83%A8%E5%B1%AC-aws-ec2-%E9%9B%B2%E7%AB%AF%E4%B8%BB%E6%A9%9F-/)
- [AWS EC2 佈署網站：卡關記錄 & 心得](https://nicolakacha.coderbridge.io/2020/09/16/launch-website/)
- [部署 AWS EC2 遠端主機 + Ubuntu LAMP 環境 + phpmyadmin](https://github.com/Lidemy/mentor-program-2nd-yuchun33/issues/15)

---

## 部署流程

網站部署大致可分為下列幾個步驟：

1. 設定 AWS EC2 主機
    - 註冊 AWS 會員
    - 架設遠端主機 EC2 
    - 選擇 Ubuntu Server 並完成設定
    - 儲存私鑰後運行主機
    - 以 CLI 輸入指令連線主機
2. 更新 Ubuntu Server
3. 設定 LAMP Server
    - 安裝 Tasksel
    - 以 Tasksel 套件來安裝 lamp-server
4. 設定 phpMyAdmin
    - 安裝 phpmyadmin
    - 設定使用者密碼
    - 修改 MySQL 登入設定
    - 設定 MySQL 的 root password
5. 設定域名
6. 部署寫好的程式到 Server
    - 利用 git clone 上傳檔案
    - 設定 FileZilla 上傳檔案

---

## 設定 AWS EC2 主機

![](https://i.imgur.com/BY8vzW5.png)

1. 註冊 AWS 會員
2. 進入 AWS 管理控制台
3. 啟動虛擬機器 - 使用 EC2

![](https://i.imgur.com/PPo4YVf.png)

4. 選擇方案：找到「Ubuntu Server 18.04 LTS (HVM), SSD Volume Type」並點選 Select

> Ubuntu 是基於 Debian 的 Linux 發行版，是個免費開源的作業系統。Ubuntu Server 版是為執行伺服端的應用程式而設計的伺服器版本。

![](https://i.imgur.com/wFjYN6H.png)

5. 選擇有綠色標籤的免費方案，按右下角的 Next，一直到 Step6

![](https://i.imgur.com/p7ylYvJ.png)

6. 點選「Add Rule」新增欄位，共需新增兩個，分別是「HTTP 跟 HTTPS」，後面的資料會自動帶入（例如 port 80 和 port 443）

![](https://i.imgur.com/2rEWA1g.png)

7. 接著點選「Review and Launch」，進入下一步
8. 確認設定都沒問題後，點選 Launch
9. 再來是選擇私鑰，第一格選擇「Create a new key pair」，第二格輸入私鑰名稱，填好後點選右下角儲存私鑰檔案

![](https://i.imgur.com/P7bVck5.png)

10. 下載後點選「Launch Instances」啟動設置
11. 確認 Server 有在運行，並記下 IPv4 Public IP（公有 IPv4 地址）

![](https://i.imgur.com/3vlSClN.png)

12. 接著點選上方的「連線」，會進入以下頁面

![](https://i.imgur.com/nPl6WqE.png)

13. 依照說明，在 CLI 輸入下列指令來連線

```=
$ chmod 400 <私鑰檔案路徑>

$ ssh -i "<私鑰檔案路徑>" ubuntu@ec2-< IPv4 位置>.us-east-2.compute.amazonaws.com
```

---

## 更新 Ubuntu Server

安裝好 Ubuntu Server 後，需要把更新系統版本到最新，避免遇到安全性漏洞以及程式的 BUG。

CLI 指令如下：

```=
$ sudo apt update && sudo apt upgrade && sudo apt dist-upgrade
```

---

## 設定 LAMP Server

### 什麼是 LAMP？

LAMP 是 Linux 系統上的開源架站組合，縮寫分別代表：

- L：Linux 作業系統
- A：Apache 網頁伺服器
- M：MySQL 資料庫
- P：PHP 程式語言

### LAMP 設定步驟

1. 安裝 Tasksel 套件：使用 Tasksel 能快速配置 LAMP 環境

```=
$ sudo apt install tasksel
```

> 跳出訊息「安裝會佔用 XX 空間，是否繼續安裝？(Y/n)」，輸入 Y。

2. 使用 Tasksel 下載 lamp-server

```=
$ sudo tasksel install lamp-server
```

會進入紫色的下載畫面：

![](https://i.imgur.com/S5t6yOa.png)

3. 接著確認伺服器是否有成功運行，在瀏覽器輸入剛剛記下伺服器的 IPv4 IP 位置，若看到下方預設網頁就代表安裝成功！

![](https://i.imgur.com/ednoBoi.png)

---

## 設定 phpmyadmin

phpMyAdmin 是以 PHP 為基礎的資料庫管理工具，讓使用者能夠以 Web 介面管理 MySQL 資料庫。

### phpmyadmin 設定步驟

1. 安裝 phpmyadmin

```=
$ sudo apt install phpmyadmin
```

> 跳出訊息「安裝會佔用 XX 空間，是否繼續安裝？(Y/n)」，輸入 Y。

2. 進入紫色畫面，選擇連接的網頁伺服器。用空白鍵選 apache2，再按 Enter 確定

![](https://i.imgur.com/arKzEgG.png)

3. 是否設定 dbconfig-common？選 `<Yes>`

> 此處將會建立一個帳號來管理 phpMyAdmin 運行時用到的資料表，使用者名稱預設為 phpmyadmin，接著要設定這個使用者密碼。

![](https://i.imgur.com/bXr9daY.png)

4. 設定使用者密碼，輸入完成後按向下鍵選 `<OK>`

![](https://i.imgur.com/LwCsRoT.png)

5. 再確認一次密碼，輸入完選 `<OK>`，即完成設置

### 修改 MySQL 登入設定

設置完 phpMyAdmin 之後，接著要設定 MySQL 的 root 使用者，建立超級使用者（Superuser）以便在 phpMyAdmin 中進行管理。

也就是說，使用者必須登入後，才能夠實際對資料庫進行增刪改查等動作，並且也可透過 phpMyAdmin 圖形化介面直接操作資料庫。

由於 MySQL 預設使用 auth_socket 的驗證 plugin，使用者可以透過 sudo 直接登入 root 帳號。但這樣就無法透過 phpMyAdmin 登入圖形化介面，因為 phpMyAdmin 預設需要密碼登入。

![](https://i.imgur.com/b7jTMwy.png)

我們可按照下列步驟來設定 root 密碼：

1. 修改 MySQL 登入設定，改成使用密碼登入

```=
$ sudo mysql -u root mysql
```

2. 輸入 mysql 指令，啟用設定密碼

- 讓 root 帳號啟用設定密碼的插件

```sql=
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
```

- 輸入指令重新載入特權表

```sql=
mysql> FLUSH PRIVILEGES;
```

- 退出 mysql 指令

```sql=
mysql> exit
```

![](https://i.imgur.com/TDNKk10.png)

3. 設定 MySQL 的 root password

```=
$ sudo mysql_secure_installation
```

4. 是否設定密碼？輸入 `$ y`，開啟 VALIDATE PASSWORD 插件
5. 選擇密碼強度，除非需要非常注重安全性，這裡可輸入 0

> 選 1 以上要中英文大小寫 + 符號混用 + 一定長度，複雜性較高。

![](https://i.imgur.com/8dkOixk.png)

6. 設置密碼，需注意這裡輸入密碼不會顯示，確定設置這個密碼嗎？輸入 `y`

![](https://i.imgur.com/7DXZilL.png)

7. 以下詢問皆輸入 `y`

- Remove anonymous users?
- Disallow root login remotely?
- Remove test database and access to it?
- Reload privilege tables now?

8. 最後出現 All Done! 代表設置完成

![](https://i.imgur.com/QpHqCtN.png)

9. 接著在瀏覽器輸入 `<IPv4 地址>/phpmyadmin`，會看到 phpmyadmin 的登入畫面，輸入帳密確認是否能夠登入
- 帳號：root
- 密碼：`<步驟 6 設定的 root 密碼>`

### 修正 MySQL 錯誤

順利以 root 帳密登入 phpmyadmin 後，會發現有個問題，當我們想查看任一個資料庫的資料表，或是想要執行匯入匯出功能時，會跳出下列錯誤訊息：

```sql=
Warning in ./libraries/plugin_interface.lib.php#551
 count(): Parameter must be an array or an object that implements Countable
```

![](https://i.imgur.com/wqqSslF.png)

原因是在 php 7.2 版本後，`count()` 在沒有添加參數的情形下，會噴出 Warning 警告訊息。這部分需要我們手動解決。其中有兩個部分需利用 vim 編輯器修改：

> vim 指令：編輯完成後，按 esc 再輸入 `:wq` 即可儲存後退出 vim 編輯器。

1. 修改 Warning in ./libraries/plugin_interface.lib.php#551：

```=
$ sudo vim /usr/share/phpmyadmin/libraries/plugin_interface.lib.php
```

將第 551 行的 `count($options) > 0` 去掉，修改後如下：

> 小技巧：可使用 PgDn 按鍵快速往下，不用一行一行往下滑XD

```php=
if ($options != null ) {
    // …
}
```

2. 修改 Warning in ./libraries/sql.lib.php#613：

```=
$ sudo vim /usr/share/phpmyadmin/libraries/sql.lib.php
```

將第 613 行修改成換成下列判斷式：

```php=
|| ((count($analyzed_sql_results['select_expr']) == 1)
```

完成以上步驟後，就可以正常操作 phpmyadmin 了！

參考資料：
- [Day 03 : 環境架設 part II -- MySQL & phpMyAdmin](https://ithelp.ithome.com.tw/articles/10216815)
- [如何安裝Ubuntu Server 18.04來架設伺服器？](https://magiclen.org/ubuntu-server-18-04/)
- [安裝 LAMP Server + phpMyAdmin 在 Linux 系統上輕鬆架設網站](https://magiclen.org/lamp/)
- [使用Ubuntu Server架設MySQL伺服器，並於PHP程式中存取MySQL資料庫](https://magiclen.org/ubuntu-server-mysql-php/)

---

## 設定域名

![](https://i.imgur.com/hBbMxyF.png)

1. 先在 [gandi.net](https://www.gandi.net/zh-Hant) 註冊帳號
2. 到電子信箱收驗證信
3. 進入域名頁面，輸入想要的網域名稱後，再按購買

![](https://i.imgur.com/jBsFzWE.png)

4. 會跳轉到註冊網域名稱的頁面，下面會有搜尋結果能夠選擇加入購物車

![](https://i.imgur.com/5rjovGd.png)

5. 填寫購買資訊，同意相關合約

![](https://i.imgur.com/YDjIGND.png)

6. 再結帳前可在步驟 2 最下方填入優惠代碼
7. 購買完成！要記得收取確認信

![](https://i.imgur.com/YJtThnL.png)

8. 到域名頁面，點選剛剛註冊好的域名

![](https://i.imgur.com/T0SX6D6.png)

9. 點選區域檔記錄，將 A 的值修改成 AWS 主機的 IPv4 地址

![](https://i.imgur.com/NgRKfZN.png)

10. 在瀏覽器輸入 `<你的域名>`，如果有出現和先前一樣的 Apache2 Ubuntu 預設頁面，就代表部署成功！

---

## 部署寫好的程式到 Server

1. 首先確定有連線到 Server，也就是前面提到的這段指令：

```=
$ chmod 400 <私鑰檔案路徑>

$ ssh -i "<私鑰檔案路徑>" ubuntu@ec2-< IPv4 位置>.us-east-2.compute.amazonaws.com
```

2. 輸入 `cd ..` 兩次，回到根目錄底下
3. 輸入 `ls` 找到 var 資料夾

![](https://i.imgur.com/mErapfq.png)

4. 輸入 `cd var/www/html`，會進到預設的 apache 首頁所在目錄，也就是我們剛才連到的域名頁面
5. 這時若想在該目錄新增檔案，會發現權限不夠

![](https://i.imgur.com/XQh7p9v.png)

6. 因此我們要透過 `chmod` 指令來修改檔案權限：

> [chmod](https://zh.wikipedia.org/wiki/Chmod)（change mode）：是在 Unix 系統中用於控制用戶對檔案的權限的命令

```=
$ sudo chown ubuntu /var/www/html
```

7. 修改完成後即可在 `var/www/html` 目錄新增檔案！

### 修改 `conn.php`

如果是部署動態網站，需要連線到遠端資料庫，那就需要修改 `conn.php` 連線資料庫需要的資料，也就是修改成剛剛設定的 root 帳密：

```
$server_name = 'localhost';
$username = 'root';
$password = '<root 密碼>';
$db_name = '<資料庫名稱>';
```

### 利用 git clone 上傳專案

以上傳 GitHub repository 為例：

1. 在 CLI 輸入 `git clone <GitHub 專案網址>`
2. 到瀏覽器輸入 `域名/對應路徑/檔案名稱`，確認是否有成功把檔案放上去

### FileZilla 設定

除了使用 CLI 操作，我們還可以透過設定 FileZilla，以 key-pairs 登入 AWS EC2 來存取檔案，如此一來即可直接在圖形化界面進行操作。

設定步驟如下：

1. 在 FileZilla 新增站台
- 選擇 SFTP 協定
- 主機輸入 AWS 的 IP 位置
- 登入型式選擇「金鑰檔案」，也就是設定主機時的那個 `.pem` 檔案

![](https://i.imgur.com/CSlkeAp.png)

2. 設定完成後點選連線，會進入 `/home/ubuntu` 目錄
3. 回到根目錄，進到 `var/www/html`，會看到 apache 的預設首頁 index.html 檔

![](https://i.imgur.com/MqTnOwv.png)

4. 接著就可以放上寫好的程式
5. 在瀏覽器輸入 `<網域名稱/路徑>`，確認是否有部署成功

---

## 部署心得

終於來到一直很期待的網站部署，想起之前隨意聊就有提過，完成十四週能夠架一個自己的網站，擁有屬於自己的網域，可以帥氣的和朋友分享自己做出的網站了！和助教索取折扣碼的當下，竟然莫名有點感動QQ

發現自己終於走到課程的一半，雖然後面還有一段路要走，但也在過程中體會到，自己很喜歡在寫程式這件事中獲得的成就感。好像有點扯遠了，回到這禮拜的部署心得XD

主要是參考幾位同學的筆記，還有一些網路上的資料，跟著步驟一步步進行部署前的設定。其實筆記中步驟都已經整理得很清楚，自己只是跟隨前人的腳步去操作，也因此少繞了很多遠路，很佩服從零開始摸索的學長姐們，感謝萬分。

但過程中也有遇到一些不懂的部分，還是想要去理解每個步驟的意義是什麼，就花了點時間去查找資料，例如：

- 為什麼選擇用 Ubuntu Server？

Ubuntu Server 是為執行伺服端的應用程式而設計的伺服器版本。簡言之，就是專門用來架設伺服器。

- 為什麼要安裝 Tasksel 套件？

能夠使用 Tasksel 安裝 LAMP，快速配置 LAMP 環境。

- 什麼是 LAMP Server？

是 Linux 系統上的開源架站組合，縮寫代表：Linux + Apache + Mysql + PHP。

- 還有在 phpmyadmin 設定 dbconfig-common 時，會建立一個帳號來管理 phpMyAdmin 運行時用到的資料表，使用者名稱預設為 phpmyadmin。
- 這和之後設定 MySQL 的 root（超級使用者）其實對象不同，一開始有困惑說為什麼要設定兩次密碼，root 這組帳號可用來登入 phpMyAdmin，能夠在圖形化界面管理資料庫。

### 待解決問題

上傳 Markdown 檔案時，發現中文部分會有亂碼問題，有查到幾個解決方法，但覺得好像在這花有點多時間，決定之後再參來考方法三來解決：

1. 安裝 apache-mod-markdown 套件

- [Serving markdown from Apache 2.4.7 on Ubuntu](https://stackoverflow.com/questions/32597316/serving-markdown-from-apache-2-4-7-on-ubuntu)：安裝完套件後在第二步卡住，不是很明白要怎麼建立模組？

2. 把 markdown 檔案轉成 html 檔

- [markdown在ubuntu上的使用](https://www.itread01.com/content/1546783937.html)

3. 安裝 hexo 這個框架，能夠支援 Markdown 語法，感覺這比較符合需求

- [Ubuntu 16.04下从零起步搭建配置github.io博客——Hexo](https://lrscy.github.io/2017/11/10/Ubuntu-Github-io-config-Hexo/)
- [Hexo+GitHub，新手也可以快速建立部落格](https://blackmaple.me/hexo-tutorial/)
