# Steam 評論分析器 Wiki 索引

此索引是 Wiki 的內容導向入口。查詢時先從此處定位頁面，再依頁面的 `source_refs` 驗證程式碼現況。

## Architecture

- [系統總覽](wiki/architecture/system-overview.md) — 前端、FastAPI、Steam、Ollama 與 MongoDB 的主要資料流。

## Backend

- [評論報告生成](wiki/backend/review-report-generation.md) — `Review` 類別的抓取、分段摘要、結構化報告與儲存流程。

## Frontend

- [前端應用流程](wiki/frontend/application-flow.md) — React 路由、表單、報告清單與報告視圖如何呼叫 API。

## Data

- [報告文件模型](wiki/data/report-document.md) — MongoDB `test.report` 的讀寫欄位與資料生命週期。

## API

- [HTTP API 契約](wiki/api/http-api.md) — 已由程式碼驗證的端點、參數與消費端。

## Operations

- [維護工作流程](wiki/operations/wiki-maintenance.md) — Ingest、Query、Lint 與過期／衝突處理的實際操作。
