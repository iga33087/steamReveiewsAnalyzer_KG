# Raw Sources

此目錄是唯讀來源區。它不保存目前程式碼的副本；本知識庫選擇以外部工作目錄的「目前內容」作為程式碼 Raw Source。

| Source key | 工作目錄 | 範圍 | 同步規則 |
| --- | --- | --- | --- |
| `backend` | `/Users/yuzheng/Desktop/projects/steamReveiewsAnalyzer/steamReveiewsAnalyzer_backend` | Python／FastAPI 後端 | 每次 Ingest 讀取當前工作目錄，記錄 HEAD、branch 與 dirty status。 |
| `frontend` | `/Users/yuzheng/Desktop/projects/steamReveiewsAnalyzer/steamReveiewsAnalyzer_frontend` | Vite／React 前端 | 每次 Ingest 讀取當前工作目錄，記錄 HEAD、branch 與 dirty status。 |

`raw/` 內的任何檔案均為不可變來源：Agent 可以讀取，但不得修改、重新格式化、搬移或刪除。使用者日後可在此加入非程式碼的需求、截圖、匯出資料或外部文件；Agent 需在其來源摘要中引用檔名與雜湊（可取得時）。
