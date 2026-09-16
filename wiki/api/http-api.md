---
title: HTTP API 契約
type: api
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:main.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:routers/Model.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:routers/Report.py
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/assets/js/api.tsx
last_verified: 2026-09-16
---

# HTTP API 契約

前端 axios instance 使用 base URL `/api`。後端 FastAPI 路由本身未加 `/api` prefix，因此部署層須負責此前綴的對接；詳見[系統總覽](../architecture/system-overview.md)。

| 方法與後端路徑 | 請求 | 回應／作用 | 前端消費者 |
| --- | --- | --- | --- |
| `GET /genReport` | query: `id`, `model`, `size`, `refer` | 同步執行報告生成；成功回傳 `{id}`，例外回傳 HTTP 400 與 `{error}`。 | Home |
| `GET /getReport` | query: `id` | 以 ObjectId 讀取一份報告。 | API wrapper 有提供；未見目前 view 呼叫。 |
| `GET /model` | 無 | Ollama `/api/tags` 的 `models` 陣列。 | Home |
| `POST /report` | `{name, limit, page, sort: {key, type}}` | 回傳 `{data, total}` 的報告清單。 | ReportList |
| `GET /report/{id}` | path ObjectId | 回傳單筆報告。 | Report |
| `DELETE /report/{id}` | path ObjectId | 刪除並回傳字串化 delete result。 | ReportList |
| `PUT /report/markup/{id}` | path ObjectId | 清除全部標記後標記目標，回傳 `{status: true}`。 | ReportList |
| `GET /test`, `GET /test1` | 無 | 分別阻塞／非阻塞約 3 秒後回傳 `OK`。 | API wrapper，測試用途。 |

## 維護注意事項

- `GET /genReport` 會在 HTTP request 生命週期內完成 Steam、Ollama 與 MongoDB 工作，並非背景 job；大量 `size` 或 Ollama 延遲會直接延長請求。
- `POST /report` 的 `name` 被轉為不跳脫的 regex pattern。此為現況行為；輸入處理與 regex 風險未經執行期測試。
- API 目前未見認證、授權、CORS 或 rate limit 設定，不能據此推論部署層沒有這些保護。
