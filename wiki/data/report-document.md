---
title: 報告文件模型
type: data
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Review.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:lib/Mongo.py
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:routers/Report.py
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/Report.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/ReportList.tsx
last_verified: 2026-09-16
---

# 報告文件模型

報告存於 MongoDB database `test` 的 `report` collection。讀寫由舊式 `lib/Mongo.py` wrapper 進行；欄位由 `Review.getData()` 建立。此頁描述程式實際寫入／讀取的形狀，而非 MongoDB schema validation。

## 頂層欄位

| 欄位 | 來源／用途 |
| --- | --- |
| `info.name`, `info.img` | Steam 商店頁的遊戲名稱與 header image。 |
| `total` | Steam reviews API 的 `query_summary`。 |
| `model`, `size` | 報告生成時選定的 Ollama 模型與評論數量。 |
| `summaryChunk` | 每批 10 筆評論的 Ollama 回應。 |
| `report.summary`, `report.positive`, `report.negative`, `report.score` | 最終 schema 驗證過的分析結果。 |
| `countryObj`, `timeObj` | 由抓取評論衍生的正負評語言／日期統計。 |
| `genStartTime`, `genEndTime`, `createTime` | Unix timestamp。 |
| `timeRange.start`, `timeRange.end` | 依 `timeObj` key 得出的日期邊界。 |
| `mark` | 不由新文件建立時寫入；標記端點把其中一筆設為 `true`，用作範例報告。 |

## 查詢與消費

`POST /report` 依 `info.name` regex、分頁及排序回傳 `{data, total}`。列表前端假設 `_id.$oid`、`info`、`model`、`size`、`report.score.avg` 與可能存在的 `mark`。單筆視圖使用 `GET /report/{id}`，並將 `report.summary` 轉成 HTML，再繪製六項雷達圖、優缺點 treemap、語言與日期長條圖。

## 資料風險

- `mark` 的唯一性由 `PUT /report/markup/{id}` 先把所有文件設 `false` 再設目標 `true`；在併發請求下不是單一原子操作。
- `Mongo.find()` 將 `page`、`limit`、`sort` 從計數查詢移除，再以剩餘 query 計數／查找；呼叫端必須提供這三個欄位。
- 尚未讀取實際資料庫文件，因此欄位完整度、索引與歷史資料相容性為待驗證。
