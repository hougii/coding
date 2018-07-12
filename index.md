---
title: "程式碼首頁"
keywords: sample homepage
tags: []
sidebar: code_sidebar
permalink: index.html
summary: 程式碼的首頁資訊
---
## 第二層標頭
1. 內容一律由 ## 第二層開頭。 (開頭# 第一層為 標題)

### 第三層標頭
TODO

## 本機環境設定
基本參考文： <https://hougii.github.io/orgcoding/mydoc_install_jekyll_on_windows.html>{:target="_blank"}
[Google](https://google.com" target="_blank)
相關指令：
```ruby
--安裝缺法的項目
bundle install 
--更新項目
bundle update
--服務本機啟動
bundle exec jekyll serve
```

## Markdown 特殊常用寫法
### 連結另開視窗
```markdown
-- with target="_blank"，這是kramdown語法，但jekyll支援  <http://bit.ly/2m840QG>{:target="_blank"}
<link>{:target="_blank"}
```
### Markdown 程式支援的語言style
Markdown 使用 rouge ，參考 <https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers>{:target="_blank"}
```markdown
記錄在OneNote
```csharp
```java
```ruby
```shell
```html
```

## 各類註解格式
### 原則
1. 屬Public 的 Class / Function /Field / Property 一定要註解。
2. 為了支援Gen Document的Format 格式
(陸續再補)

### 工具
#### 使用 jsDoc  <http://usejsdoc.org/>{:target="_blank"}
--JS產生註解的工具(??)，產生方式：<http://usejsdoc.org/about-getting-started.html>{:target="_blank"}

### CSharp 程式
多利用/// summary 自動加上註解方式進行
```csharp
/// <summary>
        /// 取得該筆角色資訊
        /// </summary>
        /// <remarks>包括關聯資訊</remarks>
        /// <param name="roleKey"></param>
        /// <returns></returns>
        public RoleData GetRoleDataByKey(int roleKey){......}
```

### javascript程式
--依jsDoc方式註解
function類的註解方式
```js
/**
 * Represents a book.
 * @constructor
 * @param {string} title - The title of the book.
 * @param {string} author - The author of the book.
 */
function Book(title, author) {
}
```
class的註解方式 <http://usejsdoc.org/tags-class.html>{:target="_blank"}
```js
/**
 * Creates a new Person.
 * @class
 */
function Person() {
}

var p = new Person();
```

## windows座標資訊
### 寫LOG格式
```js
var $this = $(this);
var $win = $(window);
var $doc = $(document);
$elem = $("#toc");
//X軸(left)：
console.log("result-left:"+""+"\t|win-width:"+$win.width()+"\t|doc-width:"+$doc.width()+"\t|scroll-left:"+parseInt($win.scrollLeft())+"\t|this-width:"+$this.width()+"\t|elem-width:"+$elem.width()+"\t|elem-offset-left:"+parseInt($elem.offset().left));
//Y軸(top)：
console.log("result-top:"+""+"\t|win-height:"+$win.height()+"\t|doc-height:"+$doc.height()+"\t|scroll-top:"+parseInt($win.scrollTop())+"\t|this-height:"+$this.height()+"\t|elem-height:"+$elem.height()+"\t|elem-offset-top:"+parseInt($elem.offset().top));

```