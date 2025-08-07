# Control Log Display Rules Generator Workflow

## 概述
此工作流程用於為 IoE Portal 的控制日誌功能產生裝置模型的顯示規則 JSON 文件。

## Input
- **Model**: 裝置模型名稱（例如：StgBattery, EVCharger, AirCon 等）

## Output
- **JSON 文件**: 包含該模型及其變體的完整顯示規則配置
- **i18n JSON 文件**: 包含所有 keyCode 的多國語系翻譯（繁中、英文、日文)

## 前置需求

### 必要文件
1. **Control Log 規格文件**: https://nextdrive.atlassian.net/wiki/spaces/NEB/pages/2541944966/R25.07A~07B+Control+Log
2. **顯示規則格式規範**: https://nextdrive.atlassian.net/wiki/spaces/ioep/pages/2815098931

### 工作目錄
- JSON 輸出目錄: `/Users/carlosli/work/ioe-portal-ui/dummies/json/`
- i18n 輸出目錄: `/Users/carlosli/work/ioe-portal-ui/public/locales/`

## 工作流程

### 第 1 步：查詢規格文件
1. 開啟 Control Log 規格文件
2. 在 "支援裝置" 章節中找到目標 model
3. 確認該 model 及其變體的完整列表
4. 記錄每個 model 變體支援的 scope 清單

### 第 2 步：分析 Scope 資訊
對於每個 scope，記錄以下資訊：
- **Scope 名稱**（英文，如：`chargeMethod`, `operationMode`）
- **十六進制代碼**（如：0xC1, 0xDA）
- **Value 範圍**（數值範圍或枚舉值）
- **單位**（如：%, kWh, kW, A）
- **多語言顯示名稱**（日文、英文、中文）
- **特殊限制**（如：某些變體不支援此項目）

### 第 3 步：確定 Value Type
根據 Value 特性選擇適當的 ValueTypeEnum：

| Value 特性 | ValueTypeEnum | 說明 |
|-----------|---------------|------|
| 純數值 + 單位 | `VALUE` | 直接顯示數值 |
| 功率單位（W→kW） | `NUMBER_DIV_1000` | 除以 1000 轉換單位 |
| 能量單位（Wh→kWh） | `NUMBER_DIV_1000` | 除以 1000 轉換單位 |
| 時間格式（hhmm） | `TIME_4` | 轉換為 HH:MM 格式 |
| 時間範圍（hhmmhhmm） | `TIME_RANGE_8` | 轉換為 HH:MM~HH:MM 格式 |
| 枚舉值 | `VALUE` + `valueMappingKeyCode` | 對應不同枚舉值的 keyCode |

### 第 4 步：設計 KeyCode 命名規範
#### 基本 KeyCode 命名
- **Scope 描述**: `{model_prefix}_{scope_name}`
- **單位顯示**: `value_with_unit`（統一使用）
- **枚舉值**: `{model_prefix}_{scope_name}_{enum_option}`

#### 範例
```
stg_battery_operation_mode
stg_battery_operation_mode_charging
stg_battery_operation_mode_discharging
value_with_unit
```

### 第 5 步：創建 JSON 結構

#### 基本結構模板
```json
{
  "controlledBy": {
    "app": {
      "keyCode": "controlled_by_app",
      "keyCodeParams": {
        "appName": ""
      }
    },
    "service": {
      "keyCode": "controlled_by_service", 
      "keyCodeParams": {
        "serviceName": ""
      }
    },
    "system": {
      "keyCode": "controlled_by_system"
    },
    "third_party": {
      "keyCode": "controlled_by_third_party"
    }
  },
  "{ModelName}": {
    "{scopeName}": {
      "keyCode": "{scope_description_keycode}",
      "value": {
        // 根據 Value Type 選擇相應配置
      }
    }
  }
}
```

#### Value 配置範例

**數值 + 單位類型**:
```json
"value": {
  "type": "VALUE",
  "keyCode": "value_with_unit",
  "keyCodeParams": {
    "unit": "%"
  },
  "valuePassToKeyCodeParams": "value"
}
```

**枚舉類型**:
```json
"value": {
  "type": "VALUE",
  "keyCode": "{default_keycode}",
  "valueMappingKeyCode": {
    "1": "{enum_option_1_keycode}",
    "2": "{enum_option_2_keycode}",
    "0": "{enum_other_keycode}"
  }
}
```

**單位轉換類型**:
```json
"value": {
  "type": "NUMBER_DIV_1000",
  "keyCode": "value_with_unit",
  "keyCodeParams": {
    "unit": "kWh"
  },
  "valuePassToKeyCodeParams": "value"
}
```

