---
title: 評論報告生成
type: backend
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:main.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Review.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Global.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Mongo.py
last_verified: 2026-09-16
---

# 評論報告生成

`GET /genReport` 建立 `Review(Global.getAppId(id), model, size, refer)` 並 await `main()`。此流程將 Steam 原始評論轉為具 Pydantic schema 驗證的報告文件；完整 API 參數見[HTTP API 契約](../api/http-api.md)，儲存欄位見[報告文件模型](../data/report-document.md)。

## `Review.main()` 的順序

1. 記錄開始時間，從 `https://store.steampowered.com/app/{id}` 擷取遊戲名稱與 header image。
2. 若 `refer=true`，從 MongoDB 找取 `mark: true` 的既有報告作為最終 prompt 的格式參考。
3. 從 `https://store.steampowered.com/appreviews/{id}` 依 cursor 讀取、每頁 10 筆、`filter=recent`、`language=all` 的評論，直到 cursor 重複或累積數量達 `size`。
4. 每 10 筆評論形成一個 chunk，以 `asyncio.gather` 送至 Ollama `/api/chat`；併發上限為 4、timeout 為 300 秒、temperature 為 0。
5. 將各 chunk 摘要送入第二個 Ollama 請求，要求符合 `GameReviewReport` JSON schema。驗證失敗時最多重試 3 次。
6. 計算語言與日期統計，寫入 MongoDB `test.report`，保存新報告 ID。

## 報告契約

`GameReviewReport` 要求 `summary` 至少 500 字；`positive`／`negative` 為必填清單，但目前 Pydantic 欄位未設定最小清單長度；每個項目的名稱長度為 1–20、分數為 0–100。`score` 包含故事、系統、音樂、創意、耐玩性、難度與整數平均值，各為 0–10。Prompt 另要求 `positive`／`negative` 不得為空、所有自然語言使用繁體中文且僅根據評論內容；這項清單非空要求依賴模型遵循 prompt，而非 schema 強制驗證。

## 維護注意事項

- `fetchReviews()` 先將上一輪 response 的 `reviews` 加進資料，再取得下一頁，因此首輪只累積空清單；而迴圈結束前最後取得的一頁不會加入 `self.data`。是否符合目標評論數的產品預期待確認，詳見[外部服務整合](external-integrations.md)。
- `getData()` 以 `getTimeObj()` 的 key 順序建立 `timeRange`，假設資料至少一筆且 key 順序符合預期；空評論或 API 異常的行為未在本次執行期驗證。
- `Global.mongoBase` 包含連線字串，維護時不應把真實密碼或環境祕密複製進 Wiki。設定與服務依賴的狀態見[外部服務整合](external-integrations.md)。
