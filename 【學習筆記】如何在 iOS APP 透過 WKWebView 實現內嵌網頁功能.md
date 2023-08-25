###### `ios` `webview` `SwiftUI`
# 【學習筆記】如何在 iOS APP 透過 WKWebView 實現內嵌網頁功能

![](https://hackmd.io/_uploads/BJXLBiH6n.jpg)

> Photo by <a href="https://unsplash.com/@danielkorpai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Daniel Korpai</a> on <a href="https://unsplash.com/photos/Y3LGWCsrgmg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  

## What is Webview？

Webview 其實就是一個精簡的瀏覽器，透過渲染引擎（Webkit）呈現網頁內容，中文又翻作「網頁視圖」。

和我們所熟悉的瀏覽器（例如：iOS / Safari 和 Android / Chrome）相比，最大差異在於，外觀介面沒有網址列、功能鍵等功能。

透過 Webview 實現的混合模式開發（Hyprid APP），相較於原生開發（Native APP），具備跨平台開發、開發效率高、降低成本、可離線執行等優點；然而，也須考量效能性、安全性與兼容性等問題。

## iOS APP 顯示網頁的三種方式

iOS SDK 提供三種方式，讓開發者用來顯示網頁內容：

### 透過 [UIApplication](https://developer.apple.com/documentation/uikit/uiapplication/) 外開網頁視窗

`UIApplication.shared.open` 是 Swift 中的一個 API，功能是可暫時離開當前 APP，開啟指定的 URL。

```swift=
import SwiftUI
struct ContentView: View {
    
    var body: some View {
        Button(action: {
            let url = URL(string: "https://heidiliu2020.github.io/")!
            UIApplication.shared.open(url)
        }) {
            Text("點我前往連結")
        }
    }
}
```

### [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)

和 Android 的 Webview 元件類似，iOS 同樣有 Webview 能用來顯示網頁內容。

而在 iOS 的 WebView 分為 UIWebView 和 iOS 8+ 新增的 WKWebView 兩種類型。

WebKit 框架中的 WKWebView 相較於前者，在於性能與穩定性的提升，具備 Nitro JavaScript 引擎的等優點。並透過 `UIViewRepresentable` 協定包裝 View，顯示特定的網頁內容。

```swift=
import SwiftUI
import WebKit

struct WebView: UIViewRepresentable {
    
    func makeUIView(context: Context) -> some UIView {
        // return UIView object
    }
    
    func updateUIView(_ uiView: some UIView, context: Context) {
        // update UIView
    }
}
```

使用上需注意 WKWebView 沒有內建的 Back & Forward 按鍵，若需提供返回歷史頁面等功能，必須自行開發一個自訂的網頁瀏覽器。

### [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller/)

相較於 WKWebView 的限制，iOS 9 中導入的控制器 SFSafariViewController，能使用與 Safari 瀏覽器相同的功能，例如切換上下頁。

方法是透過 `UIViewControllerRepresentable` 協定定義 SwiftUI view 型別，將 SFSafariViewController 包裝成 SwiftUI view，即可顯示網頁內容。

```swift=
import SwiftUI
import SafariServices

struct SafariView: UIViewControllerRepresentable {
    // TODO...
}
```

## 實戰練習

### Step1. 環境架設：安裝 XCode

首先進入[官網下載頁面](https://developer.apple.com/download/applications/)，登入後選擇要下載 XCode 版本。

![](https://hackmd.io/_uploads/BJ0BxoST3.png)

不同版本的 XCode，在使用上須考慮開發環境、APP 套件與 iOS 版本相容性，若需要安裝特定版本的 XCode，可參考這篇文章：[如何手動快速下載不同版本的Xcode - Poy Chang](https://blog.poychang.net/manually-download-multiple-versions-of-xcode/)。

### Step2. 建立新專案

運行 XCode 後，點擊「Create a new Xcode project」建立新專案。

![](https://hackmd.io/_uploads/B1ST1sB63.png)

接著點選 APP 建立專案範本，內建範本有預設程式碼，能夠協助快速開發：

![](https://hackmd.io/_uploads/HkRefsBan.png)

### Step3. 利用 UIViewRepresentable 協定調用 WKWebView

接著透過 `UIViewRepresentable` 協定包裝 View，顯示指定的網頁內容：

+ ContentView.swift

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        WebView(url: URL(string: "https://heidiliu2020.github.io/")!)
            .edgesIgnoringSafeArea(.all)
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

+ SwiftUIView.swift

```swift=
import SwiftUI
import WebKit

struct WebView: UIViewRepresentable {
    let url: URL

    func makeUIView(context: Context) -> WKWebView  {
        let wkwebView = WKWebView()
        wkwebView.load(URLRequest(url: url))
        return wkwebView
    }

    func updateUIView(_ uiView: WKWebView, context: Context) {
    }
}
```

### Step4. [Building and running an app](https://developer.apple.com/documentation/xcode/building-and-running-an-app)

最後是運行 APP 驗證結果，點選上方的開始鍵執行 Building，點開右側的選單可選擇要運行的裝置版本，或是透過傳輸線連接在實體機上進行測試。

![](https://hackmd.io/_uploads/BkG1Usrpn.png)

## 小結

同樣是因為專案上的測試需求，沒想到這回又再次把 Xcode 安裝回電腦中，也許因為過去曾稍微接觸過一點，這回相對沒那麼迷惘。

能夠直接把 APP Build 到實體手機測試，和透過 XCode 模擬器終究還是有很大區別，雖然要實際上架到 App Store 需要課金，但以現階段而言也已經很夠用了。回顧過去所接觸的知識，似乎明白儘管當初一知半解，只要再多經歷幾次也能夠好好運用這些技術了。

## Reference

+ [使用 UIViewRepresentable 協定　讓你輕鬆建立 SwiftUI TextView](https://www.appcoda.com/swiftui-textview-uiviewrepresentable/)
+ [利用 UIViewControllerRepresentable 協定　在 SwiftUI 存取相簿並使用相機](https://www.appcoda.com.tw/swiftui-camera-photo-library/)
