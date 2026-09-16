# Steam 評論分析器 LLM Wiki Schema

本檔定義本 vault 的長期知識庫規範。所有 agent 必須遵守。

## 範圍與所有權

- 此 vault 是 `steamReveiewsAnalyzer_backend` 與 `steamReveiewsAnalyzer_frontend` 的程式碼維護 Wiki。
- 程式碼目前工作目錄是 Source of Truth；Wiki 是可重建的衍生知識。不要把 Wiki 當成程式行為的權威。
- 原始來源根目錄定義在 [raw/README.md](raw/README.md)。讀取時必須檢查 Git HEAD、branch、`git status --short`，並將結果寫入來源摘要及 `log.md`。
- `raw/` 為唯讀區。Agent 不可新增、修改或刪除其中內容；使用者放入的檔案亦同。外部程式碼 repo 不在本 vault 的可寫範圍。
- Agent 可修改 `wiki/`、`index.md`、`log.md` 與本檔。不要建立 Git commit；由使用者手動管理 Git。

## 目錄、語言與命名

```text
raw/                 # 唯讀來源說明與使用者加入的補充原始資料
wiki/
  architecture/      # 系統、邊界、跨端流程
  backend/           # Python / FastAPI 與服務邏輯
  frontend/          # React UI、路由、狀態
  data/              # MongoDB、資料模型、外部資料
  api/               # HTTP 契約
  operations/        # 維護流程、風險、驗證
index.md             # 內容導向的可導航目錄
log.md               # 僅追加的時間序列紀錄
```

- Wiki 正文使用繁體中文；程式識別字、路徑與必要技術名稱保持原樣。
- 新頁檔名一律為英文小寫 `kebab-case.md`，置於最貼近的分類目錄。跨端流程放在 `architecture/`。
- 用相對 Markdown 連結交叉連結，不使用 Obsidian wiki-link。每個實質 Wiki 頁至少連回一頁相關頁面。

## 頁面格式與狀態

每個 `wiki/**/*.md` 頁面必須以此 YAML frontmatter 開始：

```yaml
---
title: 中文標題
type: architecture | backend | frontend | data | api | operations
status: verified | needs-verification | stale
source_refs:
  - backend@<commit>:relative/path.py
last_verified: YYYY-MM-DD
---
```

- `source_refs` 必須能追溯到本次檢閱的檔案與 commit。跨 repo 可同時列出 `backend@…` 與 `frontend@…`。
- `verified` 表示本頁的主要描述已由 `source_refs` 的當前內容驗證；`needs-verification` 表示缺乏足夠證據或存在未解問題；`stale` 表示來源已變動但尚未重新整合。
- 不把推測表述為事實。用「待確認」段落說明缺的證據、影響與下一步。
- 出現衝突或過期資訊時，以目前程式碼為準，修正 Wiki，並在 `log.md` 記錄被取代的主張與依據；不能判定時改為 `needs-verification`。

## Ingest

使用者要求「Ingest」或提供新 Raw Source 時：

1. 確認來源屬於既定 source root 或由使用者明確指定；`raw/` 一律不寫入。
2. 取得每個受影響 repo 的 HEAD、branch、dirty status、近期 commit，讀取相關檔案與既有 Wiki／`index.md`。
3. 建立或更新受影響頁面，寫入來源參照、驗證日期、交叉連結及未解事項；必要時建立跨端頁。
4. 更新 `index.md`，使每頁僅列一次且摘要反映目前結論。
5. 在 `log.md` 追加 `## [YYYY-MM-DD] ingest | <範圍>`，含來源狀態、變更頁面、衝突或限制。
6. 不修改程式碼、Raw Sources 或建立 commit，除非使用者另行明確要求。

## Query

回答程式碼維護問題時：

1. 先讀 `index.md`，再讀取最相關 Wiki 頁與其 `source_refs` 指向的現況程式碼。
2. 回答要區分已驗證事實、推論及待確認事項，並以 Wiki 相對連結與程式碼路徑指出依據。
3. 若回答產生可重用的架構結論、決策或排障知識，徵求使用者是否要寫回 Wiki；獲同意後以 `query` 類型追加 `log.md`。

## Lint

使用者要求「Lint」時：

1. 檢查 frontmatter 完整性、檔名、類型與來源參照格式。
2. 檢查 `index.md` 的完整性、壞連結、孤立頁及沒有交叉連結的重要概念。
3. 對比 `source_refs` 的 commit 與目前 HEAD／工作區，標記可能過期的頁面。
4. 比對頁面之間的相互矛盾；以程式碼現況更新，無法判定者標為 `needs-verification`。
5. 修正安全且明確的問題，將檢查範圍、修正和未解問題追加到 `log.md`。

## 架構決策的確認規則

遇到會改變 Source of Truth、目錄、命名、資料格式、分類、metadata、Ingest／同步、Git 權限、可寫範圍或衝突策略的未知事項，先使用 `/grill-me`：說明問題、列出選項、標示建議與原因，等待使用者回答。能從現有資料直接驗證的事實，不要詢問。
