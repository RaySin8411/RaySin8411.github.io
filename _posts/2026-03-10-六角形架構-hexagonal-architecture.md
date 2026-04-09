---
permalink: /posts/hexagonal_architecture
display: normal
layout: post
date: '2026-03-10 10:37:14 +08:00'
title: 六角形架構(Hexagonal Architecture)
tags: Hexagonal_Architecture Ports&Adapters Go
mathjax: true
---
* content 
{:toc} 
介紹六角形架構




## 什麼是六角形架構？

六角形架構，又稱為**Ports and Adapters**，或廣義上的**Clean Architecture**，是由 Alistair Cockburn 提出的一種軟體架構模式。

它的核心思想非常簡單：
```
保護你的核心業務邏輯，使其與外部世界（例如 UI、資料庫、第三方服務）完全解耦。
```

### 舉例

想像你的應用程式是一個城堡。
城堡最核心、最寶貴的是國王和他的寶藏（也就是你的**業務邏輯**）。
城堡需要與外界溝通（例如接收糧食、派遣軍隊），
但你不想讓任何人隨便就闖進來。
於是，你在城堡上開了幾個固定的城門（**Ports**），並規定了所有進出的規則。
任何想跟城堡打交道的人，都必須透過這些城門，並使用你指定的交通工具（**Adapters**）。

