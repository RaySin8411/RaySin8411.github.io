---
permalink: /posts/drf_basic
display: normal
layout: post
date: '2026-03-03 07:30:47 +08:00'
title: Django REST Framework 入門介紹
tags: Python Django DRF
mathjax: true
---
* content 
{:toc} 
打造高彈性 RESTful API 的利器



## 什麼是 REST API？


* REST 是一種設計 API 的風格，它的核心是讓不同系統之間透過 HTTP 傳遞資料（通常是 JSON 格式）。

* 常見的 HTTP 方法對應的功能如下：

  * GET：取得資料
  * POST：新增資料
  * PUT / PATCH：更新資料
  * DELETE：刪除資料

## Django REST Framework(DRF)
### 功用

* 序列化工具（Serializers）：可以把資料模型轉成 JSON 格式，方便 API 傳送。
* Class-Based View（CBV）：用更簡潔的方式來寫 API 的邏輯。
* 權限與認證系統：輕鬆控管誰能存取 API。
* 自動產生的 API 文件頁面：讓你可以在網頁上測試 API，很方便。


### 功能組件

元件		    | 說明
--------------------|------
Serializer	    | 將 Django Model 轉換為 JSON 格式，或處理輸入資料驗證
ViewSet	    	    | 將 GET / POST / PUT / DELETE 整合在一個類別中
Router	   	    | 自動產生 RESTful 的 URL 配置
Filter	    	    | 支援依欄位值過濾資料（如作者、看板）
Search	   	    | 支援全文關鍵字查詢（如搜尋文章標題）
Permission	    | 權限控管，支援驗證機制（JWT、Token 等）
Browsable API	    | 提供可互動的 API UI，方便測試與開發


### 核心概念

1. Serializer（序列化器)

    用來把 Django 的 Model 資料轉成 JSON，也能把 JSON 轉回 Python 資料。

2. API View（API 視圖）
    
    這是用來處理 API 請求的地方。

3. Router（路由)
    
    用 DRF 提供的 router 可以快速對應 URL 路徑，不用手動設定太多。
