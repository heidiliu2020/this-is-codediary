# [week14] 後端基礎：資料庫

> 本篇為「程式導師實驗計畫：Lesson 8-2 之資料庫」這門課程的學習筆記。如有錯誤歡迎指正。

## 資料庫相關名詞

1. NoSQL
2. ACID
3. Transaction
4. Lock

---

## NoSQL（Not Only SQL）

非關聯式資料庫，例如 [mongodb](https://www.mongodb.com/cloud/atlas/lp/try2?utm_source=google&utm_campaign=gs_apac_taiwan_search_brand_atlas_desktop&utm_term=mongodb&utm_medium=cpc_paid_search&utm_ad=e&utm_ad_campaign_id=6498554090&gclid=Cj0KCQjwuL_8BRCXARIsAGiC51A2fnn8cy0JQPdfGGGCluss0PuBCG96x3-NzTfSE9jy0ibAJMTLy1IaAhApEALw_wcB)；與之對應的是關聯式資料庫，例如之前實作 PHP 留言板使用的 MySQL。

1. 沒有 Schema，可以想像成存 JSON 資料進 Database
2. 使用 key-value 儲存
3. 不支援 JOIN，因為是非關聯式資料庫
4. 通常用來存一些結構不固定的資料，例如：log

### NoSQL 跟 SQL 的差別在哪裡？

SQL（Structured Query Language），中文為「結構化查詢語言」，是一種專門用來管理與查詢「關聯式資料庫」的程式語言。與之相對的是 NoSQL（Not Only SQL），「非關聯式資料庫」，也就是不限定為關聯式資料庫的資料庫管理系統。

以下是關聯式與非關聯式資料庫的差別：

#### 關聯式資料庫（RDBMS）
- 全名：Relational Database Management System
- 以 SQL 語言操作
- 儲存方式：使用不同 table 存取不同類型的內容
- 特性：資料之間保有相關性，也避免不相關的資料互相干擾
- 常見程式：MySQL、PostgreSQL、Microsoft SQL Server
- 例如：會員資料庫，其中明確關聯是會員 ID

#### 非關聯式資料庫（NoSQL）
- 全名：Not Only SQL
- 不限定使用 SQL 語言
- 儲存方式：使用 key-value 儲存資料，且沒有 Schema，可想像成把 JSON 資料存進資料庫
- 特性：彈性較高，通常用來存一些結構不固定的資料，因此不支援 JOIN
- 常見程式：mongoDB
- 例如：存取 log 日誌、社交網站上的大量資訊（文章被分享、按讚數等）

參考資料：
- [了解NoSQL不可不知的5項觀念](https://www.ithome.com.tw/news/92506)
- [NoSQL (非關聯式) 資料庫如何運作？](https://aws.amazon.com/tw/nosql/)

---

## Transaction 交易

- 可解釋為一筆交易。
- 例如：A 轉帳 100 元給 B，要確保 A 少 20 元的同時 B 多了 20 元。

實際應用：
1. 轉帳
2. 購物（一次買多個品項）
3. 其他一次牽扯到多個 query 的操作

## ACID 原則

為了保證 Transaction 的正確性，執行資料庫事務須符合的四個特性：

在執行資料庫事務時，為了保證 Transaction（交易）的正確性，必須符合 ACID 四個特性：

1. 原子性 atomicity：全部失敗 or 全部成功
    - 例如：小明轉帳 100 元給小美，成功的話小明 -100 元，小美 +100 元；失敗的話資料不會有任何變化。
2. 一致性 consistency：維持資料的一致性
    - 例如：交易前後，錢的總數均不會改變。
3. 隔離性 isolation：多筆交易不會互相影響，也就是不能同時變動同一個值
    - 例如：小明只有 100 元，如果要同時轉帳 100 元給小美和小華，會產生負值這樣的系統錯誤。因此交易時使用需要 Lock 鎖，鎖定交易資料，即可避免同時修改到同一個值。
4. 持久性 durability：交易成功之後，寫入的資料不會不見

參考資料：
- [如何理解數據庫事務中的一致性的概念？](https://www.zhihu.com/question/31346392)

### 如何在 MySQL 執行 Transaction

一個 Transaction 可同時執行多個 SQL，以下列程式碼為例：

```php=
$conn->autocommit(FALSE);    // 避免每次自動變成 transaction
$conn->begin_transaction();  // 開始 transaction
$conn->query("update from money set amount = 20");
$conn->query("update from money set sum = 10");
$conn->commit();        // 當發出 commit 後才會執行
```

---

## Lock 資料鎖定

資料庫的交易資料鎖定。

當多筆交易在讀取或寫入資料時，彼此會相互影響。因此為了交易的並行性與獨立性，需透過做「記號」來標記該資料狀態正在被讀取或寫入，其他交易則根據該記號決定能否執行其他操作，而這個記號就是所謂的 lock（交易鎖定）。但由於鎖定後會需要等待執行，也須考慮到效能上的損耗。

程式碼範例如下：

```php=
$conn->autocommit(FALSE);
$conn->begin_transaction();
$conn->query("SELECT amount from products where id = 1 for update");
// 加上 for update: 更新後就會把這行鎖起來，沒有指定欄位的話會鎖住 Schema
$conn->commit();
```

### 什麼情況需要 Lock 鎖

舉例來說，當某個商品限定數量，造成搶購時可能會發生「超賣」情形，此現象又稱作 race condition（競爭危害）。若能在交易時加上 lock，即可避免同時讀取和寫入同一筆資料時產生的資料衝突，避免系統發生錯誤。

參考資料：
- [資料庫的交易鎖定 Locks](https://www.qa-knowhow.com/?p=383)

---

## DNS 域名系統

在課程[第四週的網路基礎概論](https://github.com/heidiliu2020/This-is-Codediary/blob/master/week4_%E7%B6%B2%E8%B7%AF%E5%9F%BA%E7%A4%8E%E6%A6%82%E8%AB%96.md)時我們也曾提過 DNS。

- 全名是 Domain Name System
- 負責將域名轉換成 IP 位置。可將 DNS 伺服器作為一個分散式資料庫
  - 域名（Domain）：即常用的網址。google.com 就是一個域名，類似景點名稱
  - IP 位置：每個主機都有個 IP 位置，是網路溝通的地址。由四個數字組成，範圍是 0 ~ 255
- 功能類似網路上的電話簿，我們在搜尋網站的同時，是由 DNS 提供將網站名稱（域名）轉成號碼（IP 位置）的目錄服務

### Google 有提供的公開的 DNS，對 Google 的好處以及對一般大眾的好處？

#### 對 Google 的好處
- 透過搜尋引擎來蒐集大數據，藉以分析使用者行為，以應用於商業目的
- 日後可提供更符合該使用地區的資料，或是投放更精準的廣告

#### 對一般大眾的好處

- 使用免費
- 提升網路安全性，例如：擋下惡意網站訊息
- 加速瀏覽體驗：可能增加上網速度
- 直接取得 DNS 查詢結果：因為已經快取，就不需查詢上層 DNS 記錄

---

## hw1：短網址系統設計

請你畫出一張短網址服務的後端系統架構圖，越詳細越好，可以考慮到如何增進效能、scaling 以及備份資料。

沒靈感的話可參考：短网址(short URL)系统的原理及其实现，或是用「短網址 系統設計」之類的關鍵字去搜尋。

![](https://i.imgur.com/ODL6mPz.png)

系統設計為了能夠穩定地提供高效率的服務，其實必須不斷思考，當使用者增加，需要擴展系統來增進效能時，可能遇到哪些問題、解決方式還有其優缺點。

通常會由下列兩個觀點作切入：

- 擴展性（Scalability）：每當投入更多的資源，例如多增加一台伺服器，系統的效能（performance）也能成比例地增加。
- 可用性（Availability）：系統任何時候都要能回應使用者的請求，簡單來說，就是希望系統掛掉的時間越少越好。

### 名詞解釋

#### Load Balancer（負載平衡器）

提供類似路由器的功能，幫忙自動分配新進來的請求要導到哪一台 Server。

#### 主從模式（Master-slave）

資料的變動一律透過 Master 完成，它會再將結果同步到各個 slave。這種設計特別適合讀取的頻率大於寫入的系統。例如購物網站。可大幅減少資料庫的負擔。

#### Sticky sessions（會話保持）

解決 Load Balancer 遺失狀態的問題，將用戶端與服務器之間建立的多個連接，都發送到相同的服務器進行處理。

#### Single Point of Failure（單點故障）

系統中一旦失效，就會讓整個系統無法運作的部件，例如負載均衡器就是脆弱的單點，解決方法如引入冗餘來故障轉移。

#### 資料庫快取

資料庫的傳輸速度，是影響整個系統效能最大的因素，因此存取資料庫其實是一個高成本的行為。為了減少對資料庫的存取，我們可利用「快取（Cache）」，也就是將曾經查詢過的結果保存起來。通常會再加上有效期限，過期後快取結果就消失。

藉由快取機制，可大幅降低資料庫的負擔，使應用程式伺服器處理請求的速度更快，進而提升整個系統的效能。

參考資料：
- [系統設計101—大型系統的演進（上）](https://medium.com/%E5%BE%8C%E7%AB%AF%E6%96%B0%E6%89%8B%E6%9D%91/backend-architecture-101-5c425e760a13)

