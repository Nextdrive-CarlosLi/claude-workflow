# Midjourney AI v7 生成規則

### 工作任務
產生 prompt
MMO RPG Lineage II 風格
給 3D 建模 AI 使用
可能是
1. 場景 
2. 可裝備的服裝 (需要給定服裝名稱)
3. 人物角色

### 1.場景
滿足 Lineage II 夢幻風格

### 2.可裝備的服裝
##### 2.1 產生的服裝只可能是下列任一種部位
- Chest 胸甲 - 僅覆蓋胸部到腰部上緣
- Shoulder
- Hands
- Arm
- Pants 包含腰帶/臀甲 - 從腰部開始到腿部
- Shoes
- Back
- Hand

##### 2.2 需要滿足如下規則
- 不能包含人物
- 中間需要摟空確保能穿戴

##### 2.3 圖片需產生正面，側面，背面三種方向

##### 2.4 需要適合 T-Pose (兩臂平展) 的人體穿戴

##### 2.5 部位界線規則
- Chest 部位：僅覆蓋胸部區域，下緣止於腰線，不包含臀部
- Pants 部位：從腰部/臀部開始，包含腰帶、臀甲等下半身護具

##### 2.6 **防止人體生成規則** (重要!)
- 明確使用 "floating armor piece" 概念
- 添加多重否定詞：no human, no body, no character
- 使用白色或純色背景隔離盔甲片段
- 避免使用 T-pose 等人體姿勢描述

##### 2.7 **多角度視圖規則**
- 使用 "3D model reference sheet with multiple angles"
- 明確要求 "front view and side view and back view clearly separated"
- 添加 "orthographic views" 或 "technical drawing style"
- 可使用 "turnaround sheet" 替代方案

### 3.人物角色
這邊作為 NPC 角色使用
需滿足如下條件
- T-Pose (兩臂平展)
- 圖片需產生正面，側面，背面三種方向
- 不拿武器，需要空手

### 注意事項一
需使用 --draft 功能
且加入 profile
--v 7
--p bb77bb9a-3ce0-418c-bb37-bf99b026045a

### 注意事項二
若此次生成的是可裝備的服裝
同一套服裝名稱下，需要使用相同的 seed
Ex: `--seed 399674` 
請替換 399674
且確保同一套服裝名稱使用相同的數字

## **標準服裝 Prompt 模板**

```
[服裝名稱] [部位名稱], medieval fantasy [部位類型] armor only, floating armor piece without body, hollow [部位] design, [材質和風格描述], [裝飾細節], armor piece isolated on white background, no human no body no arms no legs, [部位覆蓋範圍描述], 3D model reference sheet with multiple angles, front view and side view and back view clearly separated, orthographic views, technical drawing style, clean studio lighting --draft --v 7 --p bb77bb9a-3ce0-418c-bb37-bf99b026045a --seed [相同數字]
```

### 模板使用範例 (闇影鎧甲 Chest)：
```
dark shadow breastplate armor piece, medieval fantasy chest armor only, floating armor piece without body, hollow chest piece design, dark metal with shadow engravings, gothic ornate details, armor piece isolated on white background, no human no body no arms no legs, chest protection only ends at ribcage, 3D model reference sheet with multiple angles, front view and side view and back view clearly separated, orthographic views, technical drawing style, clean studio lighting --draft --v 7 --p bb77bb9a-3ce0-418c-bb37-bf99b026045a --seed [相同數字]
```