### 第 6 步：處理模型變體
1. 為每個模型變體創建獨立的配置區塊
2. 根據規格文件中的限制說明，排除不支援的 scope
3. 新增變體專用的 scope（如：Enerezza 的 `userOperationMode`）

### 第 7 步：產生並儲存文件
1. 將完整的 JSON 配置儲存到指定目錄
2. 檔案命名格式：`{modelName}DisplayRules.json`
3. 確保 JSON 格式正確且可讀性良好

### 第 8 步：產生多國語系 i18n 文件

#### 8.1 收集 KeyCode 清單
從 Display Rules JSON 中提取所有使用的 keyCode：
- Scope 描述 KeyCode（如：`stg_battery_operation_mode`）
- 枚舉值 KeyCode（如：`stg_battery_operation_mode_charging`）
- 通用 KeyCode（如：`value_with_unit`, `controlled_by_app`）

#### 8.2 建立翻譯對照表
根據規格文件中的多語言資訊建立對照表：

**Scope 翻譯格式**：
```
{scopeName}:
  日文：{japanese_display_name}
  英文：{english_display_name}
  中文：{chinese_display_name}
```

**數值翻譯格式**：
```
{scopeName} = {valueKey} 時：
  數值: {numeric_value}
  中文：{chinese_translation}
  英文：{english_translation}
  日文：{japanese_translation}
```

#### 8.3 產生 i18n JSON 結構

**繁體中文檔案** (`zh-TW.json`)：
```json
{
  "stg_battery_operation_mode": "運作模式",
  "stg_battery_operation_mode_charging": "充電",
  "stg_battery_operation_mode_discharging": "放電",
  "stg_battery_charge_method": "充電方式",
  "stg_battery_charge_method_max_power": "最大充電電力充電",
  "stg_battery_charge_method_surplus_power": "剩餘電力充電",
  "stg_battery_charge_method_designated_power": "指定電力充電",
  "stg_battery_charge_method_designated_current": "指定電流充電",
  "stg_battery_charge_method_others": "其他",
  "value_with_unit": "{{value}} {{unit}}",
  "controlled_by_app": "由 {{appName}} 控制",
  "controlled_by_service": "由 {{serviceName}} 控制",
  "controlled_by_system": "由系統控制",
  "controlled_by_third_party": "由第三方控制"
}
```

**英文檔案** (`en.json`)：
```json
{
  "stg_battery_operation_mode": "Operation Mode",
  "stg_battery_operation_mode_charging": "Charging",
  "stg_battery_operation_mode_discharging": "Discharging",
  "stg_battery_charge_method": "Charge Method",
  "stg_battery_charge_method_max_power": "Maximum charging electric energy charging",
  "stg_battery_charge_method_surplus_power": "Surplus electric energy charging",
  "stg_battery_charge_method_designated_power": "Designated electric energy charging",
  "stg_battery_charge_method_designated_current": "Designated current power charging",
  "stg_battery_charge_method_others": "Others",
  "value_with_unit": "{{value}} {{unit}}",
  "controlled_by_app": "Controlled by {{appName}}",
  "controlled_by_service": "Controlled by {{serviceName}}",
  "controlled_by_system": "Controlled by System",
  "controlled_by_third_party": "Controlled by Third Party"
}
```

**日文檔案** (`ja.json`)：
```json
{
  "stg_battery_operation_mode": "動作モード",
  "stg_battery_operation_mode_charging": "充電",
  "stg_battery_operation_mode_discharging": "放電",
  "stg_battery_charge_method": "充電方式",
  "stg_battery_charge_method_max_power": "最大充電電力充電",
  "stg_battery_charge_method_surplus_power": "余剰電力充電",
  "stg_battery_charge_method_designated_power": "指定電力充電",
  "stg_battery_charge_method_designated_current": "指定電流充電",
  "stg_battery_charge_method_others": "その他",
  "value_with_unit": "{{value}} {{unit}}",
  "controlled_by_app": "{{appName}}による制御",
  "controlled_by_service": "{{serviceName}}による制御",
  "controlled_by_system": "システムによる制御",
  "controlled_by_third_party": "サードパーティによる制御"
}
```

#### 8.4 檔案儲存路徑
- `/Users/carlosli/work/ioe-portal-ui/src/locales/control-log/zh-TW/strings.json`
- `/Users/carlosli/work/ioe-portal-ui/src/locales/control-log/en/strings.json`
- `/Users/carlosli/work/ioe-portal-ui/src/locales/control-log/ja/strings.json`

#### 8.5 翻譯資源管理
1. **合併現有翻譯**：如果 i18n 檔案已存在，區塊合併到現有結構中
2. **避免重複**：檢查是否已存在相同的 keyCode，避免覆蓋現有翻譯
3. **命名一致性**：確保所有語言版本的 keyCode 結構完全一致

