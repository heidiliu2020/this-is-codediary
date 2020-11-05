###### tags: `程式基礎` `Markdown`

# [Week 0] Markdown 格式介紹

## WYSIWYG（What You See Is What You Ge）：所見即所得

WYSIWYG 編輯器，其實就類似於我們熟悉的 Office 文書軟體，讓使用者在編輯文字時，就能在螢幕上直接呈現最終顯示效果，因此又稱視覺化操作。（[範例 DEMO](https://ckeditor.com/ckeditor-4/demo/)）

---

## Markdown 格式
和視覺化的 WYSIWYG 不同，Markdown 是一種輕量級標記式語言，使用「易讀易寫的純文字格式」編寫文件，然後轉換成有效的XHTML文件。
<center>
    <img src="https://markdown.tw/images/208x128.png"> 
</center>

## 相關語法

### 標題

在行首插入 1 到 6 個 `#` ，各對應到標題 H1 到 H6。

```
# This is an H1

## This is an H2

### This is an H3

#### This is an H4

##### This is an H5

###### This is an H6

###### tags: `This is tag`
```
# This is an H1

## This is an H2

### This is an H3

#### This is an H4

##### This is an H5

###### This is an H6

###### tags: `This is tag`


### 插入連結

範例：
`[Markdown 語法說明](https://markdown.tw/)`

結果：
[Markdown 語法說明](https://markdown.tw/)

---

### 清單

Markdown 支援有序清單和無序清單：

#### 無序清單：使用星號、加號或是減號作為清單標記

```
* 星號
+ 加號
- 減號
```

- 上述標記均產生相同標記，如本行開頭

#### 有序清單：使用數字接著一個英文句點

```
1. 有序
2. 清單
```

1. 有序
2. 清單

---

### 引用

Markdown 使用 email 形式的區塊引言，看起來會像是強迫斷行，然後在每行的最前面加上 `>`。

引言（Blockquotes）常常出現在電子郵件中，表示摘錄信原句並回覆。

```
> 這一行是引言的一部分。
```

> 這一行是引言的一部分。

參考網站：
1. [3分鐘學會Markdown常用語法](https://tiida54.github.io/2018/01/03/3%E5%88%86%E9%90%98%E5%AD%B8%E6%9C%83Markdown%E5%B8%B8%E7%94%A8%E8%AA%9E%E6%B3%95/)
2. [GitBook 中文解說 - 2.4](https://wastemobile.gitbooks.io/gitbook-chinese/content/index.html)