![Ports and Adapters](https://ithelp.ithome.com.tw/upload/images/20250901/20128815foj9FWvW08.png)

### 對應程式碼

* **內部核心 (Inside / Domain)：**應用程式的核心業務邏輯。它包含了 `Usecase（業務流程）`和 `Domain（業務實體）`。這部分程式碼是純粹的，它不應該知道任何外部技術的細節。它不知道資料是存在 MySQL 還是 MongoDB，也不知道請求是來自 HTTP API 還是命令列。

* **Ports**：是內部核心與外部世界溝通的**介面（Interface）**。它定義了「需要做什麼」，但不關心「如何做」。

* **Adapters**：是 Ports 的具體實現，是連接外部技術和我們核心業務的「橋樑」。

  * **Primary/Driving Adapters**：驅動我們的應用程式執行。
  * **Secondary/Driven Adapters**：被我們的應用程式所驅動。


## 最重要的原則：依賴關係倒轉
```
所有依賴關係都必須指向內部核心。
```

這意味著：

* Adapters（外部）**可以**依賴核心（內部）。
* 核心（內部）**絕對不能**依賴 Adapters（外部）。


## 好處

* **極高的可測試性**：

  我們可以輕易地為核心業務邏輯撰寫單元測試，因為它不依賴任何外部服務。我們只需要建立一個「假的」Adapter（Mock Adapter）來模擬資料庫行為，就可以獨立測試業務流程的正確性。

* **技術無關性與靈活性**：

  我們的核心業務邏輯與外部技術解耦。這意味著，如果我們未來想把 Web 框架從 Gin 換成 Echo，或者把資料庫從 MySQL 換成 PostgreSQL，我們需要做的僅僅是更換或新增一個 Adapter，而核心業務邏輯完全不需要改動。

* **清晰的職責分離**：

  架構強迫我們思考每一段程式碼的職責。
  * `Controller` 只負責處理 HTTP 請求與回應，
  * `Usecase` 只負責業務流程，
  * `Repository` 只負責資料庫存取。
  
  這讓程式碼更容易理解和維護。

## 頂層目錄結構概覽

這邊以一個專業的Go專案當範例

```
/
├── cmd/                # 應用程式進入點
├── deployments/        # 資料庫遷移等部署相關檔案
├── docs/               # 專案文件
├── internal/           # 專案私有程式碼 (核心)
├── pkg/                # 可供外部專案使用的共享庫
├── test/               # 測試輔助工具與資料
├── go.mod              # Go 模組定義
└── Makefile            # 開發任務自動化腳本
```

* `/cmd`: 包含應用程式的 `main` 函式，是所有程式的起點。這裡的程式碼應該非常精簡，主要負責組裝和啟動應用程式。
* `/deployments`: 存放與部署相關的資源，最常見的就是資料庫遷移 (migration) 腳本。
* `/docs`: 你的專案說明書。所有架構決策、API 文件、操作指南都應存放在此。
* `/internal`: 專案的核心業務邏輯所在，也是我們接下來要深入探討的部分。此目錄下的程式碼無法被外部專案匯入，確保了業務邏輯的封閉性。
* `/pkg`: 如果你的專案中有些程式碼可以被其他專案安全地重用，就應該放在這裡。
* `/test`: 存放測試相關的輔助函式或測試資料 (test data)。


### 深入 internal：應用程式的心臟地帶

遵循 "Ports and Adapters" 架構思想，將其劃分為以下職責分明的子目錄：

```
internal/
├── application/      # DI 容器與應用程式生命週期
├── config/           # 設定管理
├── controller/       # Primary/Driving Adapters (HTTP Handlers)
├── database/         # Secondary/Driven Adapters (資料庫實作)
├── domain/           # 核心業務實體與規則
├── help/             # 輔助函式庫
├── http/             # HTTP 伺服器、路由與中介層
├── service/          # 外部服務或共享的基礎設施服務
└── usecase/          # 應用程式核心業務邏輯 (Use Cases)
```

用人體來比喻，逐一解析這些元件：

* `domain`: **心臟**。
  
  * 定義最核心的業務實體 (`User`, `Order`) 和不變的業務規則。
  * 純粹、乾淨，不依賴任何外部技術（沒有資料庫、沒有 API 框架）。

* `usecase`: **大腦**。

  * 這是應用程式的業務邏輯中心。
  * 編排 `domain` 中的實體來完成具體的業務流程（例如：使用者登入、建立訂單）。
  * 會定義所需的外部依賴埠（Ports），通常是介面（interfaces），但自己不關心這些介面的具體實作。


* `controller`: **五官與神經末梢 (Primary/Driving Adapters)**。

  * 應用程式接收指令的入口。
  * 例如，HTTP Handlers 負責解析傳入的請求，驗證參數，然後呼叫對應的 `usecase` 來執行任務，最後將結果格式化回傳給客戶端。

* `database`: **手腳 (Secondary/Driven Adapters)**。
  
  * 是外部依賴的具體實作。
  * 負責實作 `usecase` 中定義的資料儲存埠（例如 `UserRepository` 介面），提供與 MySQL、PostgreSQL 等資料庫互動的真實邏輯。

* `service`: **工具箱**。

  * 封裝那些與核心業務無關，但為應用程式提供基礎設施能力的服務。
  * 例如：
     * 密碼雜湊 (`password`)
     * JWT 權杖處理 (`token`)
     * 快取 (`cache`) 
     * 日誌記錄。

* `http`: **神經系統**。

  * 負責所有 HTTP 相關的基礎建設。
    * server.go 用於設定和管理伺服器的生命週期；
    * route.go 組織所有 API 路由，並將它們指向對應的 controller；
    * middleware/ 則存放請求共用的處理邏輯，如日誌、認證、CORS 等。

* `application`: **生命之火與組裝者**。

   * application.go 是 DI (Dependency Injection) 容器，它在應用程式啟動時，將所有鬆散的元件（如 database 的實作、usecase 的邏輯）組裝在一起，注入到需要它們的地方，最終啟動整個應用程式。

* `config`: **能量來源**。

  * 負責從環境變數或設定檔中讀取應用程式所需的所有設定值
  * 以結構化的方式提供給其他元件使用。

* `help`: **輔助工具**。
   
  * 提供專案內部使用的通用輔助函式，這些函式不屬於任何特定的業務領域。

這種精細的劃分，確保了每個套件的職責都非常單一，是大型專案能夠保持清晰、易於測試和維護的關鍵。


## 參考
* [架構選擇：我們為何在 Go 專案中採用六角形架構
](https://ithelp.ithome.com.tw/articles/10376821)

* [目錄結構的藝術：一個清晰的 Go 專案應該長什麼樣子？](https://ithelp.ithome.com.tw/articles/10377211)
