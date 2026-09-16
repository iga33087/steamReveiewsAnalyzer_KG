---
title: 前端應用流程
type: frontend
status: verified
source_refs:
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/main.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/routes/index.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/Home.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/Report.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/ReportList.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/assets/js/api.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/components/ChartBox.tsx
last_verified: 2026-09-16
---

# 前端應用流程

前端是 Vite + React + TypeScript 應用，使用 React Router、Redux Toolkit、Axios、ECharts 與 Bootstrap。`main.tsx` 以 Redux Provider、ToastContainer 和 RouterProvider 包住整個應用。端點細節見[HTTP API 契約](../api/http-api.md)。

## 路由與使用者流程

| 路徑 | Layout | View | 行為 |
| --- | --- | --- | --- |
| `/` | `Layout` | `Home` | 載入模型清單；輸入遊戲 ID／URL、模型、評論數與參考開關後生成報告。 |
| `/reportList` | `Layout` | `ReportList` | 以名稱搜尋、分頁、查看、標記範例或刪除報告。 |
| `/report/:id` | `Layout2` | `Report` | 讀取一筆報告、將 Markdown summary 轉 HTML，呈現統計與圖表。 |

`Home` 生成成功後用 `location.href = './report/${res.id}'` 導向報告頁；此相對導向在不同部署 base path 的行為尚未實測。

## 資料與狀態

- `globalSlice` 僅維護 `loading: boolean`，主要 view 在 API 前後 dispatch `loadingChange`。
- axios response interceptor 遇到 HTTP 錯誤會顯示 toast 並關閉 loading；一般 API 成功時由呼叫端自行關閉。
- `Report` 假設 API 回傳的 `report.summary` 可以安全以 `markdown.toHTML()` 轉換並插入頁面。呈現資料、清單互動與 sanitization 邊界詳見[報告呈現與互動邊界](report-presentation.md)。

## 呈現的報告資料

報告頁使用 `report.score` 六項分數繪製 radar、使用 `positive`／`negative` 建 treemap，並以 `countryObj`、`timeObj` 繪製正負評長條圖。完整 document 欄位見[報告文件模型](../data/report-document.md)。

本機 `/api` 代理規則見[本機開發拓撲](../architecture/local-development-topology.md)。