### 第 9 步：翻譯資料處理流程

#### 9.1 Scope 翻譯提取
從規格文件中提取 scope 翻譯資訊：
```
格式：{hex_code}｜{scope_name}
日：{japanese_name}
英：{english_name}
中：{chinese_name}

範例：0xA6｜acChargeUpperLimitSetting
日：AC 充電上限設定
英：AC charge upper limit setting
中：AC 充電上限設定
```

#### 9.2 數值翻譯提取
從規格文件中提取數值翻譯資訊：
```
格式：scope = {scope_name} 時，數值如下
{value1}
{value2}
...
-------------- 以下為中文翻譯
{chinese_translation_1}
{chinese_translation_2}
...
-------------- 以下為英文翻譯
{english_translation_1}
{english_translation_2}
...
-------------- 以下為日文翻譯
{japanese_translation_1}
{japanese_translation_2}
...
```

#### 9.3 自動化翻譯對照
建立自動化流程：
1. 解析規格文件中的翻譯資訊
2. 將 scope 名稱轉換為 keyCode 格式
3. 將數值對應轉換為枚舉 keyCode 格式
4. 產生完整的三語言 i18n JSON 文件

### 第 10 步：驗證 i18n 整合

#### 10.1 結構驗證
- [ ] 所有語言檔案的 keyCode 結構一致
- [ ] 每個 keyCode 在三個語言檔案中都有對應翻譯
- [ ] 參數化字串（如 `{{value}}`, `{{unit}}`）格式正確

#### 10.2 翻譯品質檢查
- [ ] 專業術語翻譯準確
- [ ] 語言風格一致
- [ ] 無遺漏或多餘的翻譯項目

#### 10.3 檔案整合檢查
- [ ] 新翻譯正確合併到現有 i18n 檔案
- [ ] 無覆蓋重要的現有翻譯
- [ ] JSON 語法正確無誤

## 輸出範例

### Display Rules JSON 範例
**檔案路徑**: `/Users/carlosli/work/ioe-portal-ui/dummies/json/control-log-rule.json`

**內容特點**:
- 包含所有模型變體的完整配置
- 統一使用 `value_with_unit` 處理單位顯示
- 枚舉類型使用 `valueMappingKeyCode` 對應
- 適當的單位轉換設定
- 完整的多語言 keyCode 定義

### i18n JSON 範例
**檔案路徑**: 
- `/Users/carlosli/work/ioe-portal-ui/src/locales/portal/zh-TW/strings.json`
- `/Users/carlosli/work/ioe-portal-ui/src/locales/portal/en/strings.json`
- `/Users/carlosli/work/ioe-portal-ui/src/locales/portal/ja/strings.json`

**內容特點**:
- 完整的三語言支援（繁中、英、日）
- 統一的 keyCode 命名規範
- 支援參數化翻譯（如單位、服務名稱）
- 與 Display Rules 完全對應的翻譯覆蓋

## 注意事項

1. **執行前置要求**: 為了避免做錯，規則中需要列出 model 底下所有的 scope 再開始執行任務
2. **Scope 名稱**: 必須使用英文名稱，不可使用十六進制代碼
3. **KeyCode 一致性**: 確保命名規範一致，便於後續維護
4. **單位統一**: 所有數值+單位的顯示都使用 `value_with_unit`
5. **變體差異**: 仔細檢查文件中關於變體限制的說明
6. **JSON 格式**: 確保 JSON 語法正確，無多餘逗號或語法錯誤

## 驗證檢查清單

### Display Rules 檢查
- [ ] 所有 scope 名稱正確（非十六進制代碼）
- [ ] Value type 選擇適當
- [ ] 單位顯示統一使用 `value_with_unit`
- [ ] 枚舉值完整對應
- [ ] 模型變體差異正確處理
- [ ] JSON 格式正確
- [ ] 檔案成功儲存到指定目錄

### i18n 檢查
- [ ] 所有三個語言檔案都已產生
- [ ] 所有 keyCode 在三語言檔案中都有對應翻譯
- [ ] 翻譯內容準確，無錯別字
- [ ] 參數化字串格式正確（`{{value}}`, `{{unit}}` 等）
- [ ] 新翻譯正確合併到現有 i18n 結構
- [ ] 專業術語翻譯一致
- [ ] JSON 語法正確無誤

### 整合驗證
- [ ] Display Rules 中的所有 keyCode 都有對應翻譯
- [ ] 枚舉值映射完整無遺漏
- [ ] 通用 keyCode（如 `controlled_by_*`）翻譯一致
- [ ] 檔案結構符合專案 i18n 規範

這個工作流程可重複用於其他裝置模型（如 EVCharger、AirCon 等）的顯示規則產生。
