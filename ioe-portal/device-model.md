### Ioe Portal device model workflow

Repo: https://github.com/nextDriveIoE/ui-mono

## Input
- 版本說明 (ex: 修復版本, 更新版本, 重大更新)
- 整合的 device model jira 單號 `[TPLUS-XXX]`

### 第一步驟
#### 1. 取得最近的一次 i18n 版本
首先透過 tool 取得 最近的一次 i18n 版本
可能是 ioe-i18n@X.X.X 的格式

#### 2. 計算出新的版本
接著找出 Github Action 名稱是否有
Update ioe-i18n and Device Models
該 Action 參數有一個 
- i18n publish version (格式為 X.X.X)

接著有三種可能
1. 修復版本 則版本號碼變化如下
   1. Ex: 0.4.20 變成 0.4.21
   2. Ex: 0.4.21 變成 0.4.22
2. 更新版本 則版本號碼變化如下
   1. Ex: 0.4.20 變成 0.5.0
   2. Ex: 0.5.0 變成 0.6.0
3. 重大更新 則版本號碼變化如下
   1. Ex: 0.4.20 變成 1.0.0
   2. Ex: 1.4.21 變成 2.0.0

#### 3. 執行 action
得到結果後填入 i18n publish version
透過 github action trigger tool 執行 action

#### 4. 等待 action 完成
此時可先中斷任務，回應等待訊息

使用者在收到訊息後，當 action 完成時，會出現一個 pull request
此 pull request 標題包含 `chore: [RX.X.X] Update i18n and device models`
其中 X.X.X 為剛才計算出的版本號碼
此時根據使用者指令繼續執行下一步

#### 5. 合併 pull request
- approve 此 pull request
- 等待 github 自動合併

### 第二步驟
1. 根據 jira 單號找到該 jira 的內容
Ex: https://nextdrive.atlassian.net/browse/TPLUS-1596
其標題為 `[IOE-Frontend] Watanabe_WMS-PE2DI｜CI & CD`
此時該 model name 為 `Watanabe_WMS-PE2DI`, 每張單可能的 model name 不同，請注意, 這邊定義為 M
且定義出 model 的 key 為 `Watanabe_WMS_PE2DI` (需要把 - 改成 _) 這邊定義為 MK
找出該 jira 的 Product Version, 可能為 R25.08A 的格式，此為 release cycle

2. 由於剛才 Pull request 合併了，所以需要先 Pull master branch to local
並且找出 M 的 i18n
可到 `packages/ioe-i18n/src/locales/en.ts` 找到 M 的 i18n key

3. Update the device model definitions in the following files:
   - `packages/hems-ui/src/types/api/request/DashboardDeviceModel.ts`
      新增 DashboardDeviceModel 為 MK 的 enum, value 為 M
   - `packages/hems-ui/src/utils/deviceModelMapper.ts`
      新增 DashboardDeviceModel 為 MK 的 enum, value 為 i18n key
   - `packages/hems-ui/src/types/response/StatusesResponse.ts`
      新增 DashboardDeviceModel 為 MK 的 enum, value 為固定的 DeviceStatusDto
example PR: https://github.com/nextDriveIoE/ui-mono/pull/1516/files

4. 執行 run PR workflow, 其中 release cycle 為 R25.8A (根據剛才得知的 Product Version)