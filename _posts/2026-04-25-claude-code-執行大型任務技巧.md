---
permalink: /posts/claude_code_skill
display: normal
layout: post
date: '2026-04-25 08:15:26 +08:00'
title: Claude Code 執行大型任務技巧
tags: Claude_Code Agent
mathjax: true
---
* content 
{:toc}
節錄Linkdein某個開發者使用Claude Code的使用心得




## 困難
為什麼給我的Claude Code 安排任務，它都不會一口氣執行完，而是跑最多幾十分鐘就停下來，然後問我要不要繼續。

### 範例
例如讓它把項目中的單測全部補全（大概1k 個），它跑了大概200 個就停了下來。

Claude Code 並不是對一句話任務抗拒，如果不理解它的執行機制，很難設計出能跑長程任務的harness 流程。

在執行一個超大任務的時候，單agent 的執行流程大概是這樣的：
1. 開始是高效模式，指令遵循效果特別棒；
2. 跑了大概80k tokens 的時候，context 開始逼近compact 閾值；
3. 緊接著，對話歷史被壓縮為摘要，模型開始忘記剛修復甚至單測的細節；

而且response 沒有ToolUse 指令時，模型會退出任務，然後開始詢問用戶："我已經修復了約200 個測試，要繼續嗎？"

如果你在當前session，回復繼續，接下來的工作，它會做的更加不符合預期，並且退出得更快。

任何試圖在一個agent session 內完成海量工作的方案，最終都會碰到context 膨脹→ compact → 資訊遺失→ 效率下降的問題。

### 最佳化方向
設計一個主-子Agent 的運行模式（任務調度器），同時將任務進度寫到file system 中（進度持久化），每個子agent 有獨立context、獨立退出邏輯，主agent 只負責調度和進度追踪，從而繞過單一agent 的所有瓶頸。

因此給Claude Code 的指令需要包含至少這三個部分：

1. 任務分解
    
   不要給一個無邊界的指令（如修復所有單測），而是先掃描出所有失敗測試，按目錄或模組分組，每組15-30 個，作為一個獨立子任務。關鍵在於每個子任務的prompt 必須自包含－寫清楚文件路徑、錯誤現象、期望行為，不能寫"根據先前的分析來修復"，因為子agent 看不到父agent 的歷史。

2. 進度持久化

   在專案根目錄維護一個progress.json，記錄completed / failed / pending 三個清單。主agent 每輪調度前讀這個檔案決定下一批任務，子agent 完成後更新對應條目。這樣即使主agent 自己被compact，重讀檔案就能恢復全部狀態。

3. 失敗策略

   子agent 報錯時，如果錯誤可修復，用SendMessage 繼續同一個子agent（保留錯誤上下文更有效率）；如果方向完全錯了，啟動新的子agent 避免錨定在錯誤路徑上；多次失敗則上報用戶，不要無限重試燒token。

### 實際上 - Claude Code 已經內建了這套能力。

最直接的方式是啟用**Coordinator Mode（輸入/coordinator）**，

主agent 自動變成純度調度者：它不執行任何實際工具調用，只負責理解子agent 的返回結果、合成下一步的具體指令、並行派發獨立任務；

每個子agent 會透過AgentTool 啟動，它們有獨立指令、並行派發獨立任務；

每個子agent 會透過AgentTool 啟動，它們有獨立context。

## 結論
設計多個agents，各司其職、快進快出，把進度交給文件系統來記憶。

## 資料來源
* [Linkdein](https://reurl.cc/mpYjzY)
