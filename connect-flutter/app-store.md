# Connect iOS App Store 發布 Workflow

## 輸入參數
- **release_version**: 發布版本號 (例如: R25.07A)
- **build_name**: 版本號碼 (例如: 2.0.1303)

## 執行流程

### 1. 查詢 Confluence Release Notes
從 Confluence 文檔查詢指定 release version 的 iOS Release Notes：
- **文檔 URL**: https://nextdrive.atlassian.net/wiki/spaces/PD/pages/1047396353/IoE+Connect
- **頁面 ID**: 1047396353

在表格中找到對應的 release version 列，並取得 iOS 欄位中的三種語系 Release Notes：
- **JP (日文)**: 日文版本的 Release Note
- **EN (英文)**: 英文版本的 Release Note  
- **CH (中文)**: 繁體中文版本的 Release Note

### 2. 執行 iOS App Store GitHub Action
在 Connect-Flutter 專案中執行 `ios-appstore.yml` GitHub Action：

**專案資訊:**
- **Owner**: nextDriveIoE
- **Repo**: Connect-Flutter
- **Workflow**: ios-appstore.yml

**Action 參數:**
- **build-name**: 使用輸入的 build_name 參數
- **ReleaseNoteJP**: 從 Confluence 查詢到的日文 Release Note
- **ReleaseNoteEN**: 從 Confluence 查詢到的英文 Release Note
- **ReleaseNoteZHTW**: 從 Confluence 查詢到的繁體中文 Release Note

### 3. 監控執行結果
- Action 執行後會返回執行 URL，可用於追蹤進度
- 系統會自動計算 build number (將版本號中的點號替換為 0)
- 執行完成後會發送 Google Chat 通知

## 範例執行

**輸入:**
- release_version: R25.07A
- build_name: 2.0.1303

**從 Confluence 查詢到的 Release Notes:**
- JP: バグの修正や安定性の向上などの軽微な改善を行ないました。
- EN: Various stability and performance improvement.
- CH: 本次更新修復了一些錯誤並提升穩定度。

**執行結果:**
- GitHub Action 成功觸發
- Build number: 20001303 (自動計算)
- 包含三種語言的 Release Notes
- 發送通知到相關 Google Chat 頻道

## 注意事項
1. 確保指定的 release version 在 Confluence 表格中存在
2. 如果表格中的版本號欄位為空，仍可使用提供的 build_name
3. Release Notes 會依照 Confluence 文檔中的內容自動填入
4. 執行前請確認 GitHub Action 權限和相關 secrets 設置正確