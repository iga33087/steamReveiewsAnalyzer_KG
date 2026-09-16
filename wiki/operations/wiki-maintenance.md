---
title: 維護工作流程
type: operations
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:main.py
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:package.json
last_verified: 2026-09-16
---

# 維護工作流程

本頁是 [AGENTS.md](../../AGENTS.md) 的人類可讀操作版。Schema 本身優先於本頁；Raw Source 對應見[raw/README.md](../../raw/README.md)。

## Ingest 操作清單

1. 先執行兩個 repo 的 `git rev-parse HEAD`、`git branch --show-current`、`git status --short`，記錄目前狀態。
2. 對照 `index.md`，讀取變動檔案及既有相關頁；若工作區 dirty，明確標示本次結論反映未提交內容。
3. 依技術分類更新／新增頁，更新 `source_refs`、`last_verified`、交叉連結與 `status`。
4. 更新索引，然後把來源狀態、頁面變更、衝突與限制追加至 `log.md`。

## Query 操作清單

1. 從 `index.md` 導航到相關頁。
2. 對可能已過期或高影響的結論，檢查其來源檔案與目前 HEAD／工作區。
3. 答覆中指出已驗證事實與未確認範圍。若產生可長期復用的結論，詢問使用者是否寫入 Wiki。

## Lint 操作清單

1. 驗證每一頁的 YAML 欄位、`type`、`status`、英文 kebab-case 名稱、相對連結和 index 條目。
2. 找出無入站連結的頁面、沒有來源的主張與不存在的來源檔案。
3. 對照 source commit 與目前 HEAD，將未重新驗證的頁面標為 `stale`；若現況直接推翻頁面，更新內容並在 log 留痕。
4. 修正明確問題；對證據不足的衝突標記 `needs-verification`，不可猜測。

## 首次基線

首次 Ingest 已在 2026-09-16 完成，來源皆為 clean working tree：backend `53da059`、frontend `c8108ea`。本次僅閱讀程式碼，尚未測試服務或建立／修改應用程式資料。詳見[log.md](../../log.md)。
