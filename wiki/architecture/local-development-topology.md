---
title: 本機開發拓撲
type: architecture
status: verified
source_refs:
  - backend@53da0590a9e6d0d29ee00bee4f963aa9b4ec3f7b:main.py
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:vite.config.ts
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/assets/js/api.tsx
last_verified: 2026-09-16
---

# 本機開發拓撲

前端開發伺服器綁定 `0.0.0.0:8080`。瀏覽器端 axios 使用相對 base URL `/api`；Vite 將此前綴代理到 `http://localhost:8000`，並在轉送前移除 `/api`。因此 FastAPI 路由定義的 `/genReport`、`/report` 等路徑在開發時會對應為瀏覽器的 `/api/genReport`、`/api/report`。端點內容見[HTTP API 契約](../api/http-api.md)，服務依賴見[外部服務整合](../backend/external-integrations.md)。

```text
Browser
  └─ http://<dev-host>:8080/api/<path>
       └─ Vite dev proxy: http://localhost:8000/<path>
            └─ FastAPI route
```

## 已驗證範圍

- 此代理只在 `vite.config.ts` 的 `server` 設定中明確存在；它可驗證本機 Vite 開發流程。
- FastAPI 程式沒有 `/api` prefix，亦沒有 CORS middleware；前端經由同源開發代理時不需要瀏覽器跨來源請求。

## 待確認

- production build 的靜態檔案宿主、反向代理與 `/api` 路徑規則未出現在目前來源；不能把 Vite 開發代理視為部署設定。
- 後端的實際啟動命令未由專案 README 或腳本定義；`requirements.txt` 含 `uvicorn`，但不構成啟動設定證據。

## 相關頁面

- [系統總覽](system-overview.md)
- [維護工作流程](../operations/wiki-maintenance.md)
