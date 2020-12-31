###### tags: `Git` `GitHub`
# Git 版本控制：如何進行多人協作 & 同步分支 

[TOC]

## 實作新功能一定要在分支進行

不管是在個人專案，還是在多人協作時，都必須記得在實作新功能時，一定要在分支上進行：

```
$ git branch <new_branch>  // 開新分支
$ git checkout <new_branch>  // 切換到新分支
$ git commit -am 'new_commit' 
$ git push origin <new_branch>  // push 分支
```

## 多人協作之狀況劇

在課程最一開始寫的 Git 筆記：[[week 1] 版本控制 - Git 進階指令 & GitHub](https://heidiliu2020.github.io/git-github/)，也有提到一些常見狀況劇。

最近在進行 Final Project 時，體驗到多人協作和個人專案的不同，有許多需要注意的地方，這裡舉幾個自己實際遇到的狀況以及解決方式：

- 狀況一：如何同步分支
- 狀況二：如何新增要忽略版本控制的檔案
- 狀況三：如何撤回已經 push 的 branch
- 狀況四：我 commit 了可是我又不想 commit 了

### 狀況一：在 push 分支之前，該如何讓分支同步 master？

當我們要 push 分支時，如果沒有先同步 master 進度，就會出現衝突問題，有兩種方式解決：

#### 第一種：切回 master 同步，直接再開一個新分支

這個方法可以確保新分支一定是同步遠端的狀態，缺點就是要記得先備份好要 commit 的內容，才能切回 master 同步：

```
$ git checkout master  // 切回 master
$ git pull origin master  // 同步遠端 master
$ git branch <new_branch> // 新開分支
$ git checkout <new_branch>  // 切換到分支
$ git commit -am 'new_commit' 
$ git push origin <new_branch> // push 分支
```

#### 第二種：繼續在舊的分支開發，需要先同步分支

較推薦這個方法，直接在舊分支同步遠端，就不須再另外新開分支

```
$ git pull origin master  // 同步遠端 master
$ git commit -am 'new_commit'
$ git push origin <old_branch> // push 分支
```

依照上述其中一種方式，之後就可以在 GitHub 頁面發 PR 進行 merge，解決衝突問題。
<br>

### 狀況二：不小心把要被忽略的檔案 push 上去了！

如果是在建立 `.gitignore` 之前，不小心把要被版本控制忽略的檔案給 push 上 GitHub 的話該怎麼辦呢？

以 React 中會自動生成的 `.eslintcache` 為例，如果在忽略之前就先把檔案 push 上去，之後多人協作要進行 merge 時，都必須解決衝突問題，這時只要按照以下步驟：

#### 1. 切換到 master，同步專案

```
$ git pull origin master
```

#### 2. 刪掉本地端的 `.eslintcache` 檔案

#### 3. 將 `.eslintcache` 加入專案根目錄的 `.gitignore` 忽略版本控制

```=
// .gitignore 檔案

*.eslintcache
```

#### 4. commit 本次操作，commit 名稱可自行修改

```
$ git commit -am "fix: remove .eslintcache"
```

#### 5. push 到 GitHub master，這時 GitHub 上的專案就不會出現應該被忽略的檔案了

```
$ git push origin master
```

#### 6. 讓其他協作者 pull 同步專案，修改完成！
<br>

### 狀況三：我 commit 了可是我又不想 commit 了

#### 回到上一個 commit 狀態，保留檔案變更，需要再 commit 來新增版本：

- HEAD 代表最新的，^ 代表前一個，因此 HEAD^ 就是上一個 commit 的狀態
- 預設是 –mixed，可參考 [Git reset 的三種模式（soft mixed hard）](https://ithelp.ithome.com.tw/articles/10187303)比較

```
$ git reset HEAD^  // 退回到上一個 commit，等同於 git reset HEAD~1
$ git log   // 可查看版本號
$ git reset <38e7e30 >  // 退回到 <38e7e30> 這個 commit
```
<br>

### 狀況四：如何撤回已經 push 的 branch

#### 1. 退回本地端的 commit 狀態

需注意這裡用 `git reset --hard` 指令，除了回到指定 commit 版本，程式碼也會回到指定版本的狀態：

```
$ git log   // 可查看版本號
$ git reset --hard <6gye93e>  // 退回到 <6gye93e> 這個 commit
```

#### 2. 強制 push 本地端 branch 到遠端

由於本地端分支版本低於遠端分支版本，這裡必須使用 `--force` 來強制 push 想要拆掉重做的 branch：

```
$ git push origin <branch> --force
```

這樣就成功撤銷已經 push 的 branch，回到指定的 commit 狀態了！
<br>

## 結語

其實 Git 版本控制是在 Lidemy 課程最一開始學的，之後繳交作業都是透過 Git 進行，中間也曾遇到種種狀況，像是不小心把同步課程大綱也 push 上去等等。

最近和同學實際進行團體協作，才發現需要注意的事情又更多了！常常一個不注意下錯指令，把應該 push 的分支 push 成 master，還沒同步分支必須解決的衝突問題等等，趁這個機會把一些自己實際遇到的狀況給寫下來。熟悉多人協作以後，感覺自己又對版本控制有更深一層的認識。
