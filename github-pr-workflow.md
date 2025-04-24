# GitHub PR 創建工作流程 - NextDrive 專案

## 1. 確認當前分支

首先需要確認當前的工作分支是否與 JIRA 任務單號相符：

```bash
git_status(repo_path="/路徑/到/專案")
```

確認輸出中的分支名稱是否符合格式: `IOE-XXXX`（JIRA 票號）

## 2. 檢查變更內容

查看提交歷史和變更詳情，以便理解 PR 的範圍和內容：

```bash
git_log(max_count=3, repo_path="/路徑/到/專案")
git_diff(target="origin/main...當前分支", repo_path="/路徑/到/專案")
```

根據變更分析內容，確定以下關鍵信息：
- 功能概述（新增了什麼功能）
- 主要變更文件和內容
- 技術實現方式

## 3. 創建 PR

使用標準格式撰寫 PR 標題和描述:

### PR 標題格式
```
<type>: [<release-tag>] <description> resolve [<issue-id>]
```
例如：`feat: [R25.6A] 實作 GA 產生 QR Code 畫面 resolve [IOE-2261]`

### PR 描述模板
PR 描述需包含以下部分：
- 檢查清單（已預設）
- 描述部分：詳細說明功能實現的各個部分
- 測試說明：提供測試功能的步驟

### 使用 API 創建 PR
```
create_pull_request(
  owner="nextDriveIoE",
  repo="專案名稱",
  title="PR 標題",
  body="PR 描述",
  head="當前分支名稱",
  base="main"
)
```

## 4. PR 類型說明

常見 PR 類型前綴：
- `feat`: 新功能
- `fix`: 修復錯誤
- `docs`: 文檔更新
- `style`: 代碼風格修改
- `refactor`: 重構
- `test`: 測試相關
- `chore`: 工具或建構過程變更

## 5. 特殊注意事項

- PR 標題和描述務必使用英文
- PR 描述中應詳細說明功能的實現方式和測試步驟
- 標題必須包含正確的版本標籤，例如：`[R25.6A]`
- PR 需與 JIRA 票號相關聯

## 6. PR 創建後流程

PR 創建完成後，應：
1. 指派合適的審核者
2. 監控 CI 檢查是否通過
3. 及時回應審核意見並進行必要的修改
4. 在獲得批准後合併 PR

這個工作流程適用於所有 NextDrive 專案的 PR 創建過程，特別是 IOE 相關的專案。依照此流程操作可確保 PR 創建過程標準化和高效化。