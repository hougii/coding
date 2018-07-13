---
title: C#的程式開發規範
keywords: 
tags: []
sidebar: code_sidebar
permalink: doc_csharp.html
summary: 定義自身常用的C# 開發規範
---

## 基本開發規範
1. public/protected 的類別/函式 一定要加註解 (請保持加Summary註解的好習慣)

## 架構規範
(TODO)
## 命名規則
(TODO)
### 變數命名
(TODO)
### 類別命名
(TODO)
1.物件類別的命名，為「名詞」命名，依其使用方式處理，由後綴詞區分 (postfix)
```csharp
//下述為class命名建議
XXX    //預設Model命名 (為名詞)
XXXCondition    //查詢條件
XXXGridModel    //供Grid使用的Model
XXXResult       //處理某件事的結果資訊

```
### 函式命名(前綴詞)
(TODO)
1. 為「動詞」開頭 (prefix)
```csharp
//下述為命名規則及其回傳型別
model GetXXX(...) //取得單筆
IEnumerble<model> QueryXXX(...)    //查詢條件 /取得多筆
IEnumerble<model> QueryXXXByYYY(...)    //查詢條件 /取得多筆
void BindXXX(...)   //挷定某些資訊
model CreateXXX(...)   //新增資料 ,回傳值為model(包括Identity)
bool UpdateXXX(...)   //更新資料
bool DeleteXXX(...)   //刪除資料
bool CheckXXX(...)   //檢查是否滿足?
bool VarifyXXX(...)  //驗証XXX
bool IsXXX(...)  //是否為?
bool ContainXXX(...) //是否包含?


```


### Enum命名