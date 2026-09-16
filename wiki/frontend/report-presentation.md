---
title: 報告呈現與互動邊界
type: frontend
status: verified
source_refs:
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/Report.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/views/ReportList.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/components/ChartBox.tsx
  - frontend@c8108ead15f68116705b3fa0e909a65aa52f7251:src/components/PageBox.tsx
last_verified: 2026-09-16
---

# 報告呈現與互動邊界

`Report` 頁將單筆文件轉成統計圖與 HTML 報告；`ReportList` 負責以建立時間倒序瀏覽、搜尋、標記與刪除。資料欄位來源見[報告文件模型](../data/report-document.md)，後端端點見[HTTP API 契約](../api/http-api.md)。

## 單筆報告視圖

- `GET /report/:id` 取得文件後，以 `markdown.toHTML()` 轉換 `report.summary`，並經 `dangerouslySetInnerHTML` 插入 DOM。
- ECharts 雷達圖使用 `story`、`system`、`music`、`creative`、`replayability`、`difficulty`；treemap 將 `positive` 與 `negative` 的 `title`／`score` 顯示為兩個子樹；其餘兩個長條圖分別消費 `countryObj` 與 `timeObj`。
- `ChartBox` 在 mount 時初始化 ECharts，並登記 `window.resize` handler；元件 unmount 時沒有 dispose chart 或移除 listener。長期使用或頻繁換頁時的資源影響尚未量測。

## 清單、搜尋與分頁

- 初始 query 為 `{page: 1, limit: 10, name: '', sort: {key: 'createTime', type: -1}}`；頁碼變化會觸發重新讀取。
- 搜尋按鈕或 Enter 會把 page 設回 1，並立即以呼叫當下的 `pageForm` 取得資料。React state 更新非同步，故在非第 1 頁搜尋時，這個立即請求仍可能帶舊 page；頁碼 state 變更後會再觸發一次讀取。
- 星號／選單均可呼叫標記；刪除與標記會重新載入現有 query 的清單。刪除沒有確認對話框。
- 分頁元件在 `total=0` 時會算出 `maxNum=0`，下一頁／末頁互動可能把 page 設為 0；後端計算 offset 時未對 page 做範圍驗證。

## 信任與錯誤邊界

- API interceptor 對 HTTP error 顯示 response body 的 JSON 字串並關閉全域 loading；成功回應是否帶 `status < 0` 也會被當作錯誤。
- 後端 prompt 雖要求 Markdown，前端未對轉換後 HTML 做 sanitization；若資料庫中的 summary 含不安全 HTML，可能影響報告頁。這是資料流推論，尚未進行瀏覽器安全測試。

## 相關頁面

- [前端應用流程](application-flow.md)
- [系統總覽](../architecture/system-overview.md)
