[![hackmd-github-sync-badge](https://hackmd.io/EwH1vtFiRQK4D1xR7FGYBA/badge)](https://hackmd.io/EwH1vtFiRQK4D1xR7FGYBA)
###### tags: `Google` `AdSense` `Advertising` 
# Google 廣告｜如何使用 Google AdSense 設定廣告

[toc]

## 前言

在上一篇文章  [Google 廣告｜比較 Google Ads、AdSense、AdMob 和 Ad Manager 的區別](https://hackmd.io/@Heidi-Liu/google-advertising-service) 中，介紹幾種與 Google 廣告相關的服務。

接下來，將會介紹如何使用 Google AdSense，從申請帳號資格到實際設定廣告投放。

## 如何使用 Google AdSense

以下是官方 AdSense 頻道的影片介紹：

<iframe width="694" height="520" src="https://www.youtube.com/embed/YSqOvgfbPhI" title="Welcome to Google AdSense" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### 申請資格

若想參加 AdSense 計畫，需要註冊 AdSense 帳戶，在這之前，必須確認是否符合 [AdSense 資格規定](https://support.google.com/adsense/answer/9724?hl=zh-Hant&ref_topic=1319756&sjid=2627952085785731458-AP)：

- 網站提供獨創內容
- 內容需符合[計畫政策](https://support.google.com/adsense/answer/48182)規定
- 需為年滿 18 歲成年人，若未滿 18 歲則可由父母或監護人代為註冊

### 申請流程

- Step1. 進入 [Google AdSense](https://www.google.com.tw/adsense/start/) 官網，點擊開始使用

![](https://hackmd.io/_uploads/S15Wc6Jfa.png)


- Step2. 填寫網站、收款國家等資訊
    - [AdSense 的網站管理異動 2023/2/20](https://support.google.com/adsense/answer/12170421?hl=zh-Hant&sjid=13509698644754879393-AP)：需注意輸入的網站網址，限制「必須是不含路徑、參數、片段或通訊埠號碼的標準網域」，因此不支援路徑（path）或子網域（subdomain），詳細可[參考官方文件](https://support.google.com/adsense/answer/2784438?hl=zh-Hant&sjid=13509698644754879393-AP#zippy=%2C%E7%84%A1%E6%95%88%E7%B6%B2%E5%9D%80%E7%A4%BA%E4%BE%8B%2C%E6%9C%89%E6%95%88%E7%B6%B2%E5%9D%80%E7%A4%BA%E4%BE%8B)
        - 有效範例：`example.com`、`example.blogspot.com`
        - 無效範例：`example.com/page.html`、`example.com/directory`、`subdomain.example.com`

![](https://hackmd.io/_uploads/rJ_X5akG6.png)


- Step3. 點選最左側的「填寫付款資訊」

![](https://hackmd.io/_uploads/H1tLH6eza.png)

- Step4. 接著點選最右側的「將網站連結到 Adsense」會出現以下頁面，可選擇驗證方法並更新至自己的網站上，方法共有三種：
    - (1) 在網頁的 HTML `<head></head>` 區塊內嵌 AdSense 程式碼片段
    - (2) 新增 Ads.txt 檔案並貼上相關程式碼片段
    - (3) 在網頁的 HTML `<head></head>` 區塊之間插入 `<meta>` 中繼標記

![](https://hackmd.io/_uploads/B1cwHagMp.png)

- Step5. 完成後打勾並點選驗證，約需經過 2 至 4 週的時間審查

## 廣告類型與設定

在啟用 AdSense 帳戶後，即可在網站上設定廣告，廣告類型可分為「[自動廣告](https://support.google.com/adsense/answer/7037624?hl=zh-Hant&ref_topic=1250102&sjid=2627952085785731458-AP#auto_ads)」和「[廣告單元](https://support.google.com/adsense/answer/7037624?hl=zh-Hant&ref_topic=1250102&sjid=2627952085785731458-AP#ad_units)」兩種方式。

### 自動廣告 Auto ads

在網站導入自動廣告（Auto ads）時，只需將程式碼放到想要顯示廣告的頁面中，系統就會自動掃描網站，並根據版面配置與內容，找出位置自動刊登廣告。

![](https://hackmd.io/_uploads/rkmKS6eMa.png)

點擊上圖頁面中的「取得程式碼」，並將程式碼放到網頁的 **`<head></head>`** 標籤中即可。

![](https://hackmd.io/_uploads/Skd5STgMT.png)

程式碼示意如下：

```htmlmixed=
<html>
	<head>
		// ...
		<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXX"
	     crossorigin="anonymous"></script>
	</head>

	<body>
		// ...	
	</body>
</html>
```

點選右下角的「鉛筆符號」進入自動廣告的設定頁面，在這裡可以預覽選擇的廣告格式，以及希望排除的區域或頁面等：

![](https://hackmd.io/_uploads/rkagLTxG6.png)

### 廣告單元 Ads units

另一種[廣告單元](https://support.google.com/adsense/answer/9183549?hl=zh-Hant&sjid=9545392955302552591-AP&visit_id=638305976341337300-3394897910&ref_topic=9183242&rd=1)（Ad units），是指透過 AdSense 廣告程式碼，根據設定的類別與樣式，顯示一至多個 Google 廣告。

可在 AdSense 帳戶的「按廣告單元」頁面建立、自訂及管理廣告單元；不同於自動廣告，根據廣告單元的程式碼，能夠控制廣告的顯示位置，而原生廣告單元則在自訂方面更提供更大的彈性。

![](https://hackmd.io/_uploads/BJRWLpezT.png)

廣告單元類型分成以下幾種，可根據不同需求選用：

- [多媒體廣告](https://support.google.com/adsense/topic/9183360)
    - 適用於任何情境
    - 預設為回應式廣告，因此會隨網頁版面和使用者裝置自動調整大小
    - 可自訂為只顯示大小固定的廣告
    - 與 [AMP 網頁](https://support.google.com/adsense/answer/9187239)相容
- [動態內廣告](https://support.google.com/adsense/topic/9183582)
    - 在動態饋給 (例如文章或產品的清單) 中放送與內容自然呼應的原生廣告，提供良好使用者體驗
- [文章內廣告](https://support.google.com/adsense/topic/9183594)
    - 讓廣告自然融入網頁段落的原生廣告，可有效提升閱讀體驗
- [Multiplex 廣告](https://support.google.com/adsense/topic/9185053)
    - 用來顯示內容建議式原生廣告的格狀廣告單元
- [搜尋引擎](https://support.google.com/adsense/topic/10033196)
    - 由 Google 技術提供的搜尋引擎，可在搜尋結果中顯示廣告

點選想要建立的廣告單元會進入設定頁面，需輸入名稱以及選擇廣告大小：

![](https://hackmd.io/_uploads/H1lQUaxGp.png)

建立完成廣告單元後，將廣告單元程式碼放到網頁的 **`<body></body>`** 標籤中，希望顯示廣告的區塊即可：

![](https://hackmd.io/_uploads/HyTXLTlMT.png)

根據[在 HTML 中安插廣告程式碼的位置（廣告單元）](https://support.google.com/adsense/answer/9190028?hl=zh-Hant)，可透過程式碼來調整廣告單元顯示的位置與樣式：

```htmlmixed=
<body>
	<!-- Ad units container -->
	<div align="center">

		<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXX"
		     crossorigin="anonymous"></script>
		<!-- Responsive_ads -->
		<ins class="adsbygoogle"
		     style="display:block"
		     data-ad-client="ca-pub-4274798044223706"
		     data-ad-slot="3472959732"
		     data-ad-format="auto"
		     data-full-width-responsive="true"></ins>
		<script>
		     (adsbygoogle = window.adsbygoogle || []).push({});
		</script>

	</div>
  
  // ...

</body>
```

### 在本地端測試 AdSense

此外，若想要在本地端（Localhost）測試廣告，可加上 `data-ad-test="on"` 屬性：

```htmlmixed=
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXX"
     crossorigin="anonymous"></script>
<!-- ad_0113 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-4274798044223706"
     data-ad-slot="7260167051"
     data-ad-format="auto"
		 data-ad-test="on"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
```

### 設定 Domain name 對應 IP

接下來，透過調整設定檔 `\etc\hosts`，手動設定網址（Domain name）對應的 IP 位置，即可方便在本地端進行測試。以下是 Mac 修改 hosts 的方式：

- Step1. 開啟資量夾，點選上方「前往」>「前往資料夾」
- Step2. 輸入 `/private/etc/hosts` 並點擊前往
- Step3. 由於無法直接進行編輯，可透過 sudo 指令來變更權限，或是把檔案拉到桌面，編輯完成再拉回 `/private/etc` 取代原本的檔案
- Step4. 輸入 `IP domain_name`，範例如下所示：

```bash
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	local.ugamenow.com
127.0.0.1	localhost

127.0.0.1 heidiliu2020.github.io
```

> 有關 Mac 設定詳細可參考：[[教學]Mac OS X也能編輯與修改Hosts檔案方法](https://mrmad.com.tw/mac-os-x-hosts)
Windows 和 Linux 如何進行設定，則可參考：[手動設定網址對應 IP 的方式 ( 主機 IP 域名對應檔 hosts )](https://blog.miniasp.com/post/2008/08/25/Modify-hosts-file-to-change-Domain-Name-IP-Mapping)
> 

## 結語

透過這篇文章，認識到如何申請 Google AdSense 帳號，以及提供刊登的廣告類型，設定上算是較容易上手。此外，也能夠先透過修改 `\etc\hosts` 檔案，在本地端進行測試，瞭解廣告實際在頁面中呈現的樣式並即時修正。

## 參考資料

- [AdSense for video overview - Google AdSense Help](https://support.google.com/adsense/answer/1705822?hl=en&ref_topic=1706004&sjid=1883497777466749318-AP)
