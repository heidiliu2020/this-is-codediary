###### tags: `Hexo` `theme`
# 【學習筆記】如何更換 Hexo 主題 & 自訂樣式

[TOC]

## 更換主題

首先介紹 Hexo 專案架構：

```
.  // 根目錄
├─ .deploy_git
├─ node_modules
├─ public　    // 使用 hexo g 指令生成的內容
├─ scaffolds   // 模板
├─ source　    // 存放原始檔案
　　├─ _discarded　// 已刪除文章
　　├─ _drafts　 // 未發布文章
　　├─ _posts　　// 已發布文章（會被 push）
　　├─ about　　 // 關於我
　　└─ categories   // 分類
├─ themes　      // 存放主題
　　└─ landscape　// 預設主題
　　　　├─ layout, scripts, source
　　　　└─ _config.yml　// 修改主題設定
└─ _config.yml　// 修改部落格的通用設定，例如：網站標題、網址、使用主題等
```

### 下載主題

以更換成 next 主題為例，可參考 [hexo-theme-next](https://github.com/theme-next/hexo-theme-next) 官方說明，將主題 clone 到本地端：

```
$ cd hexo
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```

### 修改 /_config.yml 的 theme 設定

更多主題可以到官網的 [Hexo Themes](https://hexo.io/themes/) 選擇，將主題 clone 下來後，修改根目錄的 _config.yml 檔案即可套用：

```yaml=
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
```

---

## 自訂樣式

在更早之前的版本，是透過 _custom.styl 來自訂樣式，目前則改成 source/_data 這個路徑。

可參考這篇：[NexT V7.2.0:How to change blog background？如何更改博客背景？](https://github.com/theme-next/hexo-theme-next/issues/973)

### 修改 next/_config.yml 的 Custom_file_path 設定

在 `/themes/next/_config.yml` 檔案，把 custom_file_path 的 style 註解拿掉：

```yaml=
# Define custom file paths.
# Create your custom files in site directory `source/_data` and uncomment needed files below.
custom_file_path:
  #head: source/_data/head.swig
  #header: source/_data/header.swig
  #sidebar: source/_data/sidebar.swig
  #postMeta: source/_data/post-meta.swig
  #postBodyEnd: source/_data/post-body-end.swig
  #footer: source/_data/footer.swig
  #bodyEnd: source/_data/body-end.swig
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl
```

### 在 /source 建立 _data 資料夾

需注意這裡的 source 路徑並不是 next 主題資料夾，而是在 Hexo 專案根目錄的 source/_data，如果沒有 _data 資料夾需自行建立，並在裡面新增 styles.styl 檔案：

![](https://i.imgur.com/eueuD6k.png)

### 撰寫自訂樣式

接著就可以在 styles.styl 檔案中撰寫樣式，例如設定背景圖片：

```css=
body {
  background: url(/images/bg.jpg);   // 圖片路徑
  background-repeat: no-repeat;　// 不重複
  background-attachment:fixed;　 //　固定背景
  background-size: cover; // 將背景圖片放大至容器大小
  background-position:50% 50%;  // 背景位置置中
}
```

記得把圖片放到對應位置 `/source/images/bg.jpg` 才能讀取到：

![](https://i.imgur.com/9F4gZn4.png)

結果如下：

![](https://i.imgur.com/6n4wOCV.png)

## 取消歸檔的「嗯..! 目前共有 x 篇文章。 繼續努力。」

原先看到可以把 /next/_config.yml 中的 cheers 設定為 false，但目前版本似乎沒有這個設定，後來是看到[這篇文章](https://pingshiuan.gitlab.io/hexo_gitlab_adsense_nctu.html)有提到可以直接修改 next/layout/archive.swig 檔案，把有出現 cheers 的區塊通通註解掉：

```yaml=
  <div class="post-block">
    <div class="posts-collapse">
#  以下通通註解掉   
      {###<div class="collection-title">
        {%- set posts_length = site.posts.length %}
        {%- if posts_length > 210 %}
          {%- set cheers = 'excellent' %}
        {% elif posts_length > 130 %}
          {%- set cheers = 'great' %}
        {% elif posts_length > 80 %}
          {%- set cheers = 'good' %}
        {% elif posts_length > 50 %}
          {%- set cheers = 'nice' %}
        {% elif posts_length > 30 %}
          {%- set cheers = 'ok' %}
        {% else %}
          {%- set cheers = 'um' %}
        {%- endif %}
        <span class="collection-header">{{ __('cheers.' + cheers) }}! {{ _p('counter.archive_posts', site.posts.length) }} {{ __('keep_on') }}</span>
      </div>###}
#  以上通通註解掉 

      {{ post_template.render(page.posts) }}

    </div>
  </div>
```

結果如下：

![](https://i.imgur.com/DRbxd52.png)

---

## 文章搜尋功能

### 修改 next _config.yml 的 local_search 設定

修改` /themes/next/_config.yml` 檔案中的 local_search，把 enable 改為 ture：

```yaml=
# Local Search
# Dependencies: https://github.com/theme-next/hexo-generator-searchdb
local_search:
  enable: true
  # If auto, trigger search by changing input.
  # If manual, trigger search by pressing enter key or search button.
  trigger: auto
  # Show top n results per article, show all results by setting to -1
  top_n_per_article: 1
  # Unescape html strings to the readable one.
  unescape: false
  # Preload the search data when the page loads.
  preload: false
```

### 安裝套件

```
$ npm install hexo-generator-searchdb --save
```

### 新增內容到根目錄的 _config.yml

在 `_config.yml` 檔案最下方加入這段內容：

```yaml=
# 文章搜尋功能
search:
  path: search.xml
  field: post
  content: true
  format: html
```

結果如下：

![](https://i.imgur.com/uPH9x7S.png)
