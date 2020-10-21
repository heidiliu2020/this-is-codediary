###### tags: `Front-End` `HTML & CSS`
# [week 13] 前端進階 - 如何優化執行 CSS 效能

## 前端優化

關於前端優化方式，可從下列幾個方向進行：

- 資源本身的大小
- 資源載入的方式
- 資源執行的方式

## CSS 優化

### 節省資源大小

- Minify：省略空白字元，電腦可直接辨識
  - 例如：`nps sass --style=compressed main.sass masin.css`
- gzip*：壓縮，會進行編碼，通常是在 server 端進行，電腦會經過解碼後再使用

通常會先進行 Minify 再進行 gzip*，藉此壓縮資源大小。

### 載入方式

- Critical CSS：分批載入，先載入重要的部分
  - 例如：把 navbar、banner 樣式放到 index.html，在讀取 css 之前即可先被載入

![](https://i.imgur.com/sywJHI7.png)

- CSS Sprites：將每個小檔案打包後，只需發出一個 Request  ，載入完成後再拆開引用
  - 例如：將小圖片打包成大圖片，再取用需要的區塊

![](https://i.imgur.com/XV6V5ak.png)

- Cache*：將資料暫存在瀏覽器，只有第一次登入網站需下載，之後就能快速讀取暫存檔

![](https://i.imgur.com/8n3U6Qj.png)

### 執行方式

- 選擇器：降低選擇器的複雜性
  - 例如：使用 Sass 巢狀語法時，需考慮到編譯後程式碼的複雜性

- 屬性渲染：需考慮到屬性載入的順序
  - 例如：特效網頁或有 SEO 考量的網站，需注意動畫或特效會在哪個階段匯入

---

## CSS Sprites 與 Data URI 的優缺點

### CSS Sprites

CSS Sprite 是將頁面中所使用到的小圖片整合到一張大圖上。也就是說，在加載一個包含多個圖片的頁面上時，只需使用一個HTTP請求而不需要 JavaScript 代碼。

- 優點：減少 http 請求
- 缺點：圖片合併定位費時費力

#### 使用技巧
- 切圖前就需構思拼接好圖片
- 排序有序，便於後期維護。有利於 background-position 定位
- 定位時避免使用 right, bottom 等
- 合理預留空白位置（空太多檔案變大，太小引起圖示重疊）

### Data URI

image data URI 是將圖片資源轉換為 base64 字串格式嵌到頁面或樣式中。不需要圖片的請求連結。

- 優點：
  - 減少 HTTP 請求
  - 避免某些檔案跨域
  - 無圖片快取等問題
- 缺點：
  - 相容性（IE6,7 不相容，可使用 MHTML 來解決）
  - 瀏覽器不會快取該圖片
  - 增加 css 檔案大小
  - 編碼成本及維護

#### 使用技巧

- data URI 可使用在：
  - 圖片尺寸很小，使用一條 http 請求有點浪費，如：漸變背景框
  - 圖片在全站大規模使用，且很少被更新的，如：loading 圖
- 標籤語法
  - data：取得資料協議
  - image/png：取得資料的協議名稱（注意這裡也圖片資源也可以使用字型等）
  - base64：資料編碼方式
  - iVBOR…：編碼後資料

參考資料：
- [主流CSS image比較](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/184003/#outline__1)
- [CSS Sprites vs. Data URIs:在移动端，谁更快？](https://www.oschina.net/translate/css-sprites-vs-data-uris-which-is-faster-on-mobile?print)
