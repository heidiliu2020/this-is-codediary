# FE 基本情報技術者試驗｜準備方式＆學習筆記

## 試驗說明

[IPA 基本情報技術者試験](https://www.ipa.go.jp/shiken/kubun/fe.html)

![ipa](https://hackmd.io/_uploads/Syi3dl6-We.png)

## 準備方式

+ [IPA 官方範例試題](https://www.ipa.go.jp/shiken/syllabus/henkou/2022/20221226.html)
+ [基本情報技術者過去問道場](
https://www.fe-siken.com/fekakomon.php)
+ 跟公司借的參考書（但其實啃不下去）：[キタミ式イラストIT塾 基本情報技術者 令和03年](https://gihyo.jp/book/2020/978-4-297-11781-8)
+ 發現自己看書效益不大，找到 YouTube 上的教學：
    + [【最新版】基本情報技術者試験の科目Aを12時間で完璧に理解](https://www.youtube.com/watch?v=oqaBEnhIxk0)
    + [【科目B】アルゴリズム問題をたった1動画で対策_基本情報技術者試験](https://youtu.be/wFpyeWto8Og?si=aierZ8TcbOJ-rhkV)

:::info
### 📌 基本情報 8 週計劃

W1-2: 影片學習科目 A + 專有名詞筆記
W3-4: 每日道場練習 20 題 + 整理錯題筆記
W5-6: 針對弱項特訓 + 每日 40 題
W7: 練習模擬考題（目標 70%↑）
W8: 錯題總複習 + 調整作答速度
:::

## 學習筆記

科目 A：

+ 基礎理論
    + n 進位
    + 基數變換
    + 2 進位加減法
    + 位移運算子 Shift Operator
    + 誤差
    + 理論演算和理論回路
    + 半加算計和全加算器
    + 機率統計
    + 線形代數
+ AD 變換
+ 硬體 Hardware
    + 五大裝置
    + CPU
    + 主記憶體
        + 記憶體分類
        + 記憶體高速化
    + 補助記憶體裝置
    + 輸出入裝置
+ 軟體 Software
+ 資料庫 Database
    + 資料庫正規化 Normalization
    + 交易處理 Transaction
        + Atomicity（原子性）
        + Consistency（一致性）
        + Isolation（隔離性）
        + Durability（持久性）
    + 資料復原方法
    + 分散式資料庫
    + 關係演算和 SQL
    + 機械學習和深度學習
+ 網路 Network
    + 網路基礎
    + OSI 模型
    + TCP/IP 網路
    + IP Address
    + 子網路遮罩 Subnet Mask
+ 資訊安全 Information Security
    + 電腦病毒
    + 伺服器攻擊
    + 加密與數位簽章
    + 網路安全
    + 科目 B 對策
+ 演算法與程式語言
+ 系統構成
+ 系統開發
+ 管理相關
+ 戰略相關

科目 B：20 題 = 100 分

+ 演算法大綱與學習方式
+ 演算法基礎：變數、陣列、基本演算法
+ 疑似言語：Pseudocode（偽代碼）
+ 物件導向語言

## 基礎理論

### n 進數

計算機基礎概念：

+ 二進位：是計算機使用的數字系統
    + 只使用 0 和 1 兩個數字
    + 每位的值是 2 的冪次
+ Bit（Binary digit，位元）：二進位數字的最小單位
    + 只能是 0 或 1
    + 計算機中資訊處理的基礎單位
+ Byte（位元組）：由 8 個 bit 組成的資料單位
    + 可表示範圍：二的八次方 = 256 種不同的值（0-255）
    + 用途：通常用來表示一個字符（如 ASCII 字符）
    + 1 byte = 8 bits
    + 1 KB = 1,024 bytes = 2 的十次方 bytes
    + 1 MB = 1,024 KB = 2 的二十次方 bytes
    + 1 GB = 1,024 MB = 2 的三十次方 bytes
+ 亂碼（文字化け）：指文字顯示異常的現象，當字符編碼不正確時造成
+ n 位元可表示 2 的 n 次方種不同的值
    + 4 位元：0-15（16種）
    + 8 位元：0-255（256種）

### 基數變換

+ n 進位 → 十進位
    + 方法：每位數 × 對應 n 的冪次，然後相加
+ 十進位 → n 進位
    + 有兩種方法，推薦方法二（先記住算法就好），遇到八或十六進位，一律先換算成二進位再十進位
    + 方法一：計算每位數的權重
        + 缺點是當數值較複雜時不易計算
    + 方法二：透過乘法和除法計算
        + 「整數」連續除以 2，取餘數，由下往上讀，直到商等於 `0`
        + 「小數」連續乘以 2，取整數，由上往下讀，直到積等於 `1.0`

### 2 進位加減法

+ 補數（Complement）：計算機中表示負數和進行減法運算的重要概念。
    + 透過加法器同時處理加法和減法，而不需額外的減法器
    + 計算時需注意數值轉換、位數範圍、溢位判斷
+ 2 進位補數系統
    + 1 的補數：每一位元反轉（0↔1）
        + 以 4 位元為例：0000（+0） → 1111（-0）、0111 → 1000（範圍：-7 到 +7）
    + 2 的補數：先求 1 的補數，再將結果 +1
        + 範例：1100 → 0011 （1 的補數） → 0100 （加 1）

### 位移運算子 Shift Operator

位移運算（シフト演算）是計算機中對二進制數字進行左移或右移操作的運算方式，主要分為兩種類型：

+ 邏輯位移（論理シフト）：最基本的位移操作。不考慮符號，是單純的位元移動
    + 左移（Left Shift）：乘法
        + 所有位元向左移動指定位數
        + 右邊空出的位置補 0
        + 左邊出現「1」時代表溢位（Overflow）
    + 右移（Right Shift）：除法
        + 所有位元向右移動指定位數
        + 左邊空出的位置補 0
        + 右邊超出的部分為除法的「餘數」
+ 算術位移（算術シフト）：主要用於處理有符號數（signed numbers）。考慮數字的正負號，右移時保持符號位
    + 左移（Left Shift）
        + 與邏輯左移相同，右邊補 0
        + 左邊出現與「符號位元」不同的數字代表溢位
    + 右移（Right Shift）
        + 等同於除以 2 的冪次（向下取整），最高位（符號位）保持不變
        + 對於正數：左邊補 0
        + 對於負數：左邊補 1（保持相同符號位元）
        + 右邊超出的部分為除法的「餘數」

## 硬體 Hardware

### 記憶體 RAM vs ROM

記憶體的種類可分為：

+ RAM（Random Access Memory）：電腦的主要記憶體
    + 用來暫時儲存正在執行的程式和資料
    + 斷電後資料會消失（揮發性）
    + 例如：
        + DRAM（主記憶體）
        + SRAM（快取記憶體）
+ ROM（Read Only Memory）：唯讀記憶體
    + 用來永久儲存重要的系統程式
    + 資料在斷電後保存（非揮發性）
    + 例如：
        + Mask ROM（遮罩ROM）
        + PROM（可程式化 ROM）

### RAM：DRAM vs SRAM

+ DRAM：用於主記憶體
    + 使用電容器（Capacitor，コンデンサ）儲存資料
    + 每個記憶單元 = 1 個電晶體 + 1 個電容器
    + 優點：結構簡單、成本低、適合大容量製造
    + 缺點：容器會自然放電、資料會逐漸消失，必須定期刷新
+ SRAM：用於快取記憶體
    + 使用觸發器電路（Flip-Flop Circuit，フリップフロップ回路）儲存資料
    + 每個記憶單元 = 6 個電晶體組成一個觸發器
    + 優點：穩定儲存、不需刷新、存取速度極快
    + 缺點：結構複雜、成本高、佔用面積大

### ROM：Mask ROM vs PROM

+ Mask ROM：遮罩 ROM
    + 唯讀記憶體
    + 在製造時寫入資料並永久儲存
    + 成本低，適合大量生產固定功能
    + 範例：遊戲卡匣
+ PROM：一次性可程式化 ROM
    + 適合小量生產客製開發，寫入錯誤無法修改
+ EPROM：可抹除 PROM
    + 紫外線方式抹除，可重複使用，適合頻繁修改測試
    + 應用：早期 BIOS、開發板韌體
+ EEPROM：電子抹除 PROM
    + 電氣抹除，可選擇性抹除，支援在線更新
    + 應用：主機板設定
+ Flash Memory：快閃記憶體
    + EEPROM 的改良版本，高容量低成本，以區塊（Block）為單位抹除
    + 應用：SSD、USB隨身碟、記憶卡

### 主記憶體高速化

透過以下兩種方式可達到高速化效能：

+ 使用快取記憶體（Cache Memory）
+ 記憶體交錯存取（メモリインタリーブ Memory Interleaving）

記憶體裝置構造階層如下：

+ レジスタ（Register）：暫存器
    + 直接與 CPU 運算單元相連
    + 技術：觸發器電路（Flip-Flop）
+ キャッシュメモリ（Cache Memory）：快取記憶體
    + 位於 CPU 和主記憶體之間
    + 技術：SRAM
+ 主記憶體
    + 技術：DRAM（電容 + 電晶體）
+ ディスクキャッシュ（Disk Cache）：磁碟快取
    + 位於主記憶體和儲存裝置之間
+ 輔助記憶體
    + 技術：SSD（固態硬碟）、HDD（機械硬碟）

```
レジスタ (Register)           ← 最快/最貴/最小容量
    ↓ (0.3ns)
キャッシュメモリ (Cache)       ← 極快/很貴/小容量  
    ↓ (1-40ns)
主記憶体 (Main Memory)        ← 中速/中價/中容量
    ↓ (50-100ns)  
ディスクキャッシュ (Disk Cache) ← 快速緩衝
    ↓ (μs等級)
補助記憶体 (Secondary Storage) ← 最慢/最便宜/最大容量
    (ms等級)
```

### 補助記憶體裝置

+ 補助記憶體裝置（Secondary Storage Devices）
    + 定義：非揮發性的永久儲存裝置，用於長期保存程式和資料，斷電後資料不會消失
    + 例如：光碟、USB、SSD、HDD 等

### 硬碟機 HDD

+ 硬碟機（HDD - Hard Disk Drive）
    + 原理：使用磁性材料儲存資料（Platter、プラッタ），透過磁頭（Arm、アクセスアーム）讀寫磁碟表面的磁性區域（Head、磁気ヘッド）
    + 扇區（セクタ、Sector）：資料儲存最小單位，小檔案仍會佔用一整個扇區
    + 磁軌（トラック、Track）：磁碟片表面上的同心圓環
    + 柱面（シリンダ、Cylinder）：形成一個虛擬的圓柱體
+ HDD 的記憶容量
    + 1 Sector = 512 byte
    + 1 Track = 20 Sector = 512 * 20 byte
    + 1 Cylinder = 60 Track = 512 * 20 * 60 byte 約為 0.61 MB
    + 1500 Cylinders = 0.61 * 1500 約為 922 MB
+ 存取時間（Access time）
    + 定義：從「CPU 發出讀寫指令」到實際「開始傳輸資料」之間的總等待時間
    + 流程：CPU 發出指令 > 位置決め時間 > 回転待ち時間 > データ転送時間 > 資料返回 CPU
        + 尋軌時間（シーク時間、Seek Time）：磁頭移動到目標磁軌
        + 旋轉延遲時間（サーチ時間、Search Time）：等待目標扇區旋轉到磁頭下方
        + 資料傳輸時間（データ転送時間、Data Transfer Time）：實際讀寫資料
    + Access Time = Seek Time + Search Time + Data Transfer Time
    + 待ち時間 = Seek Time + Search Time
+ 資料管理
    + 碎片化（フラグメンテーション、Fragmentation）：隨著檔案持續更新刪除，使記憶體空間被分割成不連續小區塊，導致儲存效率降低和存取效能下降的現象
    + 解決方式：透過磁碟重組（デフラグ、Defragmentation）使分散的檔案區塊變成連續儲存
+ 提升硬體性能
    + 透過組合複數個硬體，能夠提升效能、可靠性和容量擴充
    + RAID：將多個硬碟組合成一個邏輯單元的技術
        + RAID 0（ストライプング、Striping）= 磁條化
        + RAID 1（Mirroring、ミラーリング）= 鏡像
        + RAID 5（Distributed Parity）= 分散式同位檢查

## 資料庫 Database

### 資料庫正規化 Normalization

+ 正規化：把資料表設計得「不重複、易維護」，透過一層層規則（第一至第三正規化），使資料表結構更合理呈現
    + 第一正規化
        + 一格一值：不能有清單、重複項
    + 第二正規化
        + 完全依賴主鍵：若主鍵是「複合鍵（多欄位組合）」時，其他欄位不能只靠其中一部分決定
    + 第三正規化
        + 不傳遞依賴：非主鍵欄位不能用另一個非主鍵欄位算出來

### 交易處理 Transaction

+ 交易（トランザクション，Transaction）：是指一組要一起執行的資料庫操作，比如轉帳操作不會只執行一半
+ 需符合 ACID 特性：
	+ **A**tomicity（原子性）：全部執行或全部不執行
	+ **C**onsistency（一致性）：交易資料保持正確狀態
	+ **I**solation（隔離性）：不同交易不會互相干擾
	+ **D**urability（持久性）：交易完成後結果會永久保存
+ 互斥鎖（排他制御 はいたせいぎょ）：同一時間只允許一個交易存取某筆資料，避免資料不一致的情況發生
    + 共享鎖（Shared Lock）：可以同時讀，但不能改
    + 排他鎖（Exclusive Lock）：其他交易不能讀也不能改
    + 死鎖（デッドロック Deadlock）：同時在等待對方釋放資源，永遠無法繼續執行交易
    + 鎖的粒度（Lock Granularity）：鎖定資料的範圍有多大
        + 粒度大：管理簡單安全但慢
        + 粒度小：管理複雜但效率高

### 預存程序 Stored Procedure

預存程序（ストアドプロシージャ）：將常用的資料庫操作「打包」起來，需要時直接呼叫執行，即可重複使用並提升效率。

### 資料復原方法

+ 備份 / 日誌
    + 備份檔案（バックアップファイル，Backup file）：可完整恢復整個系統或特定資料
    + 日誌檔案（ジャーナルファイル = Log file）：記錄系統或資料庫所有變更操作，可分更新前和更新後兩種
+ Commit / Rollback / Roll Forward
    + Commit：確認變更
    + Rollback：向後退回（復原取消變更）
    + Roll Forward：向前推進（重新套用變更）

### 分散式資料庫

+ 分散式資料庫：將資料分佈在不同的伺服器或地點，提高系統的可靠性和效能
+ 2相コミット（Two-Phase Commit，2PC）：在分散式資料庫中確保「所有節點」同時提交交易的協調機制，避免資料不一致的問題

### 關係演算和 SQL

+ 關係演算（Relational Operations）
    + 關聯式資料庫（RDB）中，用來操作資料表之間關係的基本運算方法
    + 資料表（table）可以想成「集合」，而關係演算的本質就是「集合運算」
    + SQL（Structured Query Language）是關係演算的實用語言化版本，讓我們能直接對資料庫執行查詢、修改、刪除或新增資料等指令
+ SQL 執行順序
    + FROM：從資料表取出資料
    + WHERE：從「資料表」中篩選出符合條件的資料
    + GROUP BY：依照欄位分組
    + HAVING：對「分組後的結果」再做條件篩選
    + SELECT：選擇要顯示的欄位或計算結果
    + ORDER BY：對結果排序

### 機械學習和深度學習

+ 機械學習（Machine Learning）：讓電腦透過「人提供的特徵（Feature）」，學習規則或模式
    + 有老師（Supervised）：透過模仿的方式，對資料特性進行預測、分類
    + 沒老師（Unsupervised）：由電腦自己找出規律，進行資料分群、特徵分析
    + 強化學習（Reinforcement）：獎勵導向，透過「試錯學習」讓報酬最大化
+ 深度學習（Deep Learning）：模仿人腦深層結構的運作方式，由電腦「自己找出特徵」學習，比傳統機械學習更強大且自動化

## 網路 Network

### 網路基礎

+ 封包（Packet）：網路中傳遞資料的最小單位
+ 傳送時間：資料（封包）從發送端完全送出所需的時間 = 資料大小 / 傳送速度
+ 回線速度：資料透過網路傳輸的最大速率，以 bps（bits per second） 為單位
+ 線路傳輸效率：有效傳輸資料的比例
    + 注意計算單位：1 byte = 8 bit、1 M bit = 1000000 bit
+ LAN vs WAN
    + LAN（Local Area Network）：區域網路
        + 有線 LAN：透過物理連線的方式
            + 乙太網路 Ethernet
            + CSMA/CD 方式：載波偵聽 / 碰撞偵測
        + 無線 LAN：透過無線電波（Wi-Fi），便利性高但需注意資安問題
    + WAN（Wide Area Network）：廣域網路

### OSI 模型

+ 協定 Protocol：兩個或多個系統之間，進行資料傳輸時遵守的規則和約定
+ OSI 模型：國際標準化組織（ISO）提出的 網路分層模型，用來描述網路通信過程的七個層次
    + 7	Application	應用層：HTTP、DHCP、DNS、SMTP、POP
    + 6	Presentation 表現層：TLS、SSL、JPEG、MPEG、ASCII
    + 5	Session	會談層：NetBIOS、DSI
    + 4	Transport 傳輸層：TCP、UDP
        + ゲートウェイ（Gateway）：閘道器，負責跨協定、跨網段的轉換
    + 3	Network	網路層：IP、ARP、ICMP
        + ルーター（Router）：路由器，依「IP 位址」轉送封包
    + 2	Data Link 資料鏈路層：PPP、Ethernet
        + ブリッジ（Bridge）：橋接器，用來連接不同的網段（Segment），依「MAC 位址」轉送封包
        + スイッチングハブ（Switching Hub）：交換器，又被稱作 レイヤ2スイッチ（Layer 2 Switch），多個 Bridge 的集合因此效率更高
    + 1	Physical 實體層：網路線、RS-232、UTP
        + NIC（Network Interface Card）：網路介面卡，是電腦連接網路的「接口」，擁有唯一的 MAC 位址，用來識別設備
        + リピイータ（Repeater）：中繼器，用來放大訊號
        + リピータハブ（Repeater Hub）：集線器，是一種具多個連接埠（port）的 Repeater

![OSI](https://hackmd.io/_uploads/BkeSoolRee.png)

> MAC 位址 vs IP 位址
> + MAC 位址 → 硬體層面的設備識別
> + IP 位址 → 網路層面的裝置位置識別

### TCP/IP Network

TCP/IP 由 OSI 七層協定簡化而來，為目前網路通訊的基礎架構，用來讓不同電腦之間能在網路上傳送、接收資料。

![TCP/IP](https://hackmd.io/_uploads/B1Yqhp7Rlg.png)

+ IP（Internet Protocol 網際網路協定）
    + 負責決定資料要送到哪裡
    + 常見：IPv4（常見格式：192.168.1.1）、IPv6（更長、更新格式：2001:db8::1）
+ TCP（Transmission Control Protocol 傳輸控制協定）
    + 負責可靠傳輸
    + 常見：HTTP（網頁）、FTP（檔案傳輸）、Email（SMTP/IMAP）、SSH
+ UDP（User Datagram Protocol 用戶資料包協定）
    + 負責快速傳輸
    + 常見：線上遊戲、視訊通話、音樂串流、DNS 查詢

#### NAT / NAPT / IP Masquerade

+ NAT（Network Address Translation 網路位址轉換）
    + 把「內部網路的私人 IP 位址」轉換成「外部公開 IP 位址」的技術
    + 用途：因為公開 IP（可上網的）數量有限，私人網路（LAN）裡的電腦常用私有 IP（例：192.168.x.x）無法直接連上網，中間需透過 NAT 做橋樑
+ NAPT（Network Address Port Translation）= IP マスカレード（IP Masquerade）
    + 是 NAT 的進階版，IP 位址 + Port 同時轉換
    + 用途：因為多台內部設備要共用同一個外部 IP，NAPT 可透過「不同 Port」區分不同設備

#### DHCP / Domain / DNS

+ DHCP（Dynamic Host Configuration Protocol）
    + 自動分配 IP 位址的協定
+ Domain 網域
    + 是網路上辨識主機（網站、伺服器）的一種 名稱，比數字形式的 IP 位址更易辨識
+ DNS（Domain Name System）
    + 是一種名稱解析（Name Resolution）系統，負責把網域名稱（Domain Name）轉換成 IP 位址

### IP Address / Subnet Mask / Network Address

+ IP Address（IP 位址）
    + 用來在網路上識別每一台設備
    + 以 8 bit為一組，分成 4 組，共 32 位元 bit 的數字
    + 由兩個部分組成：
        + Network 部分（網路部）：表示哪一個網路，用來識別哪個子網（subnet）或區段
        + Host 部分（主機部）：表示哪一台設備，用來識別網路中的哪一台主機
+ Subnet Mask（子網遮罩）
    + 用來分辨 IP 的哪一部分是 Network 或 Host
    + 寫法同樣是 4 組數字（各 8bit），例如：`255.255.255.0` 或 `/24`
+ Network Address（網路位址）
    + 用來表示同一個網路區段，而非指單一主機
    + 計算方式：IP 位址 `AND` 子網路遮罩 = 網路位址

### 應用層通訊協定 Application Layer Protocols

透過通訊埠（Port）能夠區分不同的應用服務，讓一台電腦能同時處理多種網路服務。

常見的 Port：

+ HTTP：HyperText Transfer Protocol 網頁傳輸協定
    + 提供瀏覽網頁
    + 80
    + 加密 HTTPS → 443
+ FTP：File Transfer Protocol 檔案傳輸協定
    + 用於檔案傳輸
    + 20, 21
+ NTP：Network Time Protocol 網路時間協定
    + 用於同步時間
    + 123
+ SMTP：Simple Mail Transfer Protocol 郵件傳送協定
    + 郵件伺服器用來寄信
    + 25
+ POP3：Post Office Protocol v3 郵件接收協定
    + 收信，郵件下載用
    + 110
+ IMAP：Internet Message Access Protocol 郵件接收協定
    + 收信，郵件保留在伺服器上
    + 143
+ MIME：Multipurpose Internet Mail Extensions 多用途網際網路郵件延伸格式
    + 讓郵件或網頁可處理多媒體與附件
    + 加密版本為 S/MIME

### 錯誤控制 Error Control

當網路傳送資料時，若途中發生錯誤，需透過 Error Control 機制進行檢查與修正錯誤。

+ パリティチェック（Parity Check）：同位檢查
    + 單一位元錯誤
    + 在每一組資料（例如 8 位元）後面加上一個檢查位元（Parity Bit），用來判斷資料是否有誤
    + 
+ CRC（Cyclic Redundancy Check）：巡回冗長検査
    + 可檢出多位元錯誤
    + 把原始資料當作一個多項式，用固定的生成多項式進行除法運算，得出的餘數（CRC值）附加到資料後方

### SDN / IPSec / CGI

+ SDN（Software Defined Networking）：軟體定義網路
    + 是一種網路架構，將網路的控制平面與資料平面分離，透過軟體集中控制整個網路的運作
    + OpenFlow 協定：實現 SDN 的一種通訊協定
        + 控制器：負責「路徑決策」功能
        + 網路交換器： 負責「資料轉送」功能
+ IPSec（Internet Protocol Security）
    + 是一套網路安全協定，用來保護 IP 網路通訊的安全性
    + 在「網路層」提供加密、身份驗證和資料完整性檢查
    + 常用於：建立 VPN 連線，讓使用者可以安全地透過網路存取公司內部網路
+ CGI（Common Gateway Interface）
    + 是一種標準介面，讓網頁伺服器可執行外部程式並將結果回傳給瀏覽器
    + 是早期動態網頁的重要基礎

## 資訊安全 Information Security

+ 資訊安全（Information Security）
    + 是指保護數位化資訊免受未經授權的存取、竄改、洩漏等威脅
    + 主要確保機密性、完整性、可用性三大要素
+ 脆弱性（Vulnerability）
    + 指系統或軟體中存在的安全「弱點」或「缺陷」，攻擊者可能利用這些弱點來入侵系統或竊取機密資訊
    + 透過定期的安全更新和修補程式安裝，可修復脆弱性問題
+ 威脅 (Threat)
    + 可能對資安造成損害的危險因子
        + 物理面：火災
        + 人員面：社交工程（Social Engineering ソーシャルエンジアリング）
            + 是一種利用人性弱點和心理操縱來獲取機密資訊或進入系統的攻擊手法
            + 不正三角理論（Fraud Triangle）：由三個要素組成：機會、動機和合理化
        + 技術面：電腦病毒、伺服器攻擊

### 電腦病毒

### 伺服器攻擊

### 加密與數位簽章

網路通訊的三大危險為「竊聽、竄改、偽造」，類型說明和應對方式如下：

+ 竊聽：攻擊者在網路偷偷「監聽」資料傳輸內容
	+ 應對：加密技術（保密性）	
+ 竄改：攻擊者在傳輸途中「修改」資料內容
	+ 應對：數位簽章 / 雜湊（完整性）	
+ 偽造：攻擊者「假冒」合法的使用者或伺服器身分
	+ 應對：身分認證 / 憑證（認證性）

換言之，安全的網路通訊（如 HTTPS）等同於「加密（防竊聽）+ 簽章 / 雜湊（防竄改）+ 憑證驗證（防偽造）」。

+ 加密（Encryption）：把「可讀的資料（明文）」，經過演算法與金鑰處理成「無法讀懂的亂碼（密文）」，藉此保護資料內容。

#### 加密方式可分為兩種：

+ 對稱式加密（共用金鑰加密 = 共通鍵暗号方式）
    + 加密與解密用同一把金鑰，因此傳輸金鑰時要非常小心
    + 管理困難，加解密速度快，適合大量資料加密
    + 範例：**AES**、DES
+ 非對稱式加密（公開金鑰加密 = 公開鍵暗号方式）
    + 使用一對金鑰（公鑰加密、私鑰解密），用來加密的公鑰是公開的
    + 管理簡單，加解密速度慢，適合金鑰交換、驗證、簽章
    + 範例：**RSA**、ECC

而雜湊（ハッシュ，Hash）和加密不同，雜湊化（ハッシュ化）是為了「驗證資料有沒有被改過」，透過數學演算法（SHA-256 等）轉換任意長度的資料，形成固定長度的短字串（雜湊值），這段過程是單向不可逆的，因此是用來比對傳送前後的雜湊值，確認資料的完整度。
 
#### 數位簽章（ディジタル署名）

數位簽章是透過「雜湊化 + 公開金鑰加密法」的方式，防止資料被篡改或被他人冒用。

流程如下：

+ 寄件者用「寄件者的私鑰」對雜湊後的資料產生簽章
+ 收件者用「寄件者的公鑰」驗證簽章是否與雜湊後的資料一致
  + 又因為雜湊後的資料具備不可逆性，即使被公鑰開啟也無法得知內容，只用來比對資料是否一致

若驗證通過，表示：

+ 資料沒被改過（完整性）
+ 是寄件者本人簽的（身分認證）

### 網路安全

* 防火牆（Firewall）：保護內部網路不被外部攻擊或未授權存取的第一道防線
  * 包過濾型（Packet Filtering）：根據 Header 中 「IP、Port、協定」等條件判斷是否放行
  * 部署在網路出入口，監控網路層
* WAF（Web Application Firewall，網站應用防火牆）：專門保護「網站應用層」的防火牆，深入分析「HTTP、HTTPS 請求內容」偵測攻擊行為
  * 部署在 Web伺服器前端，監控應用層
* 代理伺服器（Proxy Server）：介於使用者（Client）與外部網路的伺服器（Server）之間，代替使用者進行網路請求的中介伺服器
  * 優點：快取功能、監控與存取限制、隱藏真實 IP
* 反向代理伺服器（Reverse Proxy Server）：站在「伺服器端」的代理，作用相反，由企業或網站營運者設置
  * 優點：負載平衡、安全防護、快取功能
* DMZ（DeMilitarized Zone，非軍事地帶 = 網路隔離區）：放在「內部網路」與「外部網路（Internet）」之間的安全隔離區，外部能安全存取特定伺服器，並防止外部攻擊直接影響內部網路

#### 利用者認証技術 User Authentication

* 生物辨識（Biometrics）：透過指紋、臉部、虹膜、聲紋等生物特徵驗證
* 一次性密碼（OTP：TOTP / HOTP / SMS）：產生時效性或序列式的一次性密碼
* CAPTCHA / reCAPTCHA（キャプチャ、驗證碼）：防止機器人自動操作的驗證方式

#### 其他資安對策

+ VPN（Virtual Private Network，虛擬私人網路）：在使用者與伺服器之間建立一條「加密通道」，藉此隱藏真實 IP 位址，安全存取網路資料
+ SSL = TLS：透過加密方式，保護實際傳輸的資料
+ Penetration Test（ペネトレーションテスト、滲透測試）：實際模擬駭客攻擊（White Hacker），檢測「系統」安全性
    + 對象：伺服器或網路
+ Fuzzing：（ファジング、模糊測試）：透過輸入隨機或異常資料，自動化測試「軟體」安全性
    + 對象：軟體


#### 資安相關協定

以下分別是沒有 ↔ 有資安對策的協定：

+ HTTP ↔ HTTPS
    + `HTTP + SSL/TLS = HTTPS`：在通訊層加上加密與認證機制
+ IP ↔ IPsec
    + IPsec 是「在 IP 層上增加安全性」的協定，常被 VPN 採用，防止封包被竄改或冒充
+ TELNET ↔ SSH
    + SSH 是現代遠端登入伺服器的標準方式，取代不安全的 TELNET

### 科目 B 對策

> 參考：
> https://youtu.be/wFpyeWto8Og?si=8w6HkYmnjyYX9s0N

## 演算法與程式語言

程式的屬性可分四種：

* 再配置可能 リロケータブル：可搬遷到不同記憶體位置
* 再使用可能 リユーザブル：可重用，共用模組
* 再入可能（リエントラント Reentrant）：可並行或中斷處理，多執行緒
* 再帰可能（リカーシブ Recursive）：可呼叫自己，可遞迴

### 演算法基礎分類

* 資料的保存方式：資料結構，影響搜尋或排序的效率
  * 陣列（Array）
  * 鏈結串列（Linked List）
  * 堆疊（スタック、Stack）：後入先出 LIFO
  * 佇列（キュー、Queue）：先進先出 FIFO
  * 木構造（二分木、Tree）
  * 雜湊表（Hash Table）
* 資料的搜尋方法：效率取決於資料結構與演算法
  * 線性搜尋（Linear Search）
  * 二分搜尋（Binary Search）：升序、降序
  * 雜湊搜尋法（Hash Search）：透過雜湊函數把資料的關鍵字（key），轉換成位址（index），表示資料在表中的存放位置
    * 衝突（シノニム）：當不同的 key 經過 hash 計算得到相同的位置
  * 深度優先搜尋（DFS）
  * 廣度優先搜尋（BFS）
* 資料的排序方法（Sort）：將資料依照某種順序（例如由小到大）排列的演算法
  * 基本交換法 = 泡沫排序（Bubble Sort）：兩兩比較相鄰元素，若順序錯誤就交換，一輪下來最大的元素會「冒泡」到最後
  * 基本插入法 = 插入排序（Insertion Sort）：把每個元素插入已排序部分，保持順序
  * 基本選擇法 = 選擇排序（Selection Sort）：每次從未排序的部分找最大/小值，放到前面
  * シェルソート（Shell Sort）：插入排序的改良版，先比較間隔較大的元素，逐步縮小間隔
  * 快速排序（Quick Sort）：選一個基準（Pivot），把比基準小的放左邊，比基準大的放右邊，然後對左右子陣列遞迴排序
  * ヒープソート（Heap Sort，堆排序）：把資料建成最大堆或最小堆，每次取出最大（或最小）放到已排序部分
  * 合併排序（Merge Sort）：把資料分成兩半遞迴排序，最後再合併成有序陣列

### 大 O 符號（Big O）

> 【註記】
> A 和 B 幾乎必出的範圍，硬記文字其實幫助不大，建議搭配圖形幫助記憶，理解什麼情境適用排序法。

+ 時間複雜度：用來衡量演算法執行好壞
+ O(n)：用來描述演算法的時間複雜度，根據「輸入資料量(n)」與「總執行時間」的關係來作為衡量的標準
+ 忽略常數和低次項：重點不是精確的數字，而是看增長的「趨勢」

基本概念與對應的排序法：

* O(1) → 常數時間：不管資料有多大，執行時間幾乎不變。
    * 例：**Hash 法**
* O(n) → 線性時間：執行時間隨輸入數量成正比增加
    * 例：**線性搜尋法**
* O(log n) → 對數時間：資料翻倍，時間只增加一點點
    * 例：**二分搜尋法**
* O(n²) → 二次時間：常見於巢狀迴圈
    * 例：**基本交換法（Bubble Sort）、基本選擇法（Selection Sort）、基本插入法（Insertion Sort）**
* O(n log n) → 線性對數時間：常見於高效排序演算法
    * 例：**合併排序（Merge Sort）**、**快速排序（Quick Sort）**

### 物件導向

物件導向（オブジェクト指向 / Object-Oriented）的基本概念可分以下幾點：具體化、抽象化、類別、繼承 封裝、多型。

+ 具體化（インスタンス化 / Instantiation）
    + 定義：由「類別（Class）」產生出實際可使用的「物件（Object）」的過程
    + 範例：

```java
class Dog { ... }
Dog dog1 = new Dog(); // 產生一隻具體的狗（物件）
```

+ 抽象化（Abstraction）
    + 定義：將複雜的現象或資料中共通的重要特徵提取出來，忽略不必要細節，形成「類別」或「模型」
    + 範例：不需描述每隻狗的毛色，只需知道「狗會叫、會跑」的共通特徵
+ 類別（クラス / Class）
    + 定義：定義物件的屬性（Attribute）與行為（Method）的模板或藍圖
    + 範例：`Dog` 類別中包含 `name` 屬性和 `bark()` 方法
+ 繼承（継承 / Inheritance）
	+ 定義：新的類別（子類別）可以繼承既有類別（父類別）的屬性與方法，實現重用與擴充
	+ 範例：`Dog` 繼承 `Animal`，可直接使用 `Animal` 的方法如 `eat()`
+ 封裝（カプセル化 / Encapsulation）
	+ 定義：將資料（屬性）與行為（方法）包裝在一起，限制外部直接存取，只能透過介面（Getter/Setter）操作
	+ 優點：維護性高，避免外部誤用
+ 多型（ポリモーフィズム / Polymorphism）
	+ 定義：同一介面或方法名稱，根據實際物件類型可表現不同的行為
    + 範例：

```java
Animal.speak();
// Dog → 「汪」，Cat → 「喵」
```

### HTML / CSS / UML

* UML（Unified Modeling Language）：統一建模語言
    * 用來「視覺化、設計與描述系統」的標準圖示語言，表現系統的結構與流程

常見的 UML 圖（圖形要會分！），依照目的可分以下四種：

* クラス図（Class Diagram）：定義類別、表現**類別之間**的關係
    * 例：顧客類別、訂單類別（靜態結構）
* シーケンス図（Sequence Diagram）：物件之間的訊息傳遞順序
    * 例：顧客→系統→倉庫（動態流程）
* アクティビティ図（Activity Diagram）：表示**處理流程**與條件分支
    * 例：購物流程（控制流程）
* ユースケース図（Use Case Diagram）：使用者（行為者 Acter）與系統的互動
    * 例：顧客登入、下單（功能需求）

## 系統構成

## 系統開發

### 系統開發流程

### 系統開發方式

### 業務模型

+ 業務模型（商業模式）：描述企業或組織如何進行業務活動、創造價值的模型
    + E-R 図（Entity-Relationship 図）：實體關係圖
        + 資料庫設計，表示「資料之間的關係」
        + Entity（實體）, Attribute（屬性）, Relationship（關係）
    + DFD（Data Flow Diagram）：資料流程圖
        + 系統分析，表示「資料流動」
        + Process（資料處理 `O`）, Data Flow（資料的流動線 `→`）, Data Store（資料儲存處 `=`），資料起點與終點（`口`）
    + UML（Unified Modeling Language）：統一建模語言
        + 系統設計，描述「軟體結構與行為」
        + Use Case, Class, Sequence, Activity

### User Interface（UI）

+ User Interface（使用者介面）：人與電腦互動的「接觸面」，表示使用者操作系統或應用程式的方式
    + GUI（Graphical User Interface）：圖形化使用者介面
        + 透過「圖形（Icon、Button、Menu）」與電腦互動
            + ラジオボタン（Radio Button）：單選框
            + チェックボックス（Check Box）：複選框
    + CUI（Character User Interface）：文字式使用者介面
        + 透過「文字指令（Command）」與電腦互動
+ Interface 設計（介面設計）：設計「系統之間」或「人與系統之間」資訊交換的方式
    + 畫面設計：設計使用者實際看到的「畫面」構成與操作流程
    + 帳票設計：設計系統輸出的「報表、單據、清單」等格式

### Module 化

+ Module 化（モジュール、模組分割）：以「模組」為單位進行開發測試
    + 優點：適合多人作業、共通功能可重用、利於除錯
    + 模組的獨立性：取決於「模組結合度」與「模組強度」兩指標
        + 模組強度（モジュール強度、凝集度）：模組各功能之間，彼此關聯性有多強
    + 模組結合度（モジュール結合度）：模組之間互相依賴的強度
    + 高凝集（內聚）＋低結合（耦合）= 高獨立性
    
### 測試方式

## 管理相關

## 戰略相關

### ソリューションビジネス

* ソリューションビジネス（Solution Business、解決方案型業務）：根據客戶的需求或問題，提供能夠整體解決的系統、服務與支援
    * オンプレミス（On-premises）：企業自行在公司內部設置伺服器與網路設備，並自行管理維護
        * 従来型（傳統型）：企業內部資料庫、內部郵件伺服器、ERP 系統
        * ハウジング（Housing）：企業擁有自家伺服器
        * ホスティング（Hosting）：租用網頁空間（Web Hosting）、租用 VPS（虛擬伺服器）
    * クラウド（Cloud）：由雲端服務提供商（如 AWS、Azure、Google Cloud）在網路上提供伺服器、儲存空間、應用程式等資源
        * SaaS(Software as a Service)：軟體即服務，直接透過網路使用「應用程式」，無須自行安裝或維護
        * PaaS(Platform as a Service)：平台即服務，提供應用程式開發的平台環境（包含系統、資料庫、開發工具等）
        * IaaS(Infrastructure as a Service)：基礎設施即服務，提供虛擬伺服器、儲存空間與網路資源等基礎硬體

### SOA

* Service Oriented Architecture：服務導向架構，將整個系統拆解成許多可重複使用的「服務（Service）」
* 情報リテラシー（Information Literacy）：資訊素養、資訊運用能力
* デジタルデバイド（digital divide）：數位落差
* BYOD（Bring Your Own Device，自帶設備）：指「員工使用自己的電腦、平板或手機來處理公司業務」的制度
* RPA（Robotic Process Automation／機器人流程自動化）：利用軟體「機器人（Robot）」自動執行人類在電腦上重複進行的作業流程
* BI（Business Intelligence，商業智慧）：透過資料分析來支援企業決策

### EC

* EC（電子商務 / Electronic Commerce）：透過網際網路（Internet）進行商品或服務的買賣、付款與物流處理 的商業活動
    * 根據交易方式分四種身分：
        * 消費者 Consumer
        * 企業　Business
        * 員工　Employee
        * 政府 Government
     * 主要交易方式：
         * BtoC（Business to Consumer）：企業 → 消費者
             * Amazon、樂天市場、Yahoo!購物
         * BtoB（Business to Business）：企業 → 企業
             * 零件採購網站、企業間訂單系統
         * CtoC（Consumer to Consumer）：消費者 ↔ 消費者
             * Mercari（メルカリ）、Yahoo!拍賣
         * GtoB（Government to Business）：政府（Government）對企業（Business）之間進行的電子化服務與交易
             * 稅務申報（e-Tax）
* EDI（電子資料交換 / Electronic Data Interchange）：讓企業之間可以用電子方式、自動化地交換商業文件資料的系統
* 暗号資産（あんごうしさん / Cryptocurrency / 加密資產）：以「區塊鏈（Blockchain）」技術為基礎，交易記錄公開且不可竄改
    * マイニング（Mining / 挖礦）：參與者利用電腦運算資源，驗證加密貨幣的交易，並因此獲得報酬

### Webマーケティング（網路行銷）

* SEO（Search Engine Optimization）：搜尋引擎最佳化
* アフィリエイト（Affiliate）：聯盟行銷
* SNS（Social Networking Service）：社群媒體行銷
* CGM（Consumer Generated Media）：使用者產生內容
* オムニチャネル（Omni-Channel / 全通路整合）：指企業將線上（Online）與線下（Offline）銷售管道整合起來，讓顧客可以「不論從哪個通路」都能獲得一致、順暢的購物體驗
* ロングテール（Long Tail）：長尾理論，與其只依賴暢銷商品，透過銷售多樣化、少量銷售的冷門商品，加總起來反而能獲得更多利潤的理論


### 法規相關

+ 標準化
    + ISO 國際標準化機構
    + JIS 日本標準化規格
    + IEEE 美國
    + IETF 網路標準化機構，RFC
+ 勞動者派遣契約
    + 派遣元企業～勞動者：僱用關係
    + 派遣先企業～勞動者：指揮命令關係
    + 派遣元企業～派遣先企業：勞動者派遣契約


