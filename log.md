# Wiki 變更日誌

本檔僅追加。標題一律採用 `## [YYYY-MM-DD] <operation> | <scope>`，以便以 `rg '^## \\[' log.md` 檢索。

## [2026-09-16] ingest | initial codebase baseline

- Sources：`backend` main、HEAD `53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b`、clean；`frontend` main、HEAD `c8108ead15f68116705b3fa0e909a65aa52f7251`、clean。
- 建立 Schema、Raw Source 對應、索引與初始頁面：系統總覽、報告生成、前端流程、報告文件模型、HTTP API、維護流程。
- 本次未修改 Raw Source 或前後端程式碼，亦未建立 Git commit。
- 限制：未執行服務、未連線 MongoDB／Ollama／Steam；執行期設定、部署方式與實際文件資料均待驗證。

## [2026-09-16] ingest | frontend and backend source verification

- Sources：`backend` main、HEAD `53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b`、clean；`frontend` main、HEAD `c8108ead15f68116705b3fa0e909a65aa52f7251`、clean。已讀取兩 repo 的目前應用程式碼、設定與近期三筆 commit；Raw Source 與應用程式碼均未修改。
- 新增頁面：[本機開發拓撲](wiki/architecture/local-development-topology.md)、[外部服務整合](wiki/backend/external-integrations.md)、[報告呈現與互動邊界](wiki/frontend/report-presentation.md)。
- 更新頁面：系統總覽、評論報告生成、前端應用流程、報告文件模型、HTTP API 契約與索引。
- 取代的主張：既有系統總覽稱 `/api` 的反向代理或 Vite proxy 未顯示。`frontend@c8108ea:vite.config.ts` 已證實開發伺服器以 `:8080` 將 `/api` 代理至 `http://localhost:8000` 並移除前綴；已改為「production 部署設定待確認」。
- 新發現／限制：評論抓取的首輪與最後一頁累積邊界、前端 Markdown 未 sanitization、空清單分頁可產生 page 0、ECharts listener 未清理，以及未使用的 `mongo_crud.py` 均已記錄。未執行服務、未連線 Steam／Ollama／MongoDB，故外部可用性、實際資料形狀與部署期行為仍待驗證。
