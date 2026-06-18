# 智慧健康日誌與風險評估系統 (期末挑戰 - 題目 A)

## 專案架構
* **前端**：HTML / CSS / JavaScript (原生 Fetch API)
* **後端**：Node.js / Express.js
* **資料庫**：SQLite (better-sqlite3)

## 資料流動說明 (Frontend → API → DB)
1. 使用者在前端表單輸入「睡眠、步數、心情」後，透過 `fetch` 發送 `POST /health-logs` 請求給後端。
2. 後端 API 接收資料後，呼叫內建的決策樹演算法進行風險評估計算。
3. 評估完成後，將原始數據與計算出的 `risk_level` 一起寫入 SQLite 資料庫 (`health_logs` 表)。
4. 寫入成功後回傳 Response，前端收到確認後再次呼叫 `GET /health-logs` 重新渲染歷史紀錄列表與動態徽章。

## ML 決策樹模型整合說明
本系統捨棄寫死的 if-else，實作了基於 **Information Gain (資訊增益)** 的動態決策樹演算法。
* 系統啟動時會自動生成 90 筆具有明確規律的種子資料。
* 每次使用者新增日誌時，後端會撈取歷史資料庫，動態尋找最佳的「切分特徵」與「門檻值」來建構決策樹，並將使用者輸入的新資料餵入該決策樹中預測出「高風險 / 中風險 / 低風險」。
* 前端設有專屬的「AI 模型狀態面板」，會即時呼叫 `GET /health-logs/tree-info` 顯示目前根節點的最佳切分參數。
