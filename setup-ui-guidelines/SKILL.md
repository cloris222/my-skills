---
name: setup-ui-guidelines
description: 手動呼叫專用。透過問答問卷為新專案生成客製化的 ui-guidelines 規範文件集，供 ui-builder skill 使用。僅在使用者明確以 /setup-ui-guidelines 呼叫時啟動。
allowed-tools: Read Write Bash
metadata:
    github-path: setup-ui-guidelines
    github-ref: refs/heads/main
    github-repo: https://github.com/cloris222/my-skills
---

# Setup UI Guidelines

為目前專案生成一套客製化的 `ui-guidelines` 規範文件，供 `ui-builder` skill 使用。

**此 skill 手動呼叫專用**，通常在新專案初始化時執行一次。

---

## Phase 0：讀取模板

在開始問答前，先讀取以下 6 份模板文件（路徑為安裝此 skill 的使用者本機路徑）：

```
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/ui-guidelines.md
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/ui-architecture-rules.md
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/component-discovery-rules.md
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/component-selection-rules.md
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/frontend-review-checklist.md
~/.claude/skills/setup-ui-guidelines/../ui-builder/templates/good-bad-ui-implementation-examples.md
```

將 6 份模板內容暫存於對話脈絡中，供 Phase 3 使用。

---

## Phase 1：問答問卷（共 6 題）

依序詢問以下問題。每題等待使用者回覆後再繼續。

---

### Q1：UI 框架

> 這個專案使用哪個 UI 元件框架？

選項（可直接輸入名稱或選擇）：
- `1` → Naive UI
- `2` → Element Plus
- `3` → Ant Design Vue
- `4` → Vuetify
- `5` → Shadcn Vue
- `6` → Tailwind CSS（無元件庫，純 utility）
- `7` → 完全無 CSS 框架（純 scoped CSS）
- 或直接輸入其他框架名稱

記錄結果為 `UI_FRAMEWORK`。

---

### Q2：Wrapper 元件

> 專案中是否有對 UI 框架元件進行二次封裝的共用元件（如 BaseButton、BaseCard、BaseInput）？

- `y` → 有，請列舉主要元件名稱（用逗號分隔，例如：BaseButton, BaseCard, BaseInput）
- `n` → 無，直接使用 UI 框架原生元件

若有，記錄名稱清單為 `WRAPPER_COMPONENTS_LIST`。

---

### Q3：共用元件搜尋路徑

> 共用元件存放在哪些目錄？（每行一個路徑，按 Enter 確認後輸入「完成」結束）

預設路徑（直接按 Enter 套用）：
```
src/components/
src/components/base/
src/components/shared/
src/features/**/components/
```

記錄為 `COMPONENT_SEARCH_PATHS`（多行）。

---

### Q4：i18n 設定

> 專案是否使用 i18n 多語系？

- `y` → 有，繼續詢問：
  1. 語系檔案目錄路徑（例如：`src/locales/` 或 `src/common/config/i18n/`）
  2. 語系清單（逗號分隔，例如：`zh-TW, en-US, ja`）
  3. 使用的 i18n 函式（`$t()` / `t()` / `useI18n().t()` / 其他）
- `n` → 無，跳過所有 i18n 相關步驟

記錄為 `HAS_I18N`、`I18N_PATH`、`I18N_LOCALES`（陣列）、`I18N_FUNCTION`。

---

### Q5：Styling 方案

> 專案使用哪種 CSS 撰寫方式？

- `1` → SCSS（`.scss`）
- `2` → CSS Modules（`.module.css`）
- `3` → Tailwind CSS（utility-first，不寫 scoped CSS）
- `4` → Plain CSS（`.css`）

記錄為 `STYLE_APPROACH` 與 `STYLE_EXT`（例如 `scss`、`module.css`、`css`）。

---

### Q6：輸出路徑

> 要將生成的 ui-guidelines 文件放在哪裡？

預設路徑（直接按 Enter 套用）：`docs/ui-guidelines/`

請輸入相對於專案根目錄的路徑。若目錄不存在，會自動建立。

記錄為 `OUTPUT_PATH`。

---

## Phase 2：確認摘要

問答完成後，列出確認摘要並等待使用者確認：

