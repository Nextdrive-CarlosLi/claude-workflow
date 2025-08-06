# Control Log Display Rules Generator Workflow

## 概述
此工作流程用於為 IoE Portal 的控制日誌功能產生裝置模型的顯示規則 JSON 文件。

## Input
- **Model**: 裝置模型名稱（例如：StgBattery, EVCharger, AirCon 等）

## Output
- **JSON 文件**: 包含該模型及其變體的完整顯示規則配置

## 前置需求

### 必要文件
1. **Control Log 規格文件**: https://nextdrive.atlassian.net/wiki/spaces/NEB/pages/2541944966/R25.07A~07B+Control+Log
2. **顯示規則格式規範**: https://nextdrive.atlassian.net/wiki/spaces/ioep/pages/2815098931

### 工作目錄
- JSON 輸出目錄: `/Users/carlosli/work/ioe-portal-ui/dummies/json/`

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

## 輸出範例

**檔案路徑**: `/Users/carlosli/work/ioe-portal-ui/dummies/json/stgBatteryDisplayRules.json`

**內容特點**:
- 包含所有模型變體的完整配置
- 統一使用 `value_with_unit` 處理單位顯示
- 枚舉類型使用 `valueMappingKeyCode` 對應
- 適當的單位轉換設定
- 完整的多語言 keyCode 定義

## 注意事項

1. **Scope 名稱**: 必須使用英文名稱，不可使用十六進制代碼
2. **KeyCode 一致性**: 確保命名規範一致，便於後續維護
3. **單位統一**: 所有數值+單位的顯示都使用 `value_with_unit`
4. **變體差異**: 仔細檢查文件中關於變體限制的說明
5. **JSON 格式**: 確保 JSON 語法正確，無多餘逗號或語法錯誤

## 驗證檢查清單

- [ ] 所有 scope 名稱正確（非十六進制代碼）
- [ ] Value type 選擇適當
- [ ] 單位顯示統一使用 `value_with_unit`
- [ ] 枚舉值完整對應
- [ ] 模型變體差異正確處理
- [ ] JSON 格式正確
- [ ] 檔案成功儲存到指定目錄

這個工作流程可重複用於其他裝置模型（如 EVCharger、AirCon 等）的顯示規則產生。
