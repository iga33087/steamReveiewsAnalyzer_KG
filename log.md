# Wiki 變更日誌

本檔僅追加。標題一律採用 `## [YYYY-MM-DD] <operation> | <scope>`，以便以 `rg '^## \\[' log.md` 檢索。

## [2026-09-16] ingest | initial codebase baseline

- Sources：`backend` main、HEAD `53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b`、clean；`frontend` main、HEAD `c8108ead15f68116705b3fa0e909a65aa52f7251`、clean。
- 建立 Schema、Raw Source 對應、索引與初始頁面：系統總覽、報告生成、前端流程、報告文件模型、HTTP API、維護流程。
- 本次未修改 Raw Source 或前後端程式碼，亦未建立 Git commit。
- 限制：未執行服務、未連線 MongoDB／Ollama／Steam；執行期設定、部署方式與實際文件資料均待驗證。
