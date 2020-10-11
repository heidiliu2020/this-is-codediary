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
