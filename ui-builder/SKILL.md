---
name: ui-builder
description: >
  觸發時機：使用者說「建立 UI」、「實作介面」、「新增頁面」、「加一個元件」、
  「寫這個 view」、「做一個畫面」、「重構 UI」、「開始 UI 實作」、「建立頁面」、
  「實作這個功能的 UI」、「幫我做這個畫面」、「改這個元件」、「套用 ui-guideline」
  等任何需要建構、新增或修改 UI 介面的情境。
  此外，當使用者表達對現有 Vue 元件或頁面進行大幅度結構性改寫的意圖——
  不限特定措辭，只要語意涉及對現有 UI 實作重新規劃或換用不同方式實現——
  也應主動觸發此 skill。
allowed-tools: Read Bash Edit Write
metadata:
    github-path: ui-builder
    github-ref: refs/heads/main
    github-repo: https://github.com/cloris222/my-skills
---

# UI Builder Skill

嚴格依照專案 `docs/ui-guidelines/ui-guidelines.md` 定義的三階段規範執行。
**每個階段的確認門檻必須通過後才可進入下一階段，不可跳過任何步驟。**

> **前置需求**：本 skill 依賴專案 `docs/ui-guidelines/` 目錄下的 6 份規範文件。
> **新專案初次使用**：請先執行 `/setup-ui-guidelines` 問答問卷自動生成規範文件，再啟動本 skill。
> 若規範文件放在其他路徑，請修改下方所有 `docs/ui-guidelines/` 路徑。

---

## 執行前：讀取最新規範

在執行任何步驟前，讀取以下文件確保使用最新規範：

1. `docs/ui-guidelines/ui-guidelines.md`
2. `docs/ui-guidelines/ui-architecture-rules.md`
3. `docs/ui-guidelines/component-discovery-rules.md`
4. `docs/ui-guidelines/component-selection-rules.md`
5. `docs/ui-guidelines/good-bad-ui-implementation-examples.md`
6. `docs/ui-guidelines/frontend-review-checklist.md`

---

## 階段一：開發前 — UI Implementation Plan

### 步驟 1：確認 UI 實作優先順序

依 `docs/ui-guidelines/ui-architecture-rules.md` 確認優先順序：
**共用元件 → UI 庫元件 → utility class → scoped SCSS → raw HTML**

### 步驟 2：搜尋現有元件

搜尋以下目錄，確認是否已有可複用的元件：

```
src/components/
src/components/base/
src/components/shared/
src/features/**/components/
```

### 步驟 3：決定每個 UI 區塊的來源

依 `docs/ui-guidelines/component-selection-rules.md` 逐一決定每個區塊。

### ✋ 確認門檻 A：產出 UI Implementation Plan 並等待使用者確認

以下格式呈現 Plan，**等待使用者確認後才可繼續**：

```
UI Implementation Plan
- [區塊名] → [元件來源與理由]
- [區塊名] → [元件來源與理由]
- 自訂樣式 → [僅列真正需要 scoped SCSS 的項目]
```

---

### 步驟 4：列出 i18n Pre-flight 清單

列出本次所有固定顯示文字（按鈕、標題、提示、佔位符等）：

```
i18n Pre-flight
| # | 顯示文字 | 建議 key | 備註 |
|---|----------|----------|------|
| 1 | ...      | ...      |      |
```

### 步驟 5：Key Reuse Check

掃描 `src/common/config/i18n/` 下的語系檔，對每個建議 key 標記：

- `→ 複用 existing.key`（語意完全相同）
- `→ 新建`（語意有差異，即使文字相似）

**禁止「文字相同但語意不同」的誤複用；禁止重複新增已存在的相同語意 key。**

### ✋ 確認門檻 B：呈現 i18n 清單並等待使用者確認

等待使用者確認 i18n 清單後，執行步驟 6。

### 步驟 6：同步新增至全部語系檔

將確認後的新 key 同步寫入所有語系檔，缺一不可。

---

### 步驟 7：UI 元件庫 API 驗證

凡 Plan 中決定使用 UI 庫元件，**每個元件都必須**使用 context7 MCP 工具查詢 Props 與 Slots 文件：

```
[元件名] API 確認
- prop: [名稱] | 型別: [型別] | 說明: [用途]
- slot: ...
```

### ✋ 確認門檻 C：等待使用者確認 API 後才進入實作

**核心規則**：
- 嚴格依照文件出現的 props/components 實作
- 文件與訓練資料衝突時，**以文件為準**
- **禁止**：查完文件後，實作時回退使用未出現在文件中的 props 或子元件

---

**確認門檻 A、B、C 全部通過後，才進入階段二。**

---

## 階段二：開發中

撰寫 UI 程式碼，對照 `docs/ui-guidelines/good-bad-ui-implementation-examples.md` 中的範例：

- **禁止**重建 UI 庫已提供的元件（button、input、modal、table、tabs、form 等）
- **禁止**在 scoped SCSS 中重建 flex 排版、間距系統、字型系統
- 自訂 scoped SCSS 只允許在「utility class 與 UI 庫 props 皆無法滿足」的情況下使用

---

## 階段三：開發後 — 自我驗證

完成程式碼後，讀取 `docs/ui-guidelines/frontend-review-checklist.md`，逐項確認並回報結果：

```
前端 Review Checklist 結果

Architecture Validation
- [ ] 現有共用元件優先：[通過 / ⚠️ 說明]
- [ ] UI 庫元件優先：[通過 / ⚠️ 說明]
- [ ] 無不必要 raw HTML：[通過 / ⚠️ 說明]
- [ ] 無重複 UI 邏輯：[通過 / ⚠️ 說明]

Styling Validation
- [ ] 複用 utility class：[通過 / ⚠️ 說明]
- [ ] 無任意間距值：[通過 / ⚠️ 說明]
- [ ] 無重複 typography：[通過 / ⚠️ 說明]
- [ ] 無 inline style：[通過 / ⚠️ 說明]

i18n Validation
- [ ] 所有固定文字皆透過 $t() 實作，無裸字串：[通過 / ⚠️ 說明]
- [ ] 所有語系檔全部更新：[通過 / ⚠️ 說明]
- [ ] 實作與 Pre-flight 清單一致：[通過 / ⚠️ 說明]
- [ ] 無重複 key：[通過 / ⚠️ 若有重複條列所有重複 key]
```

**所有項目通過後，UI 實作才算完成。出現 ⚠️ 的項目必須修正後重新執行驗證。**
