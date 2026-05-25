# 元件測試規範（@testing-library/vue）

來源：`tests/component-testing-agent-rules.md`

---

## A. 測試範疇

### A-1. 只測公開元件合約

測試目標：
- props（傳入值對渲染的影響）
- slots（插槽內容渲染）
- emits / events（事件發出與 payload）
- rendered output（渲染的可見內容）
- 使用者互動（點擊、輸入、keyboard）
- 無障礙行為（role、aria、label）

### A-2. 禁止測試內部實作

以下項目禁止直接存取或斷言：
- 元件內部 ref
- 私有狀態
- framework 內部機制
- implementation-only 方法

測試只能驗證**外部可觀察的行為**。

---

## B. 渲染規範

### B-1. 驗證有意義的渲染輸出

斷言應驗證：
- 可見文字
- 渲染狀態（顯示 / 隱藏）
- 條件渲染
- disabled / enabled 狀態
- 無障礙屬性

✅ 正確：
```ts
expect(screen.getByRole('button')).toBeDisabled()
expect(screen.getByText('錯誤訊息')).toBeVisible()
```

### B-2. Snapshot 限制

Snapshot **不得**作為唯一驗證策略。  
只有在同時搭配語義斷言時才允許使用。

---

## C. 互動規範

### C-1. 模擬真實使用者互動

✅ 允許的互動方式：
- `fireEvent.click(element)`
- `fireEvent.input(element, { target: { value: '...' } })`
- 鍵盤操作（keydown / keyup）
- focus / blur 事件

❌ 禁止：
- 直接修改元件內部狀態
- 手動呼叫元件內部方法

### C-2. 必須驗證事件發出

測試需確認：
- 事件確實被發出
- payload 內容正確
- 互動後的結果狀態符合預期

### C-3. 含資料淨化邏輯的輸入元件，必須驗證輸出合約

無論使用者輸入的來源或方式，元件最終對外暴露的值（v-model、emit payload）必須符合淨化後的格式。

✅ 必須涵蓋：
- 使用者輸入帶前後半形空白 → 最終 v-model / emit payload 為 trim 後的值
- 使用者輸入帶前後全形空白（U+3000）→ 同上
- 正常輸入（無空白）→ 值不變

❌ 不得關注：
- trim 是在哪個事件（blur / input / change）觸發的
- 元件內部狀態在哪個時間點被更新
- 資料流如何流向 store 或 API

---

## D. 查詢器規範

### D-1. 語義查詢優先序

```
1. getByRole           ← 優先使用（最穩定、最語義化）
2. getByLabelText      ← 表單元素首選
3. getByPlaceholderText
4. getByText
5. getByTestId         ← 最後手段
```

❌ 禁止：
- 脆弱的 CSS 選擇器（`.className > div:nth-child(2)`）
- 實作導向的選擇器

---

## E. 無障礙規範

### E-1. 無障礙行為必須測試

測試需涵蓋：
- 正確的 accessible role
- label / aria-label
- keyboard 可操作性
- disabled 狀態
- aria 屬性

---

## F. 隔離規範

### F-1. 元件必須獨立測試

以下外部依賴需用受控 mock 隔離：
- API 呼叫（mock fetch / axios）
- Pinia store（提供測試用 store）
- Vue Router（提供 stub router）
- i18n（提供測試用翻譯）

---

## G. AI 完稿自我審查

> ⚠️ 不得只在心裡打勾——必須與實際撰寫的測試案例對照後才能確認。

**G-1. 覆蓋完整性**

1. 每個 prop 的正常值、邊界值、預設值行為都有對應 `it`？
2. 所有 `emit` 事件都被驗證了（發出 + payload 正確 + 不重複發出）？
3. 含資料淨化邏輯的輸入，有測試「帶空白 → 輸出為淨化後的值」嗎？

**G-2. 品質確認**

4. 測試的是元件公開行為，而非內部 ref / 私有狀態？
5. 這是在測試 implementation detail 嗎？（若是，刪除）
6. 使用者如果看到這個失敗，會知道發生了什麼問題嗎？
7. 查詢器語義穩定（`getByRole` 優先，避免 CSS 選擇器）？
8. 互動是真實的使用者行為（`fireEvent` / `userEvent`），而非直接呼叫方法？
9. 外部依賴（API / store / router）已用受控 mock 隔離？
10. Snapshot 有搭配語義斷言嗎？
