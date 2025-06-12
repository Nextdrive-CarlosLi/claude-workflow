### Staging Build Workflow

#### 1. 指定或者取得最近的一次 release 版本
case 1: 指定 release version
case 2: 取得最近的一次 release 版本

#### 2. 確保 release version
首先確保當前狀態已經取得 release version

#### 3. Android Staging build
找出 Github Action 是否有  Android Staging build
執行它，注意 branch 必須是剛才的 release version
並且 Build name 必須是 release version 去掉最前面的 v 字樣

#### 4. iOS Staging build
找出 Github Action 是否有  iOS Staging build
遵照剛才 Android Staging build 的流程執行它