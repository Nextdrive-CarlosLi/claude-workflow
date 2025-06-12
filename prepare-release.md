### Prepare Release Workflow

#### 1. 取得最近的一次 release 版本
首先透過 tool 取得 最近的一次 release 版本, 這邊定義為 A
可能是 vX.X.XXXX 或 vX.X.X 的格式

#### 2. 計算出新的版本
接著找出 Github Action 名稱是否有
Prepare release branch
該 Action 參數有兩個 

release version
base version

其中 base version = A
release version 則使用下面判斷

接著有兩種可能
1. 修復版本 則版本號碼變化如下
   1. Ex: v2.0.0700 變成 v2.0.0701
   2. Ex: v2.0.0701 變成 v2.0.0702
2. 更新版本 則版本號碼變化如下
   1. Ex: v2.0.0700 變成 v2.0.0800
   2. Ex: v2.0.0701 變成 v2.0.0800
3. 重大更新 則版本號碼變化如下
   1. Ex: v2.0.0700 變成 v2.1.0000
   2. Ex: v2.1.0803 變成 v2.2.0000

#### 3. 執行 action
得到結果後填入 release version
透過 github action trigger tool 執行 action