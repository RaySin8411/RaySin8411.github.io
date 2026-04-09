---
permalink: /posts/ai_agent
display: normal
layout: post
date: '2026-03-21 08:40:49 +08:00'
title: AI Agent - 以OpenClaw 為例介紹
tags: OpenClaw
mathjax: true
---
* content 
{:toc} 
以OpenClaw 為例介紹AI Agent



## 歷史發展
* 2023.04 Auto-GPT
* 2025.02 Claude Code
* 2025.06 Gemini CLI


## OpenClaw (AI Agent) ≠ 人工智慧 (語言模型)
1. 人透過媒介(Ex: 通訊軟體)向AI Agent下指令
2. AI Agent對這些指令做加工後
3. 再傳給語言模型(雲端/地端的模型皆可)


## 語言模型如何回答問題?

- 外界發出”呼叫(Call)” → Prompt Input → 語言模型 → Token Output → 外界接收由Token組合成的Response

本質上是在做文字接龍

### 語言模型輸入與輸出的限制
- Prompt+Token(輸入加輸出)有長度上限 → Context Window (以Token為單位)
- 文字接龍時，語言模型會輸出一個Tokem，然後再放到輸入(Prompt)
- 若輸出太長，到某個階段時會超過長度限制 → 無法準確接龍/無法運作

### 運作方式:
- 使用者發出Request → AI Agent對該Request做加工 → 把地端上的相關資訊整理成一大段文字段落(System Prompt)，加在Request之前再丟給LLM → LLM開始做文字接龍 → AI Agent回傳Response給使用者
- System Prompt有什麼資訊:
  - 會去讀取放置在”地端”的與身分有關的資訊檔案
有哪些工具可使用(以及怎麼用)
  - 紀載資訊非常豐富(長) → 燒錢
  - 可人工修訂，也可由AI修改 → 人工遺漏可能造成錯亂

#### 多輪對話的運作方式
- 核心概念: LLM做的是文字接龍，且LLM沒有記憶模式
- AI Agent會把 Request 加上System Prompt，再加上過去的對話紀錄，形成非常長的Prompt → LLM回傳Response後再回傳給使用者
- AI Agent每次的對話其實都是重新開始 → 需閱讀歷史紀錄


### 可能的防禦方法
- 語言模型層面的防禦 → 加註在提交給LLM的System Prompt內
有可能出錯，Ex: 讀取錯誤、不一定100%執行
- OpenClaw層面的防禦 → Ex: 在執行前加上需要人工審核的步驟


### Context Window終究會不夠 → 記憶問題
- 如何解決尚待研究 → 現行方法是直接清空對話框 or 歷史紀錄
- 記憶方法是把對話紀錄or運行紀錄寫成.md檔 → System Prompt內會有文字指令

#### 如何讀取記憶
- 對記憶的.md檔做 *RAG* → Run Memory_search + memory-get
- Default方法: 將比對結果做Embedding → 加權比較相似分數後找出相對應的記憶
  在MEMORY.md的資料夾內有許多.md檔，.md檔被切成一塊塊的文字(chunk)


### 心跳(HEARTBEAT)機制
- 讓AI Agent每隔一段固定時間對LLM發出固定指令
  HEARTBEAT.md檔中可以寫入需要日常/規律執行的文字描述
- HEARTBEAT指令可以不明確

#### 搭配HEARTBEAT使用的Cron Job系統
- 任務排程系統
- 透過排程系統開始額外的HEARTBEAT (並非透過讀取HEARTBEAT.md來啟動)
- 讓AI學會等待 → 不同AI工具切換時可能會有時間差
- 可以當作任務是否完成的真的double check機制


### Context Compression
- Compaction機制: 當輸入資料快超過Context Window限制時，把以較舊的歷史紀錄變成較短的摘要 → 壓縮memory的機制
- 此機制是可以遞迴的 → 增加長期運行的可能性
- 若放在System Prompt的memory.md內，重要的資訊才不會被壓縮


## 結語
- AI Agent擁有強大的力量但不成熟的想法(無人監控)
- 需要給予安全的環境避免無可挽回的錯誤
- 像對待學生/實習生一樣的指導與檢查
- 安裝在新/格式化的電腦


## 參考

* [解剖小龍蝦 — 以 OpenClaw 為例介紹 AI Agent 的運作原理
](https://www.youtube.com/watch?v=2rcJdFuNbZQ)

* [Github-Auto-GPT](https://github.com/significant-gravitas/autogpt)

* [Claude Code 概述- Claude Code Docs](https://code.claude.com/docs/zh-TW/overview)

* [Github-Gemini CLI](https://github.com/google-gemini/gemini-cli)

* [Github-OpenClaw](https://github.com/openclaw/openclaw)
