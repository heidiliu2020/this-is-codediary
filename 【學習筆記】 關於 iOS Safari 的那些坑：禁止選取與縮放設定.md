###### tags: `Front-End` `Safari`
# 【學習筆記】 關於 iOS Safari 的那些坑：禁止選取 & 縮放設定

![](https://i.imgur.com/BjhUbsU.png)

這次專案開發是針對 iOS 系統，需要解決畫面縮放的問題，有些關鍵字來回搜尋好多遍，卻還是容易搞混，於是乎乾脆記錄下來，日後如果遇到這類型問題，也會直接更新在這篇。

## A. 前言：進入 meta 元宇宙

[meta tag](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta/name) 是網頁 HTML 架構中的一種描述標籤，提供網頁的內容資訊給瀏覽器或搜尋引擎，語法如下：

```htmlmixed=
<meta name="參數" content="具體參數值">
```

在 android 系統與 iOS 10 以前的行動裝置，透過設定 HTML meta tag 的 viewport（可視區域），我們能控制畫面顯示的寬高、縮放比例以及是否允許縮放。

### A-0. `user-scalable=no`：禁止縮放畫面

一般而言，在行動裝置的瀏覽器中，使用者可透過以下手勢來達到縮放頁面（zoom-in/zoom-out）的效果：

- **pinch**：兩指縮放
- **double-tap**：雙擊縮放

若在網頁的 `<head>` 區塊加入以下屬性，即可達到「禁止使用者縮放畫面」的效果：

```htmlmixed=
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">

// 等同於
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1">
```

上述 content 屬性說明：

- `width=device-width`：設定瀏覽器頁面的寬度同裝置的寬度，即自動符合不同手機螢幕預設的最佳解析度
- `initial-scale=1.0`：設定畫面初始縮放比例為 100%，即不放大也不縮小
- `minimum-scale=1, maximum-scale=1`：設定畫面最小和最大的縮放比例，均為 1 代表無法縮放
- `user-scalable=0`：不允許使用者改變縮放比例

但到 iOS 10 之後的版本，為了提高 Safari 網頁的輔助功能，便忽略 meta 設定中的 `user-scalable=no` 屬性，使得「不允許使用者改變縮放比例」這項限制失效，以下是[官網原文](https://developer.apple.com/library/archive/releasenotes/General/WhatsNewInSafari/Articles/Safari_10_0.html)：

> To improve accessibility on websites in Safari, users can now pinch-to-zoom even when a website sets user-scalable=no in the viewport.
>
> 為了提高 Safari 網站中的輔助功能，即使在網站中的 viewport 設定 user-scalable=no，用戶仍可用手指進行縮放。

查了資料發現 meta viewport 特性原來最早就是由 Apple 公司引入，旨在解決不同行動裝置畫面顯示的問題；到現在選擇忽略 `user-scalable=no`，雖然本意是好的，讓使用者能夠自由縮放畫面至合適的大小，卻也因此失去開發網頁的彈性。

但山不轉人轉，我們還是能透過其他語法來達到想要的結果，以下舉幾個範例：

- CSS 屬性 `touch-action: manipulation`：禁止 double-tap
- 監聽 JS 事件 `touchstart/touchend`：禁止 pinch &  double-tap
- 監聽 JS 事件`gesturestart`：禁止 pinch

### A-1. ****CSS**** 解法：以 **[touch-action: manipulation](https://developer.mozilla.org/zh-CN/docs/Web/CSS/touch-action) 禁止 double-tap**

加上 `touch-action: manipulation` 屬性，讓元素只能使用滑動或兩指縮放，即忽略 double tap 手勢的縮放：

```css=
html, body {
  touch-action: manipulation;
}
```

### A-2. ****JS**** 解法：****handle touchstart/touchend event****

監聽這兩個 event 原理如下：

- touchstart：透過偵測 `e.touches.length`（觸控點數目）handle pinch 手勢

```jsx=
  document.addEventListener('touchstart', (event) => {
    if (event.touches && event.touches.length > 1) {  // 禁止多指觸控
       event.preventDefault();
    }
  }, { passive: false });
```

由於 iOS 11.1 版本的變動，預設 `passive: true` 以提高使用性能，但也因此造成 `e.preventDefault()` 語法失效。因此需加上 `{ passive: false }` 屬性，主動告訴瀏覽器這裡的監聽將使用 `event.preventDefault()`，即可阻止事件後面的動作。

可參考這篇討論：[scroll - Can't prevent `touchmove` from scrolling window on iOS - Stack Overflow](https://stackoverflow.com/questions/49500339/cant-prevent-touchmove-from-scrolling-window-on-ios)

- touchend：透過偵測點擊時間差 handle double-tap 手勢

```jsx=
let lastTouchEndTime = 0;
document.addEventListener('touchend', (event) => {
  const now = new Date().getTime();
  if((now - lastTouchEndTime) <= 300) {      // 偵測時間差是否小於 300ms
    event.preventDefault();
  }
  lastTouchEndTime = now;
}, false);
  
```

之所以 `touchend` 會取 `300ms` 時間差，是因為每一次在使用者 touch 螢幕時，都會產生 300ms 的延遲去監聽是否觸發 double-tap。在兩次 touch 之間的時間差小於 300ms 的情況下執行 `event.preventDefault()`，即可取消 double-tap 動作所觸發的縮放效果。

### A-3. JS 解法：[gesturestart](https://developer.mozilla.org/en-US/docs/Web/API/Element/gesturestart_event) event 兩指以上事件觸發

可參考 MDN 文件 [Element: gesturestart event - Web APIs | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/API/Element/gesturestart_event)，和 touchEvent 類似，但僅適用於 iOS 系統。

gesturestart 會在兩指以上觸碰螢幕時觸發，即可忽略  pinch tap 手勢的兩指縮放：

```jsx=
// [Safari only] gesturestart event: multi finger gestures touching 
document.addEventListener('gesturestart', function(event) {
      // 阻止兩指縮放畫面
  event.preventDefault();
});
```

## B. 其他想記錄的

### B-1. mouseEvent vs touchEvent vs gesturestart

- mouseEvent 一次只能有一個觸擊點
- touchEvent 支援多點觸控
- gesturestart 兩指以上觸碰時觸發，僅 iOS 支援

![](https://i.imgur.com/IJ6LKrt.png)

### B-2. `user-select: none`：****禁止區塊反白選取****

即雙點擊不會選取到文字區塊：

```css=
* {
  user-select: none;
  -webkit-user-select: none; /* Chrome Safari */        
  -moz-user-select: none;    /* Firefox */
}
```

## C. 小結

如果被問到工作上曾遇過什麼樣的困難，除了溝通技巧，我想「跨平台開發」肯定能名列前茅。

畢竟光是不同作業系統（如：Windows、Mac、Android、iOS）、不同行動裝置（Pixel、Samsung、iPhone、iPad）、不同版本（Android 10-14、iOS 10-16.3），甚至是不同瀏覽器（Safari、Chrome、Firefox、Edge）等，都可能出現非預期的結果，有時不一定是程式碼有錯誤，而是版本不支援導致，必須仰賴多方測試，才能確定是否能夠兼容各個平台系統。

想當然耳，一名工程師哪可能同時擁有這麼多種裝置，要面面俱到尤其困難，更多的情況是「遇到問題再說」，或盡可能吸收前人的智慧，避免再踩到類似的坑。

## D. Reference

- [html - How do you disable viewport zooming on Mobile Safari? - Stack Overflow](https://stackoverflow.com/questions/4389932/how-do-you-disable-viewport-zooming-on-mobile-safari)
- [“user-scalable=no”屬性被iOS Safari ignore的解決方法 | by 狗狗 | Medium](https://medium.com/@littleDog/%E5%A6%82%E4%BD%95%E8%A7%A3%E6%B1%BA-user-scalable-no-%E5%B1%AC%E6%80%A7%E8%A2%ABios-safari-ignore-e6a0531050ba)
- [禁止Safari 中Double Tap 的縮小和放大. 在很多情況下 - Medium](https://medium.com/@jacky810124/%E7%A6%81%E6%AD%A2-safari-%E4%B8%AD-double-tap-%E7%9A%84%E7%B8%AE%E5%B0%8F%E5%92%8C%E6%94%BE%E5%A4%A7-661802fa27cb)