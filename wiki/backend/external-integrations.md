---
title: 外部服務整合
type: backend
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Global.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Review.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Mongo.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:routers/Model.py
last_verified: 2026-09-16
---

# 外部服務整合

後端在程式碼中直接設定 Steam Store／Reviews、Ollama 與 MongoDB 端點。報告編排流程見[評論報告生成](review-report-generation.md)，開發期間的請求路由見[本機開發拓撲](../architecture/local-development-topology.md)。

| 服務 | 程式行為 | 維護影響 |
| --- | --- | --- |
| Steam Store | `fetchInfo()` 讀取 `https://store.steampowered.com/app/{appId}` HTML，以 CSS class 取得遊戲名稱與 header image。 | Store HTML 結構改動或缺少目標元素會使生成失敗。 |
| Steam Reviews | `fetchReviews()` 向 `https://store.steampowered.com/appreviews/{appId}` 請求 JSON；固定 `filter=recent`、`num_per_page=10`、`language=all`、`purchase_type=all`。 | `size` 是抓取迴圈上限條件，但實際累積數可能超過它（以 10 筆頁次加入）。 |
| Ollama | `GET /model` 從 `/api/tags` 直接回傳 `models`；chunk 與最終報告均 POST `/api/chat`。 | chunk 呼叫使用 `httpx.AsyncClient(timeout=300)`，最終報告的 `requests.post` 未設定 timeout。 |
| MongoDB | 舊式 `Mongo.py` 以 `Global.mongoBase` 建立每次操作各自的 `MongoClient`，使用 database `test`、collection `report`。 | 連線字串目前硬編碼於原始碼；Wiki 不複製其值或任何密碼。 |

## 資料處理細節

- `Global.getAppId()` 從使用者輸入中擷取第一個連續數字；它接受 App ID 或含數字的 URL，但不驗證該遊戲是否存在。
- Steam 評論會保留 `review`、`language`、`voted_up` 送入摘要；`timestamp_created` 只用於後續日期統計。
- chunk 以每 10 筆評論分組、最多同時四個請求。最終階段把 chunk 回應中的 `message.content` 傳給具 JSON Schema format 的 Ollama 請求。
- `refer=true` 時，系統尋找一筆 `mark: true` 的文件，僅將其 `report.summary` 作為最終提示中的文法／格式參考。

## 待確認與風險

- 本次未連線外部服務，無法驗證 API 可用性、模型相容性、Steam 回應形狀或 MongoDB 認證。
- `fetchReviews()` 於每輪請求前加入上一輪 `res.reviews`，首輪加入空陣列；當迴圈因 `len(self.data) < size` 結束時，最後一次取得的頁面不會被加入。這是目前程式可直接推得的行為，是否符合產品預期仍待確認。
- `lib/mongo_crud.py` 提供另一套環境變數優先的通用 CRUD class，但 `main.py` 和 routers 未 import 它；目前不參與應用請求路徑。

## 相關頁面

- [報告文件模型](../data/report-document.md)
- [HTTP API 契約](../api/http-api.md)