```
── UI Guidelines 設定摘要 ──────────────────
UI 框架：       {{UI_FRAMEWORK}}
Wrapper 元件：  {{WRAPPER_COMPONENTS_LIST 或「無」}}
元件搜尋路徑：
  {{COMPONENT_SEARCH_PATHS（每行縮排 2 格）}}
i18n：          {{有 / 無}}
  └ 路徑：      {{I18N_PATH}}
  └ 語系：      {{I18N_LOCALES}}
  └ 函式：      {{I18N_FUNCTION}}
Styling：       {{STYLE_APPROACH}}（{{STYLE_EXT}}）
輸出路徑：      {{OUTPUT_PATH}}
────────────────────────────────────────────

確認後輸入「繼續」開始生成，或說明需要修改的項目。
```

---

## Phase 3：生成文件

確認後，依序生成 6 份文件。每份文件生成完成後告知使用者進度。

### 變數對照表

根據收集到的答案，建立以下替換對照：

| Placeholder | 替換值 | 說明 |
|-------------|--------|------|
| `{{UI_FRAMEWORK}}` | 使用者輸入的框架名稱 | 直接替換 |
| `{{UI_LIB_PREFIX}}` | 框架元件前綴（n- / el- / a- / v- 等） | 依框架推導 |
| `{{LAYOUT_COMPONENTS}}` | 框架的主要 layout 元件 | 依框架推導 |
| `{{UI_COMPONENTS_LIST}}` | 框架常用元件清單 | 依框架推導 |
| `{{UI_COMPOSABLES_SECTION}}` | 框架 composable 說明（若有） | 依框架推導，無則略去 |
| `{{WRAPPER_COMPONENTS_LIST}}` | Q2 收集的元件清單 | 若無則替換為「（無）」 |
| `{{WRAPPER_COMPONENT_RULES}}` | wrapper 元件的使用規則說明 | 有 wrapper → 詳細規則；無 → 精簡說明 |
| `{{WRAPPER_COMPONENTS_RULE_DETAIL}}` | wrapper 元件優先規則 | 有 wrapper → 詳細；無 → 簡短說明 |
| `{{UI_TABLE_COMPONENT}}` | 框架的 table 元件名稱 | 依框架推導 |
| `{{COMPONENT_SEARCH_PATHS}}` | Q3 收集的路徑 | 每行一個路徑 |
| `{{STYLE_APPROACH}}` | Q5 的 Styling 方案名稱 | 直接替換 |
| `{{STYLE_EXT}}` | 樣式副檔名 | scss / module.css / css |
| `{{STYLE_EXT_UPPER}}` | 樣式副檔名大寫 | SCSS / CSS |
| `{{BREAKPOINTS_SECTION}}` | 斷點表格與範例 | 依框架推導，若框架無斷點系統則略去 |
| `{{BREAKPOINT_CHECKLIST_ITEM}}` | checklist 中的斷點驗證項目 | 依框架推導 |
| `{{GUIDELINES_PATH}}` | Q6 的輸出路徑（去除末尾 /） | 直接替換 |
| `{{I18N_PREFLIGHT_SECTION}}` | i18n Pre-flight 完整段落 | 有 i18n → 生成；無 → 空 |
| `{{I18N_VALIDATION_SECTION}}` | i18n 驗證段落 | 有 i18n → 生成；無 → 空 |
| `{{I18N_CHECKLIST_SECTION}}` | checklist 中的 i18n 部分 | 有 i18n → 生成；無 → 空 |
| `{{I18N_FUNCTION}}` | i18n 呼叫函式 | $t() / t() 等 |
| `{{NO_UI_FRAMEWORK_NOTE}}` | 無 UI 框架時的補充說明 | 僅在無框架時加入 |

### 各框架對應值（依 Q1 結果套用）

**Naive UI：**
- `UI_LIB_PREFIX`：`n-`
- `LAYOUT_COMPONENTS`：`n-flex`、`n-grid`
- `UI_COMPONENTS_LIST`：`n-button`、`n-input`、`n-flex`、`n-grid`、`n-data-table`、`n-form`、`n-modal`、`n-select`、`n-tabs`
- `UI_TABLE_COMPONENT`：`n-data-table`
- `UI_COMPOSABLES_SECTION`：加入「Naive UI Composable Rules」（useMessage / useDialog / useNotification / useLoadingBar）
- `BREAKPOINTS_SECTION`：生成 Naive UI 斷點表格（xs/s/m/l/xl）與 `n-grid` 範例

**Element Plus：**
- `UI_LIB_PREFIX`：`el-`
- `LAYOUT_COMPONENTS`：`el-row`、`el-col`
- `UI_COMPONENTS_LIST`：`el-button`、`el-input`、`el-table`、`el-form`、`el-dialog`、`el-select`、`el-tabs`
- `UI_TABLE_COMPONENT`：`el-table`
- `UI_COMPOSABLES_SECTION`：加入 ElMessage / ElNotification 使用說明
- `BREAKPOINTS_SECTION`：生成 Element Plus `el-col :span` 與 `:xs/:sm/:md/:lg/:xl` 響應式範例

