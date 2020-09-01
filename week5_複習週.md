# [week 5]複習週

## 前四週心得與解題心得

### Command line 的使用

第一次在終端機輸入指令，像是害怕把電腦弄壞似的，總是擔心會輸錯指令；也還記得第一次卡在 vim 介面出不來，最後使用重開大法解決（？）事後也證明其實不需顧慮這麼多，就算手殘打錯字，也只會回傳錯誤訊息而已XD 

#### 複習筆記：
- [
coderbridge - [Week 1] Command Line 入門 & 基本指令](http://jtliu.coderbridge.io/2020/06/14/command-line-codediary/)


### Git 的基本操作

在剛學習程式語言時，就看過幾篇文章說 Git 版本控制非常重要！而且剛好現在工作的地方是使用 SVN，雖然平常只有接觸到下載或更新檔案，但實際接觸到也更能夠了解到彼此之間的差異。

到現在邁入第六週，許多指令也已經記在腦海裡，想想還真是不可思議！雖然每次交作業時還是會一邊看[步驟](https://hackmd.io/qimY3Kj4QVeX3vf4-9uBEA)來操作，相信多練習幾次就會更加熟記起來！
　
#### 複習筆記：
- [
coderbridge -  [week 1] 版本控制 - Git 概念 ＆ 基本指令](https://jtliu.coderbridge.io/2020/06/19/git-beginner-codediary/)
- [[week 1] 版本控制 - Git 進階指令 & GitHub](https://hackmd.io/D_APVkJWTZyZwS4TV0g97g)

### JavaScript 基本語法及程式思維

在迴圈卡住似乎是必經之路，把觀念建立起來之後就是不斷解題練習。有時候還是會發生程式碼雖然幾乎對了，卻因為把值放在大括號錯誤位置，導致無法拿到預期輸出。

再來是了解如何在 LIOJ 上解題，一開始在「如何把值傳入」這件事情陷入苦戰，經歷過無數個 WA；最後還是先看了參考影片，也了解到有時卡住可能是某些觀念還需要釐清，例如值的型態、邊界數字等，都是需要注意的地方。還有透過 eslint 檢查語法，讓自己習慣依照規則去撰寫程式碼，熟悉之後在 commit 時就比較不會跳出一整排錯誤需要修正了。

雖然還有些題目沒做完，會再找空閒時間當作複習慢慢完成。筆記也想再好好整理，並且更新到 coderbridg 上。複習週沒能完成這些事情，之後如果有空檔會再回來補上！

#### 尚須整理 & 補筆記（位移運算子、ES6）：
- [[week 2] 先別急著寫 leetcode - 虛擬碼、Debugger、解題技巧](https://hackmd.io/710JQgMJRRqq95_x71Z_Hw) 
- [[week 2] JavaScript 基礎 - 基本語法、值的型別、變數、迴圈、函數](https://hackmd.io/9Wy0bxsSRuyPZlzF8SNTxA)
- [[week 3] JavaScript 技能：ES6 + npm + Jest](https://hackmd.io/UgUKFFlUTqmUNEY1M0fh8g)

### 網路基本概念

是前四週挫折感最重的一週，但也難怪畢竟是接觸全新的東西，必須花費更多時間來熟悉。前面有關 HTTP 的概念在火球數也有提到一些，但到了實際串接 API 部分就突然懵懂了。

還記得在寫 week4 的第一項作業時完全沒有頭緒，最後還是先找了參考解答，了解整個流程應該要如何操作。了解之後其實就和課程提到的實戰練習很像，知道不同 Method 的使用時機。還有最一開始的傳紙條故事，其實就和送出請求、回傳資料的概念是一樣的，重新看過文章後才漸漸有所體會。

#### 尚須整理 & 補筆記（curl 指令）
- [[week 4] 網路基礎概論 - HTTP 協定、TCP/IP、API](https://hackmd.io/Wdw-PHZoToSxi-MwykV8aQ#%E6%B7%BA%E8%AB%87-API)

---

## 解題心得 - Lidemy HTTP Challenge

謝謝 Huli 製作這麼有趣的小遊戲！其實剛開始對 API 串接還是有點不太了解，透過小遊戲實際操作，漸漸能夠知道自己究竟傳入哪些資訊，要如何才能拿到想要的資訊，或是該如何進行身分驗證等等，也讓自己對有點挫敗的第四週重拾些許信心。目前玩到 Lv10，之後也會想把後面慢慢解完！

### start
- 了解如何使用 query string 傳入 token。
- https://lidemy-http-challenge.herokuapp.com/start
- https://lidemy-http-challenge.herokuapp.com/lv1?token={GOGOGO}

### Lv1
- 了解如何在 token 後面帶上其他資訊（name）。
- https://lidemy-http-challenge.herokuapp.com/lv1?token={GOGOGO}&name=Heidi

### Lv2
- 了解如何在 token 後面帶上其他資訊（id）。
- 這題是 54~58 一個一個試，最後發現 id 56 符合結果。
- https://lidemy-http-challenge.herokuapp.com/lv2?token={HellOWOrld}&id=56


### Lv3
- 了解如何透過 node.js 傳入 request，利用 POST 來新增書籍。
- 這題稍微卡關了一下，因為一開始有點搞不懂 header、body、form 的階層關係，修改過後原來這兩種寫法都能得到 response：`{ message: '新增成功', id: '1989' }`。

```js
// 在 body 以 query string 帶入 name 和 ISBN
  headers: {
    'content-type': 'application/x-www-form-urlencoded',
  },
  body: 'name=《大腦喜歡這樣學》&ISBN=9789863594475'
```

```js
// 在 form 帶入 name 和 ISBN
headers: {
  'content-type': 'application/x-www-form-urlencoded',
},
form: {
　name: '《大腦喜歡這樣學》',
　ISBN: '9789863594475'
}
```

- 得到回應為：`{ message: '新增成功', id: '1989' } `
- https://lidemy-http-challenge.herokuapp.com/lv3?token={5566NO1}&id=1989


- https://lidemy-http-challenge.herokuapp.com/lv4?token={LEarnHOWtoLeArn}

### Lv4

- 了解如何透過 GET 來獲得所有書籍資訊，並從中篩選出符合結果的書名和作者。
- 得到回應為：` { id: 79, name: '世界末日與冷酷異境', author: '村上春樹', ISBN: '9571313408' },`
- https://lidemy-http-challenge.herokuapp.com/lv4?token={LEarnHOWtoLeArn}&id=79

- https://lidemy-http-challenge.herokuapp.com/lv5?token={HarukiMurakami}

### Lv5

- 了解如何透過 DELETE 來獲得刪除書籍。
- 得到的 response 好像讓人有點不安（？）下一題才知道是因為沒有登入就能夠新增刪除，缺少了身分驗證。

```
{
  message: '\n咦...是刪掉了沒錯，但總覺得哪裡怪怪的，算了，先這樣吧！下一關的 token 為 {CHICKENCUTLET}\n'
}
```

- https://lidemy-http-challenge.herokuapp.com/lv6?token={CHICKENCUTLET}

### Lv6

- 了解如何將 http basic authorization 帶入 Header 進行身分驗證。
- 這關有稍微卡住，一開始是驗證碼少加 Basic，然後又卡一陣子找不到錯在哪裡，才發現原來是 圖書館資訊系統 API v2 接點最後有多加 `/v2`，真是魔鬼藏在細節裡，諸此類小地方都需要更加注意！

```js
  method: 'GET',
  url: `https://lidemy-http-challenge.herokuapp.com/api/v2/me`,
  headers: {
    'Authorization': 'Basic YWRtaW46YWRtaW4xMjM=',
  },
```

- 得到回應為：`{ username: 'admin', email: 'lib@lidemy.com' }`
- https://lidemy-http-challenge.herokuapp.com/lv6?token={CHICKENCUTLET}&username=admin&email=lib@lidemy.com

- https://lidemy-http-challenge.herokuapp.com/lv7?token={SECurityIsImPORTant}

### Lv7

- 和 Lv5 類似。了解如何透過 DELETE 刪除書籍，但要在 Header 多加上身分驗證。

```js
  method: 'DELETE',
  url: `https://lidemy-http-challenge.herokuapp.com/api/v2/books/89`,
  headers: {
    'Authorization': 'Basic YWRtaW46YWRtaW4xMjM=',
  },
```

- 得到回應為：`{ message: '\n希望下一次進這本書的時候不會再被偷走了。下一關的 token 為 {HsifnAerok}\n' }`
- https://lidemy-http-challenge.herokuapp.com/lv8?token={HsifnAerok}

### Lv8

- 了解如何透過 GET 查詢書籍，再以 PATCH 更改書籍資訊。

```js
  method: 'GET',
  url: `https://lidemy-http-challenge.herokuapp.com/api/v2/books`,
  headers: {
    'Authorization': 'Basic YWRtaW46YWRtaW4xMjM=',
  },
```

- 找到與目標相符的資料：`{id: 72, name: '日日好日：茶道教我的幸福15味【電影書腰版】', author: '森下典子', ISBN: '9981835427'},`，一開始還沒看清楚作者名字是四個字，幸好最後檢查有發現到！

- 把 ISBN 最後一碼改成 3，和 Lv3 使用的 POST 一樣要在 Header 加上 content type：

```js
  method: 'PATCH',
  url: `https://lidemy-http-challenge.herokuapp.com/api/v2/books/72`,
  headers: {
    'Authorization': 'Basic YWRtaW46YWRtaW4xMjM=',
    'content-type': 'application/x-www-form-urlencoded',
  },
  form: {
    name: '日日好日：茶道教我的幸福15味【電影書腰版】',
    ISBN: '9981835423'
  },
```

- 得到回應為：`{ message: '\n希望之後他們能引進語音輸入系統，我就只要講講話就好。下一關的 token 為 {NeuN}\n' }`
- https://lidemy-http-challenge.herokuapp.com/lv9?token={NeuN}

## Lv9

- 了解如何在 Header 帶上參數 `X-Library-Number` 和 `User-Agent`。想到在 Request 補充影片剛好就有提到 `User-Agent` 的用途，查到了這篇文章：[談談UserAgent 字符串的規律和偽造方法](https://zhuanlan.zhihu.com/p/28086252)。

```js
  method: 'GET',
  url: `https://lidemy-http-challenge.herokuapp.com/api/v2/sys_info`,
  headers: {
    'Authorization': 'Basic YWRtaW46YWRtaW4xMjM=',
    'User-Agent':'MSIE 6.0',
    'X-Library-Number': '20'
  },
```

- 得到回傳值：

```js
{
  message: 'success',
  version: '1A4938Jl7',
  owner: 'lib',
  createdAt: '121290329301'
}
```
- 把 version 的值放到 query string：https://lidemy-http-challenge.herokuapp.com/lv9?token={NeuN}&version=1A4938Jl7

- https://lidemy-http-challenge.herokuapp.com/lv10?token={duZDsG3tvoA}

## Lv10

- 了解如何把數字放在 query string，並且用 num 當作 key 傳入，藉由回傳值判斷是否為目標數字。

- https://lidemy-http-challenge.herokuapp.com/lv10?token={duZDsG3tvoA}&num=9613
- 這題還蠻快就猜到答案了，自己都有點嚇到XD 想說先判斷出四個對的數字之後再調整位置，能夠迅速解開真是太開心了！


