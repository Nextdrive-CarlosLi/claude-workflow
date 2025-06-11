# GitHub PR Code Review 工作流程 - NextDrive 專案

## 1. 確認 PR 標題和描述

PR 標題和描述應包含以下信息：
- PR 類型（`feat`、`fix`、`docs`、`style`、`refactor`、`test`、`chore`）
- 版本標籤（例如：`[R25.6A]`）
- PR 描述（例如：`實作 GA 產生 QR Code 畫面`）
- JIRA 票號（例如：`resolve [IOE-2261]`）

## 2. 檢查變更內容

透過 github tool 審核 PR 的變更內容：
注意： 
- 修改內容是否包含隱私風險或邏輯上瑕疵
- 是否修改內容符合 PR 敘述

## 3. 檢查測試內容
- Github status 中的 CI 檢查是否通過

## 4. 送出審核結果 (跟據 1, 2, 3 的檢查結果)
- Approve PR
- Request changes

注意：
- 可留 comment, 但敘述需要簡潔
- 不做過多的程式風格建議

