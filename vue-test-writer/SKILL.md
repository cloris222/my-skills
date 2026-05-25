---
name: vue-test-writer
description: 為 Vue 3 專案撰寫單元測試（unit test）與元件測試（component test）。當使用者說「幫我寫測試」、「為這個寫 test」、「補測試」、「這個怎麼測」、「針對這個元件加測試」、「測試這個 store」、「測試這個 utility」時，必須觸發此 skill。即使使用者沒有明確說出測試類型，只要涉及 Vue 元件、composable、utility function、Pinia store 的測試需求，都應使用此 skill。框架：Vitest（單元測試）、@testing-library/vue（元件測試）。
---

# Vue Test Writer

為 Vue 3 專案（Vitest + @testing-library/vue）生成高品質測試。

**不參考現有測試檔案**——所有測試依照本 skill 的規範從頭撰寫。

---

## Step 1：判斷測試類型

讀取使用者提供的目標檔案，判斷適合的測試類型：

| 測試類型 | 適用目標 |
|---------|---------|
| **單元測試** | pure function、utility function、validator、transformer、composable、Pinia store、業務邏輯 |
| **元件測試** | Vue 元件（`.vue`）的 props、slots、emits、渲染輸出、使用者互動、無障礙行為 |

若一個任務同時需要兩種測試，分別產生。若無法判斷，詢問使用者。

---

## Step 2：閱讀目標原始碼

在寫任何測試前，必須完整讀取目標檔案，理解：
- 公開介面（函式簽名 / props / emits / slots）
- 核心邏輯與分支條件
- 可能的邊界值與例外狀況

---

## Step 2.5：建立輸入值枚舉表（寫測試前必做）

對**每個函式 / prop**，在開始撰寫測試前先在心中（或草稿中）建立枚舉表，確保後續覆蓋無遺漏：

| 參數型別 | 必須覆蓋的值 |
|---------|------------|
| 字串 | 正常值、`null`、`undefined`、`""`（空字串）、含前後空白、無效格式 |
| 數字 | 正常值、`0`、負數、`NaN`、`Infinity`、`null`、`undefined`、超出業務範圍的值 |
| 物件／陣列 | 完整資料、空 `{}`／`[]`、`null`、`undefined`、缺少必要欄位 |
| 布林 | `true`、`false` |

**額外規則：**
- 若函式的回傳值依賴另一函式（例如 `getLevelColor` 依賴 `getLevel`），必須列出被依賴函式的**所有 exit path**，每條路徑都要有對應測試
- 若分支條件為 `||` / `&&` 連鎖（例如 `a?.msg || b?.message || fallback`），必須枚舉出每個 falsy 組合以確保每條分支都被觸發
- `null` 和 `undefined` 已測不代表 `""` 已測，三者語意不同，必須個別驗證

---

## Step 3：套用對應規範

依測試類型讀取規範參考檔：
- 單元測試 → `references/unit-rules.md`
- 元件測試 → `references/component-rules.md`

**嚴格遵守規範中的所有規則。**

---

## Step 4：撰寫測試

### 測試檔位置

```
src/utils/foo.js          →  tests/unit/foo.test.js
src/stores/fooStore.js    →  tests/unit/fooStore.test.js
src/components/Bar.vue    →  tests/components/Bar.test.js
```

### 單元測試框架模板

```js
import { describe, it, expect, beforeEach, vi } from 'vitest'
// 視需要引入 createPinia / setActivePinia（測試 store 時）

describe('目標名稱', () => {
  // Arrange（共用設定放 beforeEach）

  it('行為描述：正常路徑', () => {
    // Arrange
    // Act
    // Assert
  })

  it('行為描述：邊界值 / 例外', () => {
    // ...
  })
})
```

### 元件測試框架模板

```js
import { describe, it, expect } from 'vitest'
import { render, screen, fireEvent } from '@testing-library/vue'
// 視需要引入 router、pinia、i18n 等 plugin

describe('元件名稱', () => {
  it('渲染行為描述', () => {
    render(MyComponent, { props: { ... } })
    expect(screen.getByRole('button', { name: '送出' })).toBeDisabled()
  })
})
```

---

## Step 5：自我審查（完稿前必做）

> ⚠️ **禁止只在心裡打勾。** 每個問題必須與實際已撰寫的測試案例對照核實，發現缺漏則立即補測，補完後才能輸出。

---

### 5-A. 單元測試——覆蓋完整性驗證（逐函式執行）

對每個被測函式，依照 Step 2.5 的枚舉表逐一核對：

**1. 每種輸入型別都有對應的 `it` 嗎？**

| 型別 | 必測值 | 對應測試存在？ |
|------|--------|-------------|
| 字串 | 正常值 / `null` / `undefined` / `""` / 含空白 / 無效格式 | 每項都必須 ✓ |
| 數字 | 正常值 / `0` / 負數 / `NaN` / `Infinity` / 超出業務範圍 | 每項都必須 ✓ |
| 物件 | 完整 / 空 `{}` / `null` / `undefined` / 缺少欄位 | 每項都必須 ✓ |

> `null` 已測 ≠ `undefined` 已測 ≠ `""` 已測。三者語意不同，缺任一項即為遺漏。

**2. 每個 `if` / `switch` / `??` / `||` 分支都被觸發過嗎？**
- 找出所有條件分支，確認至少一個 `it` 能觸發每條路徑
- `||` 連鎖（如 `a || b || fallback`）：需測 a 有值、a 無值 b 有值、a b 均無值三種情境

**3. 若依賴另一函式，被依賴函式的所有 exit path 都有對應測試嗎？**
- 例如 `getLevelColor` 的回傳值由 `getLevel` 決定：`getLevel` 有 5 條路徑（Fail / Poor / Good / Excellent / Undefined），`getLevelColor` 必須各覆蓋一個

---

### 5-B. 單元測試——品質確認

4. 每個斷言都驗證精確值（`toBe` / `toEqual`），而非只是 `toBeDefined()`？
5. 如果受測邏輯壞掉，這個測試會失敗嗎？
6. 測試是否確定性（無未受控的 `Math.random` / `Date.now`）？
7. 有無過度 mock（mock 了本來就要測的邏輯本身）？
8. 每個 `it` 只測一個邏輯關注點？

---

### 5-C. 元件測試——覆蓋完整性驗證

**1. 所有 props 的對應行為都有測試嗎？**
- 每個 prop 的正常值、邊界值、預設值行為各至少一個 `it`

**2. 所有 emit 事件都被驗證了嗎？**
- 事件有發出 ✓ / payload 內容正確 ✓ / 相同狀態不重複發出（若有 early return）✓

**3. 含資料淨化邏輯的 prop 或 v-model，有測試「帶空白輸入 → 輸出為 trim 後的值」嗎？**

---

### 5-D. 元件測試——品質確認

4. 測試的是元件公開行為，而非內部 ref / 私有狀態？
5. 查詢器語義穩定（`getByRole` 優先，避免 CSS 選擇器）？
6. 互動是真實的使用者行為（`fireEvent.click` / `fireEvent.input`），而非直接呼叫元件內部方法？
7. 外部依賴（API、store、router）已用受控 mock 隔離？
