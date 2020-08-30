###### tags: `程式基礎` `Command Line`

# [Week 1] Command Line 入門 & 基本指令

> 本篇為 [[CMD101] Command Line 超新手入門](https://lidemy.com/p/cmd101-command-line)和 [[CS101] 初心者的計概與 coding 火球術](https://lidemy.com/p/cs101-coding) 這兩門課程的學習筆記。

```
學習目標：

 說得出程式如何執行
 理解寫程式的本質只是一行行的指令
 了解前端與後端的區別
 能說出從發出一個 request 到接收 response 中間發生的事
 了解不同載具的差異在哪（Desktop、Mobile、Web）
 了瞭解基本的 command line 指令
 了解 Git 在做什麼，以及為何我們需要 Git
 知道 add、commit、push、pull 等基本 Git 指令
 知道怎麼使用 branch 並送出 Pull Request
 熟悉 Git Workflow（也就是交作業的流程）
```

---

## 關於寫程式這件事
就是對電腦下指令（程式碼），讓電腦執行我們想要的動作（解決問題）。
## 條列式的重要性
試著用「程式化思考」將解法以標準化的格式呈現。

> 範例：「給你一串文字，問你裡面有沒有 p」

```
1. 假設 n 是 1，length 是字串長度，
   A 設成 -1 代表找到的位置，p 是要找到的字   //字串不會有負數，若小於零代表找不到

2. 看第 n 個字是不是 p，是的話 A=n　　//把 A 設成 n 的值，若 n=3，即在第 3 個數找到 p

3. 把 n+1，如果 n+1 大於 length 的話，就跳到第 5 步　　//條件機制

4. 跳回第 2 步　　//迴圈的概念

5. 如果 A < 0，代表沒出現過；否則 A 就是答案
```

---

## Command Line 是什麼？
- 操控電腦的方法大致上分兩種類型：
1. **GUI（Graphical User Interface，圖形化介面）**：一般習慣使用的電腦操作介面，透過設計出的「圖形」來下指令。
2. **CLI（Command Line Interface，命令列介面）**：透過「純文字」來達成指定動作。
- 兩種都可以用來和電腦溝通；但有些伺服器可能沒有 GUI （例如 database），這種情況就只能使用 CLI 來進行操作。


## 環境設置
- 要執行 Command Line 指令，必須先根據作業系統安裝 Command Line Tool：
1. Windows：[git-bash](https://gitforwindows.org/)（推薦）、[Cmder](https://blog.miniasp.com/post/2015/09/27/Useful-tool-Cmder)
2. Mac：[iTerm2](http://huli.logdown.com/posts/402147-iterm2-zsh-better-environment)

- 或是使用 [JSLinux](https://bellard.org/jslinux/) （[以 JavaScript 寫成的 Linux 模擬器](https://zh.wikipedia.org/wiki/Jslinux)）來統一環境，即可在瀏覽器介面進行測試。

## 基本指令介紹
> 所謂的「指令」其實也是由程式寫成，我們藉由輸入指令來執行程式。

#### `pwd`（Print Working Directory）：印出目前所在位置

#### `ls`（List）：印出當前位置檔案清單
- `ls-l`：長格式（long），顯示有關檔案的詳細資訊
- `ls-a`：列出所有檔案（包括隱藏檔）

#### `cd`（Change Directory） 切換目前資料夾
- `cd ..`：回到上一層資料夾
- `cd ~`：回到 home 目錄（我的文件）
- `cd /`：回到根目錄
   - 絕對路徑：「一定由根目錄 / 寫起」
`cd /Users/share/data`
   - 相對路徑：「不是由 / 寫起」
`cd test`
-  範例：「若要從 /downloads/test/data 到 /downloads/test/media 底下時」
可以寫 `cd ../media`：先回到上一層，再進入 media 資料夾
也可寫 `cd ~/downloads/test/media`：直接用絕對路徑進入

![cd 語法練習](https://i.imgur.com/emeMG2h.png)

> 小技巧：直接把圖形介面的資料夾拖到 CLI 裡，就會自動輸入那個資料夾的絕對路徑。可節省打字時間。

#### `clear`：清空螢幕

#### `touch`：碰一下
   - 作用 1：touch 現有的檔案，會修改檔案時間成當前時間 
   - 作用 2：若檔案不存在，則會新增檔案 

#### `mkdir`：MaKe DIRectory 新增資料夾
#### `rm`：ReMove 刪除檔案（註：有關刪除指令需慎用）
#### `rmdir`：刪除資料夾
- 也可用 `rm -r` 指令來刪除檔案或資料夾
#### `cp`：CoPy 複製
   - 範例 1. `cp data data_2`：複製出一個 data_2 檔案
   - 範例 2. `cp -r deep deep2`：複製出一個 deep2 資料夾

#### `mv`：MoVe 移動檔案 or 更改檔名
   - 作用 1：當找到該資料夾時，檔案會**移到資料夾裡**
        - 範例：`mv data folder`：將 data 檔案移到 folder 資料夾裡
   - 作用 2：當找不到該資料夾，則會**更改檔案名稱**
        - 範例：`mv data test`：將 data 檔案名稱更改為 test
#### `vim`：進入文字編輯器
> 本來想說沒問題，結果還真的卡很久無法回到上層介面 Orz
需按下 [ESC] 按鍵回到一般指令模式，才能下 `:wq` 指令存檔（write）並離開 （quit)。
#### 參考 vim 介紹：

可分為普通模式跟編輯模式：

- 按鍵 i：進入編輯模式
- 按鍵 Esc：進入普通模式
- `:q` 退出
- `:wq` 存檔（write）後退出（quit)
- `:q!` 不存檔直接退出

參考資料：
1. [vi 與 vim 的指令整理](http://www.vixual.net/blog/archives/234)、
2. [鳥哥的Linux 私房菜-- 第九章、vim 程式編輯器](http://linux.vbird.org/linux_basic/0310vi.php)

## 更多常用指令
#### `date`：印出當前日期
#### `top`（Table Of Processes）：印出所有Process，可持續偵測程序運作的狀態（按 q 鍵離開）
#### `cat`（CATenate）：查看檔案內容
#### `less`：分頁式印出檔案
#### `grep`：抓取特定關鍵字
#### `echo`：印出字串
#### `|`（pipe）：把前面的輸入「變成」後面的輸出
   - 範例：`cat file.txt  | grep hi `：從印出的 file.txt 內容中，抓取字串 hi 並輸出
#### `>`（redirect）：將輸入或輸出重新導向
   - 範例 1：`date > time.txt`：將 `date` 輸出到 `time.txt`
  　　再輸入 `cat time.txt`，會顯示出 `Sun May 31 20:29:57 UTC 2020`
         
   - 範例 2：`echo "123" > 123.txt`：印出 123 到 123.txt
　1. 若輸入 `echo "456" > 123.txt`：顯示 456，檔案會被全部覆蓋
　2. 若輸入 `echo "456" >> 123.txt`：顯示 123456，兩個 `>` 會新增內容在最後

#### `wget`：下載檔案
   - 範例：輸入 `wget https://www.google.com.tw/`，即可下載 Google 首頁的網頁原始碼（index.html）
 
#### `curl`：送出 request，可用來測試 API


---

### 補充：檔案格式
+ 內容是一回事，如何解讀是另一回事
+ 在電腦裡的任何檔案，其實都是以純文字組成，可用不同格式進行解析
+ 可以透過設定副檔名（如：文件.pdf、圖片.jpg）幫助電腦辨別格式


參考資料：
1. [[第一週] Command Line - Terminal、基本指令介紹](https://yakimhsu.com/project/project_w1_CommandLine.html)
2. [Linux Command 命令列指令與基本操作入門教學](https://blog.techbridge.cc/2017/12/23/linux-commnd-line-tutorial/) 
3. [介紹好用工具：Cmder ( 具有 Linux 溫度的 Windows 命令提示字元工具 )](https://blog.miniasp.com/post/2015/09/27/Useful-tool-Cmder)
4. [[心得] iTerm2 + zsh，打造更好的工作環境](http://huli.logdown.com/posts/402147-iterm2-zsh-better-environment)
