###### tags: `Git`

# [week 1] 版本控制 - Git 概念 ＆ 基本指令

> 本篇為 [[GIT101] Git 超新手入門](https://lidemy.com/p/git101) 這門課程的學習筆記。

## 什麼是版本控制？

#### 簡言之，就是將一個檔案所有歷史紀錄的版本都保存起來，以便日後參考。

我們其實曾接觸過版本控制，例如備份檔案，或是以不同檔名保存編輯過的檔案：

![個人的版本控制](https://i.imgur.com/2nnCsCD.png)
## 但這種管理方式會碰到哪些問題？

1. 檔案一多、時間久了之後不易管理
2. 多人協作易發生衝突，無法快速分辨檔案差異

## 那麼，該如何做版本控制呢？

1. 需要新版本時：
開一個新資料夾，用來完整保存當時檔案狀態。
3. 不想加入版本控制：
就不需加入資料夾，例如有些不會更動的檔案（電腦設定檔、log 檔），或者不需做版控的檔案（例如帳密等有安全性考量）。
5. 避免版本號衝突：
會以亂數，也就是絕不會重複的流水號作為資料夾名稱。
7. 知道最新版本：
開一個檔案（new）來存最新的版本號。
9. 保存歷史紀錄：
開一個檔案（order）整理流水號。
 
#### 上述概念，其實就類似 Git 版本控制系統的運作模式。目的是進行備份與共用，讓電腦自動幫我們做一些記錄與管理。

> 補充：GitHub 就是透過 Git 進行版本控制的軟體原始碼代管服務平台。

---

## Git 基本指令操作

### `git init`：初始化當前目錄

在當前位置新增 `.git` 隱藏資料夾，也就是建立 Git Repository（檔案庫）。目的是初始化這個目錄，讓 Git 對這個目錄做版本控管。

![init 初始化](https://i.imgur.com/BVuxZag.png)

### `git status`：檢查當前版本狀態

例如：未進入版本控管（Untracked）、新增檔案（New File）、刪除檔案（Deleted）、檔案已修改（Modified）等。是會經常使用的指令。

### `git add`：加入版本控制

把檔案從 Untracked（不加入的檔案）移到 stage（加入版本控制的檔案）。

依照括號內提示可知：
- `git rm --cached <file>`：取消版控
- `git add <file>`：加入版控
- 若檔案數量太多，可使用 `git add .` 把檔案全部加入版本控制。

![git add 指令](https://i.imgur.com/vX2e43W.png)
 
### `git commit`：新建版本

1. 輸入指令後會進入 vim 介面，就可以編輯提交版本訊息（commit message）
2. 若不想進入 vim，也可輸入 `git commit -m "message"` 直接建立新版本

![git commit 指令](https://i.imgur.com/NlBaU85.png)

### 小技巧：`git commit -am　"message"` 

輸入 `git commit -am "message"` 可一次完成 `add` 和 `commit` 兩個指令動作。

#### 但需注意：

1. `-a` 指令只對「已存在」的檔案有效；不適用於新加入的檔案（Untracked File）
2. 若有新增的檔案，仍須先 `add` 再 `commit` ，才能把新檔加入版控

### `git log`：查看版本紀錄

從新到舊列出，內容分別是版本號碼、提交者、提交時間。

![git log 指令](https://i.imgur.com/gZ9G2c1.png)

- `git log --oneline`：輸出更簡短的 log，只用版本號前七碼代值

![git log --oneline 指令](https://i.imgur.com/V0HyJbP.png)

### `git checkout`：切換到某個版本

切換到某個版本，可查看過去的版本內容。
- `git checkout <版本號碼>` ：切換到指定版本
- `git checkout master` ：切換到最新版本

![git checkout 指令](https://i.imgur.com/hqROiIw.png)

### `.gitignore`：忽略不要版本控制的檔案

`.gitignore` 這個檔案本身也需加入版本控制。通常會加入忽略清單的檔案有：系統檔案、記錄檔、暫存檔等，可參考 [Facebook 開源專案 React 裡的 .gitignore](https://github.com/facebook/react/blob/master/.gitignore) 為例子。

#### 那麼，該如何建立忽略清單呢？

1. 輸入 `touch .gitignore` 建立檔案
2. 輸入 `vim .gitignore` 進入 `vim` 介面
 
![進入vim介面](https://i.imgur.com/dwIzY2m.png)

3. 以不加入版控的 test 為例，輸入完 test 後儲存離開

![建立忽略清單](https://i.imgur.com/WtYtJkL.png)

4. 以 `git status` 查看，可知 test 這個檔案已被 git 忽略

![完成忽略清單](https://i.imgur.com/XUsgqPr.png)

5. 如此即可使用 `git add .` 把所有檔案加入版本控制，包括新舊檔案；
再用 `git commit -m "message"` 建立新版本


## 小結

複習上述指令，以下為開始使用 git 的流程：

1. `git init`：初始化當前位置，讓 Git 對這個目錄進行版控
2. 建立 `.gitignore` 忽略不需版本控制的檔案
3. `git add .`：把所有檔案加入版本控制（把東西放到一個暫存資料夾 `temp`  ）
4. `git commit -am "message"`：新建一個版本（把 `temp` 資料夾改名為`"版本號"`）
   - 若有新檔案，需重複`步驟 3.` 把所有檔案加進版本控制，才能執行 `commit`
   - 在 commit 之前，可用 `git diff` 查看與上一版的差異
5. `git checkout <版本號>` ： 可以切換各個版本（去到某個資料夾底下）
`git checkout master` ： 回到最新版本

> 操作 Git 時突然每行句首都出現 `>` 這個符號，無法繼續輸入指令怎麼辦？
> 
>解決方法：ctrl + d 即可退出。這種情況可能是因為只輸入了一邊雙引號或單引號，導致沒有輸入完成（輸入沒有閉合）。

---

### 補充 1：檔案在 Git 底下的四種狀態

- File status lifecycle（圖片來自 [link](https://kueiblog.herokuapp.com/post/git%E5%AD%B8%E7%BF%92%E4%B9%8B%E4%BA%8C%20-%20commit%E8%AC%9B%E8%A7%A3%E3%80%81reset%E5%BE%A9%E5%8E%9F%E7%89%88%E6%9C%AC%E3%80%81%E5%BB%BA%E7%AB%8B%E5%88%86%E6%94%AF/)）
![File status lifecycle](https://i.imgur.com/jz9qufH.png)

1. untracked（未追蹤的）：代表檔案尚未被加入版控
2. staged（暫存區）：等待被 commit 的檔案，才能執行 `git commit` 指令送入版本庫
3. unmodified（未修改的）：代表檔案第一次被加入，或檔案內容與 `HEAD` 內容一致的狀態
4. modified（已修改的）：代表檔案已經被編輯過，或檔案內容與 `HEAD` 內容不一致的狀態


### 補充 2：設定帳號和姓名

第一次輸入 `git commit` 指令時出現錯誤，跳出需要設定帳號和姓名的畫面，這時需輸入以下指令：
```
git config --global user.name "your name"　　//  "換成自己的帳號和信箱"
git config --global user.email "your email"
```


參考資料：

1. [用 Git 進行版本控制](https://djangogirlstaipei.herokuapp.com/tutorials/version-control-with-git/?os=windows)
2. [【工程師必懂的版本控制技術】什麼是GitHub?](https://medium.com/@makerincollege2018/%E5%B7%A5%E7%A8%8B%E5%B8%AB%E5%BF%85%E6%87%82%E7%9A%84%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6%E6%8A%80%E8%A1%93-%E4%BB%80%E9%BA%BC%E6%98%AFgithub-376421fd871d)
3. [Git 筆記| Summer。桑莫。夏天](https://cythilya.github.io/2018/04/05/git/)
4. [[第一週] 版本控制 - 原理 ＆ 基本 Git 指令 by Yakim](https://yakimhsu.com/project/project_w1_Git_1.html)
5. [[KueiKuei 's Blog] git學習之二 - commit講解、reset復原版本、建立分支](https://kueiblog.herokuapp.com/post/git%E5%AD%B8%E7%BF%92%E4%B9%8B%E4%BA%8C%20-%20commit%E8%AC%9B%E8%A7%A3%E3%80%81reset%E5%BE%A9%E5%8E%9F%E7%89%88%E6%9C%AC%E3%80%81%E5%BB%BA%E7%AB%8B%E5%88%86%E6%94%AF/)
