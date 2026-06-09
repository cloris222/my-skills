#### 階段一：開發前 — UI Implementation Plan

**在撰寫任何 UI 程式碼前**，先產出一份簡短的 UI Implementation Plan：

1. 依 `{{GUIDELINES_PATH}}/ui-architecture-rules.md` 確認 UI 實作優先順序（共用元件 → UI 庫元件 → utility class → scoped CSS → raw HTML）
2. 依 `{{GUIDELINES_PATH}}/component-discovery-rules.md` 搜尋共用元件目錄中是否已有現成元件
3. 依 `{{GUIDELINES_PATH}}/component-selection-rules.md` 逐一決定每個 UI 區塊的來源

Plan 格式（逐項列出，不得省略）：

```
UI Implementation Plan
- [區塊名] → [元件來源與理由]
- [區塊名] → [元件來源與理由]
- 自訂樣式 → [僅列真正需要 scoped {{STYLE_EXT}} 的項目]
```

**禁止直接撰寫 UI 而不先列出 Plan。**

{{I18N_PREFLIGHT_SECTION}}

**以上步驟全部完成後，才可開始撰寫 UI 程式碼。**

#### 階段二：開發中 — 參照 Good/Bad 範例

撰寫 UI 時，對照 `{{GUIDELINES_PATH}}/good-bad-ui-implementation-examples.md` 中的範例：

- 禁止重建 {{UI_FRAMEWORK}} 已提供的元件（button、input、modal、table、tabs、form 等）
- 禁止在 scoped {{STYLE_EXT}} 中重建 flex 排版、間距系統、字型系統
- 自訂 scoped {{STYLE_EXT}} 只允許存在於「utility class 與 {{UI_FRAMEWORK}} props 皆無法滿足」的情況

{{NO_UI_FRAMEWORK_NOTE}}

#### 階段三：開發後 — frontend-review-checklist 驗證

完成 UI 程式碼後，**對照 `{{GUIDELINES_PATH}}/frontend-review-checklist.md` 自我驗證全部項目**：

- Architecture Validation：現有共用元件 / UI 庫元件優先，無不必要 raw HTML
- Styling Validation：複用 utility class，無任意間距值、無重複 typography、無 inline style
- Layout Validation：使用既有 layout 系統，結構精簡
- Consistency Validation：與現有頁面的互動模式、間距、字型保持一致
- SCSS Validation：無深層巢狀，scoped styles 最小化
- Accessibility Validation：{{UI_FRAMEWORK}} 無障礙功能未被破壞

{{I18N_VALIDATION_SECTION}}

**所有項目通過後，UI 實作才算完成。未通過項目必須修正。**
