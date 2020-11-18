###### tags: `Lidemy` `產品開發`
# [week 19] 淺談產品開發與工作流程

> 本篇為 [[PD101] 淺談產品開發與工作流程](https://lidemy.com/p/pd101) 這門課程的學習筆記。如有錯誤歡迎指正！

透過這堂課程，我們能夠在進入職場之前，先試著瞭解開發流程，雖然每間公司實際工作情形不一定相同，但也能先有個初步概念：

![](https://i.imgur.com/PPlaGG2.png)

課程內容也會以之前 Huli 和助教共同實作的 Lidemy 學習系統作為範例。

---

## 在實際開發之前

工程師會根據 PM 提出的產品需求來進行開發，而一個產品需求產生的流程如下：

![](https://i.imgur.com/EHWat4x.png)

- stakeholder 利益相關者，也就是客戶
- Product Requirement Document 產品需求文件
- Product Specifications 產品規格書
- wireframe 設計線稿
- mockup 模型

接著工程師會透過 spec 瞭解產品需求，根據 mockup 寫出相對應的產品介面與功能。

### Product spec 裡面有哪些東西？

實際開發中，寫得越詳細的產品規格書，能夠幫助開發內容與規格達成一致；假如 PM 沒有將需求寫在規格書中，開發產品也需同步進行，避免程式行為與規格不一致，導致後續維護問題。

一般而言，產品規格書屬於公司機密不能對外公開，這裡舉一些網路上公開的範例。

#### 範例一：[PRD到底该怎么写？更全面的文档范例来了](http://www.woshipm.com/pmd/3327770.html)

- 修訂紀錄
- 全局說明
- 項目背景
  - 透過現狀、方案、目標來描述
- 項目範圍
  - 項目對應搭框架
  - 產品結構圖
- 開發流程
- 功能需求
  - 對功能逐一描述
  - 透過使用者 + 功能方式來描述
  - 配置條件、介面元素、產品邏輯、異常與分支流程
  - 數據字典
- 非功能需求
  - 數據、監控、性能需求

#### 範例二：[【SOP不藏私】系列＃EP1「連猴子也會的PRD指南」](https://medium.com/as-a-product-designer/sop-ep01-prd-3c6d33880c34)

這個例子會比較偏向於介紹產品如何開發以及開發時程，說明產品規格書的文件架構：

- 文件概況
  - 文件修訂紀錄
- 產品簡介與功能對照
  - 產品簡介
  - 產品目標說明
  - 「工作排程、使用者故事、對應功能、進版時間」對照表
- 產品架構與流程
  - 產品功能架構圖（Function Map）
  - 產品信息架構圖（Information Architecture）
  - 功能邏輯圖（Flow Chart）
- 產品設計原型
  - 產品全局簡介
  - 局部功能 Prototype，有互動的部分
- 產品指標
- 字串表
  - 用來顯示多國語言

延伸閱讀：

- [【產品經理 PM｜需求文檔 PRD】優惠券發放的產品設計，需求文檔怎麼寫？](https://medium.com/y-pointer/product-prd-ca0ea9b75b85)

### User Story 使用者故事

User Story（使用者故事）是一段簡單的功能敘述，藉由客戶或使用者的觀點寫下有價值的功能（functionality）。除了能更明確化產品需求，工程師也能夠知道客戶會做哪些驗收測試、期望跟假設。

在 User Story 中，對於一個需求的寫法大致如下：
- As a user... 身為一個使用者..
- I want to... 我希望...
- so that I can... 我就可以...

以開發 Lidemy 作業系統為例子，這是初步的產品需求：

![](https://i.imgur.com/5Ho0Vcl.png)

若以 User Story 方式撰寫，就能夠使產品需求更加明確化，也能瞭解不同使用者身份的需求以及開發順序：

![](https://i.imgur.com/Fewp6BB.png)

此外還有像是 User Flow 使用流程和 Wireframe 設計線稿，也能幫助理解產品邏輯：

- User Flow

![](https://i.imgur.com/YH4yf2T.png)

- Wireframe

![](https://i.imgur.com/xuiuBsv.png)

## Card = Ticket = Task = Issue

在實際開發過程，通常會把每個功能切割成一個任務，以下這些名詞均可代表工作的最小單位：
- Task：任務
- Issue：GitHub 上的一個問題或 Bug
- Card：卡片
- Ticket：票

通常會利用現成的平台，像是 [Jira](https://www.atlassian.com/software/jira?&aceid=&adposition=&adgroup=95003645449&campaign=9124878702&creative=415542752365&device=c&keyword=jira&matchtype=e&network=g&placement=&ds_kids=p51242189318&ds_e=GOOGLE&ds_eid=700000001558501&ds_e1=GOOGLE&gclid=Cj0KCQiAqdP9BRDVARIsAGSZ8AmzbfXvaxlwsl-90CIIUAkOsgLAcBIQqvuBIBX6R1pusz1JtPF-YN0aAmAzEALw_wcB&gclsrc=aw.ds) 或 [Trello](https://www.googleadservices.com/pagead/aclk?sa=L&ai=DChcSEwjcs9ex14vtAhUJ2JYKHUcCDpAYABAAGgJ0bA&ohost=www.google.com&cid=CAESQeD2WFtLVx7m4sxyAETq0QhFpv4aoGVXXC4BVir7uZg3w-13OYYSOAe1JqEjAJlVp3D8sJb-CC2_cVhsWy4YuOVX&sig=AOD64_0u5335M7RN9R-81x8vFUm_0Adr3A&q&adurl&ved=2ahUKEwjgjtGx14vtAhUHwosBHfAjCF0Q0Qx6BAgMEAE) 來管理任務，通常一個 User Story 就會切割成一張票：

![](https://i.imgur.com/l4GdKJq.png)

透過這些平台，能夠用來切割工作區塊，也能讓整個團隊快速瀏覽整體工作進度。

參考資料：

- [什麼是 User Story？](https://ihower.tw/blog/archives/2090)
- [Agile 使用者故事 ](https://www.youtube.com/watch?v=apOvF9NVguA)
- [拆解使用者故事 ](https://www.youtube.com/watch?v=EDT0HMtDwYI)

---

## 軟體開發方法論 Methodology

在實際軟體開發過程，並不是拿了一張卡就開始工作那麼簡單，而是必須考慮透過哪種模式或框架來進行開發，大致可分成幾種方法：

- Waterfall 瀑布流
- Agile 敏捷

### Waterfall 瀑布流

![](https://i.imgur.com/XRtzTAx.png)

- 從上往下，不能往回，一次性
- 呈現完整性較高
- 事前規劃需做完善，若過程中需要增加新功能獲改善，就需要再等下一輪流程
- 較不適合大型專案

參考資料：
- [What's the Difference? Agile vs Scrum vs Waterfall vs Kanban](https://www.smartsheet.com/agile-vs-scrum-vs-waterfall-vs-kanban)
- [【Podcast EP03】敏捷或瀑布開發哪個好？流程用哪種重要嗎？](https://medium.com/3pm-lab/pm-podcast-ep03-waterfall-vs-agile-82b214853112)

### Agile 敏捷

![](https://i.imgur.com/Rp9dRyX.png)

- 及早並持續地交付，精簡化
- 彈性較高，能夠改變需求
- 類似於把 Waterfall 的任務切小細分
- 按照 12 項原則就能夠稱為敏捷，又可細分為不同框架來進行

參考資料：

- [【文思不藏私】@敏捷宣言 12 原則](https://medium.com/%E6%96%87%E6%80%9D%E4%B8%8D%E8%97%8F%E7%A7%81/%E6%96%87%E6%80%9D%E4%B8%8D%E8%97%8F%E7%A7%81-%E6%95%8F%E6%8D%B7%E5%AE%A3%E8%A8%80-12-%E5%8E%9F%E5%89%87-64ad7d592087)
- [什麼是敏捷?](https://www.youtube.com/watch?v=Z9QbYZh1YXY)
- [【敏捷系列 - 1】什麼是敏捷？敏捷實例分享](https://www.youtube.com/watch?v=HDmO7Ev7Mlc)

### 實作 Agile 概念：Kanban 看板

這裡以 Trello 和 Jira 平台為例：

![](https://i.imgur.com/dvYXPf0.png)

- Trello

![](https://i.imgur.com/5dSZWIc.png)

- Jira 平台的 Kanban Board

![](https://i.imgur.com/6oPUTVR.png)

![](https://i.imgur.com/a9XYtOX.png)


### 實作 Agile 概念：Scrum

![](https://i.imgur.com/Xs7PQ5q.png)

透過 Scrum 的開發流程：

- Product Backlog：列出產品所有功能
- Sprint Planning：預估所需時間，分配任務
- Sprint Backlog：這個週期要開發的功能
- Sprint 開發週期：2-4 weeks 循環
- Daily Scrum：例行溝通進度
- Review、Retrospective：檢視進度與檢討

![](https://i.imgur.com/qMvYoim.png)

在實際開發扮演的角色：

- Product Owner：決定產品走向
- Scrum Master：幫助團隊跑 Scrum 流程
- Team：實際開發的工程師

參考資料：

- [Introduction to Scrum - 7 Minutes](https://www.youtube.com/watch?v=9TycLR0TqFA)
- [【敏捷系列 - 3】Scrum中的短衝 (Sprint)](https://www.youtube.com/watch?v=CQp0nGY4noo)

---

## 工程師的兩週生活 & Scrum

這裡以工程師的生活作為範例，通常以兩個禮拜為一個開發週期，流程大致如下：

- Sprint Planning
  - 預估任務時間 Story Point
  - 分配任務
- 開發與 Daily Standup
  - Daily Standup：報告昨天進度、今天預計進度、提出遇到的困難，藉此同步團隊資訊
- Deploy 部署
  - 例如隔週會部署程式到測試環境
- Sprint Demo
  - PM 藉由 Demo 讓客戶瞭解目前進度
- Sprint retrospective 檢討會議
  - Went well 做得不錯的點
  - To improve 要加強的點
  - Action items 如何改善

參考資料：

- [協助做 retro 的工具](https://reetro.io/)

## 關於部署

實際部署程式可分成幾種環境，藉由不同環境，能夠在過程中進行測試，確認功能都沒問題後再進行公開：

- Local 環境
  - 本機端開發，通常會接 Dev 的資料
- Development 環境（Dev）
  - 和 Local 的差別：一個在自己電腦測試，另一個則是部署到 Server 環境
  - 例如：[Netlify](https://www.netlify.com/)
- Staging 環境 / QA 環境
  - 和 Production 類似，只是用來測試，不對外公開的環境
- Production 環境
  - 所有使用者都看得到的環境

## 關於測試

這邊提到的測試，和我們之前學到的 Unit Test 不同，是工程師針對程式碼進行的測試；但 QA 要測的只是呈現功能是否正常，簡單介紹下列兩種測試方式：

- SIT（System Integration Testing）：系統整合測試，確認功能是否正常，可能透過手動或寫自動化程式來測試
- UAT（User Acceptance Testing）：使用者可用性測試，通常是 PM 會進行測試，確認功能是否符合預期

---

## 結語

最後整理產品開發流程的幾個重點：

1. 由 PM 提出產品需求給工程師
    - spec 產品規格書：需要哪些功能
    - wireframe 設計草圖：用來確認網頁架構
    - mockup：由設計師根據產品規格做出模型
    - 透過 User Flow、User Story，更明確寫出針對不同使用者、需要哪些功能、以及優先順序（例如：身為一個使用者/管理員，我希望可以XXX）
2. 實作 Agile 開發
    - 在實際開發之前，可先在 Kanban（看板）列出產品所有功能，並以功能切割任務、預估所需時間
    - 例如：Trello、Jira 等平台
3. 實際開發流程
    - 以 Sprint 開發週期為例，通常以 2-4 weeks 為週期，根據分配的任務進行開發，最後檢視進度並檢討如何在下個週期改善
    - 可將各個產品功能加上編號，在 commit 時也加上相對編號，之後就能快速找到當初討論的 requirement

延伸閱讀：

- [做產品真是哭夭難！ — Marty Cagan 演講 70 分鐘中文逐字翻譯（附贈 YouTube 錄影）](https://medium.com/3pm-lab/marty-cagan-producttank-taipei-speech-933e7dfc13af)
