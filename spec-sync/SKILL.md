---
description: 規格同步助手。在完成功能開發或 UI 修改後手動呼叫，分析 git diff 判斷哪些改動需要更新 openspec 規格文件，並產出草稿供確認後寫入。
name: spec-sync
metadata:
    github-path: spec-sync
    github-ref: refs/heads/main
    github-repo: https://github.com/cloris222/my-skills
---

# Spec-Sync Skill

分析最近的程式碼改動，判斷是否需要更新 openspec 規格文件，並產出草稿。

**呼叫時機：** 功能開發、UI 改動、或行為修改完成後手動呼叫。

> **依賴工具**：`openspec` CLI（需先安裝，見 README）

## 流程

```
1. 取得 diff（git diff HEAD 或 指定範圍）
2. 讀取現有 openspec specs
3. 判斷哪些改動屬於「規格層級」
4. 產出草稿（ADDED / MODIFIED / REMOVED）
5. 使用者確認後寫入並 validate
```

## Step 1：取得 diff

```bash
# 尚未 commit 的改動
git diff HEAD

# 指定 commit 範圍（例如最近 N 個 commit）
git diff HEAD~3..HEAD

# 指定檔案
git diff HEAD -- src/views/
```

執行後閱讀 diff 內容。若 diff 為空，改用：
```bash
git diff HEAD~1..HEAD
```

## Step 2：讀取現有 specs

```bash
openspec list --specs
openspec show <spec-name>
```

讀取所有相關 capability 的 spec.md。

## Step 3：判斷規格層級的改動

**需要更新規格（MUST 處理）：**
- 新增或移除 API 呼叫
- 新增或移除使用者可見的功能（按鈕、Tab、頁面）
- 改變現有功能的行為（不同的 success/error 處理）
- 改變資料顯示邏輯（顯示欄位、格式）

**不需要更新規格（可 skip）：**
- 純 CSS 微調（顏色、間距、字體大小數值調整）
- 重構（功能不變，只是程式碼結構改變）
- Bug fix（恢復原本預期行為，非新增行為）
- 測試檔案本身的改動

## Step 4：產出草稿

分析後產出符合 openspec 格式的草稿：

```markdown
## ADDED Requirements

### Requirement: [需求名稱]
[系統 SHALL/MUST 做什麼]

#### Scenario: [情境名稱]
- **WHEN** [觸發條件]
- **THEN** [預期結果]

## MODIFIED Requirements

### Requirement: [既有需求名稱（完整複製原本內容再修改）]
[修改後的完整需求描述]

#### Scenario: [情境名稱]
- **WHEN** [觸發條件]
- **THEN** [修改後的預期結果]
```

**注意：**
- MODIFIED 必須貼上原始需求完整內容再編輯，不能只寫差異
- 每個 Requirement 至少一個 Scenario
- 用 SHALL/MUST 描述規範性需求

## Step 5：確認與寫入

1. 將草稿呈現給使用者確認
2. 使用者確認後，決定要：
   - 新增到現有 change（若仍有 active change）
   - 建立新的 change（`openspec/changes/<new-change-id>/specs/<capability>/spec.md`）
3. 寫入檔案
4. 執行驗證：
   ```bash
   openspec validate --all
   ```
5. 若有 active change 且所有工作已完成，詢問是否要 archive：
   ```bash
   openspec archive <change-id> --yes
   ```

## 輸出格式

在 Step 3 後先輸出分析摘要：

```
## Spec-Sync 分析

**檢視範圍：** [git diff 範圍]
**相關 specs：** [列出讀取的 spec]

### 需要更新：
- [改動描述] → [對應 capability] [ADDED/MODIFIED/REMOVED]

### 不需要更新（skip）：
- [改動描述] → [原因]
```

然後呈現草稿供確認。

## 注意事項

- 如果 openspec 目前沒有 active change，需要先 scaffold 一個新的 change
- capability 名稱使用 kebab-case（例如 `class-material-text`）
- change-id 使用動詞開頭（`update-`、`add-`、`fix-`）
- 永遠先分析再寫入，不要未經確認就修改檔案