**Ant Design Vue：**
- `UI_LIB_PREFIX`：`a-` 或 `<a-`
- `LAYOUT_COMPONENTS`：`a-row`、`a-col`、`a-space`
- `UI_COMPONENTS_LIST`：`a-button`、`a-input`、`a-table`、`a-form`、`a-modal`、`a-select`、`a-tabs`
- `UI_TABLE_COMPONENT`：`a-table`
- `UI_COMPOSABLES_SECTION`：加入 message / notification API 使用說明
- `BREAKPOINTS_SECTION`：生成 Ant Design Vue 斷點說明（xs/sm/md/lg/xl/xxl）

**Tailwind CSS（無元件庫）：**
- `UI_LIB_PREFIX`：（無）
- `LAYOUT_COMPONENTS`：Tailwind `flex`、`grid` utility classes
- `UI_COMPONENTS_LIST`：（無，用 Tailwind utility 組合）
- `UI_TABLE_COMPONENT`：（自訂 table 元件）
- `UI_COMPOSABLES_SECTION`：略去
- `BREAKPOINTS_SECTION`：生成 Tailwind 斷點表格（sm/md/lg/xl/2xl）與 responsive class 範例

**完全無框架：**
- 所有框架相關 section 略去
- `BREAKPOINTS_SECTION`：生成 @media query 範例（依專案慣例）

### 生成 `good-bad-ui-implementation-examples.md`

此文件不使用模板替換，而是依照以下規則**重新生成 4 組 GOOD/BAD 範例**：

1. **Standard Page Header**：header 含標題 + action 按鈕 + 資料列表
2. **Form Layout**：表單含 input + submit
3. **Scoped CSS 使用**：最小化的 custom style，只處理 UI 框架無法滿足的部分
4. **響應式 Layout**：使用框架的 responsive 系統（breakpoints / cols）

每個 GOOD 範例規則：
- 使用 `{{UI_FRAMEWORK}}` 元件 + Wrapper 元件（若有）
- 所有顯示文字使用 `{{I18N_FUNCTION}}("key")`（若有 i18n）
- 樣式使用 `{{STYLE_EXT}}`

每個 BAD 範例規則：
- 顯示對應的反模式（raw HTML、duplicated CSS、arbitrary values、裸字串）

---

## Phase 4：寫入檔案

生成完成後：

1. 確認輸出目錄存在，若不存在則建立：
   ```bash
   mkdir -p {{OUTPUT_PATH}}
   ```

2. 依序寫入 6 份文件：
   - `{{OUTPUT_PATH}}/ui-guidelines.md`
   - `{{OUTPUT_PATH}}/ui-architecture-rules.md`
   - `{{OUTPUT_PATH}}/component-discovery-rules.md`
   - `{{OUTPUT_PATH}}/component-selection-rules.md`
   - `{{OUTPUT_PATH}}/frontend-review-checklist.md`
   - `{{OUTPUT_PATH}}/good-bad-ui-implementation-examples.md`

3. 寫入完成後輸出確認摘要：

```
✅ UI Guidelines 已生成至 {{OUTPUT_PATH}}/

生成的文件：
- ui-guidelines.md
- ui-architecture-rules.md
- component-discovery-rules.md
- component-selection-rules.md
- frontend-review-checklist.md
- good-bad-ui-implementation-examples.md

下一步：
1. 確認生成的文件是否符合專案需求（建議快速瀏覽 good-bad-ui-implementation-examples.md）
2. 若有需要調整，可直接編輯對應文件
3. 將 docs/ui-guidelines/ 加入版本控制（git add）
4. 下次執行 /ui-builder 時會自動讀取這些規範
```

---

## 注意事項

- 此 skill 不會覆蓋已存在的文件，除非使用者明確確認「覆蓋」
- 若 `{{OUTPUT_PATH}}` 已有文件，在 Phase 2 確認摘要中標示警告並詢問處理方式：
  - `overwrite`：覆蓋全部
  - `skip`：跳過已存在的，只生成缺少的
  - `backup`：先備份（加 `.bak` 後綴）再覆蓋
- 生成完成後，`ui-builder` skill 的 SKILL.md 中的路徑若與 `{{OUTPUT_PATH}}` 不同，提醒使用者手動更新 `ui-builder/SKILL.md` 中的 `docs/ui-guidelines/` 路徑
