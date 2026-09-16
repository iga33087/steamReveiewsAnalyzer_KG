---
title: 系統總覽
type: architecture
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:main.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Review.py
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/routes/index.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/assets/js/api.tsx
last_verified: 2026-09-16
---

# 系統總覽

此專案由獨立的 React 前端與 Python FastAPI 後端組成，用途是抓取 Steam 遊戲評論、透過本機 Ollama 產生繁體中文分析報告，並將結果保存至 MongoDB。核心報告資料結構見[報告文件模型](../data/report-document.md)，HTTP 邊界見[HTTP API 契約](../api/http-api.md)。

```text
Browser (React /api)
        │
        ▼
FastAPI main.py ──► Steam Store / appreviews API
        │                    │
        ├────────────────────┘
        ├──► Ollama localhost:11434 /api/chat
        └──► MongoDB test.report
```

## 主要流程

1. 使用者在首頁輸入 Steam App ID 或 URL、選擇 Ollama 模型、評論數量與是否參考已標記報告。
2. 前端以 `GET /api/genReport` 呼叫後端；後端從輸入提取數字 App ID，抓取 Steam 商店資訊與評論。
3. 後端把評論每 10 筆分組，最多並行 4 個 Ollama 請求產生摘要，再由一次結構化 Ollama 請求產出最終報告。
4. 後端將 Steam 統計、模型、摘要、最終報告與衍生的語言／日期統計寫入 `test.report`，回傳新文件 ID。
5. 瀏覽器導向 `/report/:id`，讀取並呈現報告；`/reportList` 提供搜尋、分頁、標記範例與刪除。

## 系統邊界

- 前端 API client 的 base URL 是 `/api`；本次來源未顯示其反向代理或 Vite proxy 設定，因此 `/api` 如何對接 FastAPI 為待驗證事項。
- 後端硬編碼 Steam、Ollama 與 MongoDB URL，詳見[評論報告生成](../backend/review-report-generation.md)。本次未進行外部服務連線測試。

## 待確認

- 開發與部署啟動命令、環境變數／反向代理設定未由 README 或設定檔提供。
- `mongo_crud.py` 是通用 CRUD helper，但目前主要報告流程使用 `Mongo.py`；是否為遷移中的替代實作尚不可判定。
