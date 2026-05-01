---
permalink: /posts/Instant_messaging_bot
display: normal
layout: post
date: '2026-05-01 08:07:22 +08:00'
title: 通知系統 - Telegram Bot
tags: Telegram Line
mathjax: true
---
* content 
{:toc}
Telegram Bot通知系統的相關設定及為啥不用Line




## 為什麼使用Telegram Bot 不使用Line
LINE 官方已經宣布 `LINE Notify` 將於 2025 年 4 月 1 日起正式終止服務。現在如果要用 LINE 發通知，必須改用 `LINE Messaging API (Chatbot)`，而這是有訊息則數限制的（免費額度縮水，且超過後要收費），對於頻繁發送回測或即時訊號的開發者來說，壓力很大。

## 與 LINE Messaging API 對比
特性	| Telegram Bot | LINE Messaging API (現行)
--------|--------------|--------------------------
費用 | 完全免費 (目前的 API 政策) | 訊息量大時需付費
Push 限制 | 極高 (每秒可發多則訊息) | 免費方案每月有額度上限
開發難度 | 簡單 (申請 BotFather 即可) | 複雜 (需處理 Webhook, 頻道設定等)
隱私性 | 高，不需要個人手機號碼綁定 | 需綁定 LINE 帳號
格式支援 | 支援 Markdown (字體粗體、程式碼區塊) | 需使用 Flex Message (JSON 格式)

## 🤖 Telegram Bot 申請三部曲
### 第一步：向「機器人之父」申請權限
在 Telegram 搜尋 `@BotFather`（這是一個官方認證、帶藍勾勾的帳號）。

對它輸入指令：`/newbot`。

它會問你機器人的名字（例如：`RaySin_Stock_Bot`）。

接著它會要求你設定 **username**，必須以 `bot` 結尾（例如：`raysin_quant_bot`）。

關鍵產出：它會給你一段 **HTTP API Token**（一長串數字與英文字母組合）。

    ⚠️ 注意： 這串 Token 就是機器人的密碼，請先存放在記事本，絕對不要讓它出現在 GitHub 的代碼中。

### 第二步：取得你的個人 Chat ID（門牌號碼）
機器人有了 Token 只是有了「發送權力」，它還需要知道要把訊息「發給誰」。

在 Telegram 搜尋你剛剛創立好的 username，並對它發送一則隨便的訊息（例如：Hi）。

打開瀏覽器，貼上這串網址（將 `<YourToken>` 換成剛才拿到的 Token）：
[https://api.telegram.org/bot](https://api.telegram.org/bot)<YourToken>/getUpdates

在回傳的 JSON 畫面中，尋找 `"chat":{"id":123456789...}`。

關鍵產出：這串 id 數字 就是你的個人 Chat ID。

### 第三步：環境變數安全性設定
身為後端工程師，我們要確保安全性。明天我們寫代碼時，會把這兩個關鍵數值放在專案根目錄的 .env 檔案裡：

```Plaintext
# .env 檔案內容範例
TG_TOKEN=6823456789:ABCDefghIJKLmnOp...
TG_CHAT_ID=123456789
```

並在 .gitignore 加入 .env，這樣你的敏感資訊就不會隨著 git push 流出去。
