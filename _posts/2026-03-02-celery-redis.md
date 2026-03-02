---
permalink: /posts/celery_and_redis
display: normal
layout: post
date: '2026-03-02 10:00:01 +08:00'
title: 'Celery & Redis '
tags: Celery Redis
mathjax: true
---
* content 
{:toc} 
認識Celery & Redis 及 在django專案的功能



## 什麼是 Celery？


Celery 是一個功能強大的 非同步任務隊列（task queue） 框架，支援：



* 後台背景處理（如：發送通知、排程任務）
* 定時任務（如：每天爬一次資料、每小時更新資料）
* 可擴展、多 worker 並行處理

## 什麼是 Redis？

Redis 是一個 記憶體資料庫與訊息中介（Message Broker），常被用來：

* 暫存資料（如快取系統）
* 儲存佇列訊息（如 Celery 任務等待執行時）

在 Celery 架構中，Redis 扮演「任務訊息的排隊者與轉送者」，它會暫存你丟進 Celery 的任務，等 Celery Worker 來取用：

```
Django 發送任務 → Redis 存放任務 → Celery Worker 執行任務
```
