# 單元測試規範（Vitest）

來源：`tests/unit-testing-agent-rules.md`

---

## A. 測試範疇

### A-1. 只測隔離邏輯

測試目標：
- pure function
- utility function
- validator / transformer
- 業務邏輯
- composable / hook
- Pinia store（狀態邏輯）

### A-2. 禁止真實外部依賴

以下必須 mock 或隔離，不得使用真實版本：
- API 請求
- 資料庫
- 瀏覽器環境（localStorage 等）
- 檔案系統
- 網路條件

---

## B. 斷言規範

### B-1. 斷言必須驗證精確邏輯

✅ 正確：
```ts
expect(calculateTax(100)).toBe(5)
```

❌ 禁止：
```ts
expect(result).toBeDefined()
```

### B-2. 邊界值為必要項目

每個邏輯單元必須涵蓋：
- `null` 輸入
- `undefined` 輸入
- 空字串 / 空陣列 / 空物件
- 邊界數值（0、負數、最大值）
- 無效格式
- 非預期狀態
- **使用者輸入型字串**：前後帶半形空白、全形空白（U+3000）、Tab、換行的輸入（適用於所有處理使用者輸入的 transformer / validator / store action）

### B-3. 例外路徑必須測試

若邏輯會拋出錯誤，需驗證：
- 正確的錯誤類型
- 正確的錯誤訊息
- fallback 行為

---

## C. 隔離規範

### C-1. 測試必須完全獨立

每個測試：
- 可獨立執行
- 不共享可變狀態（使用 `beforeEach` 重置）
- 不依賴執行順序

### C-2. 測試必須確定性

以下情況若未受控 mock，禁止使用：
```ts
Math.random()
Date.now()
```

---

## D. Mock 規範

### D-1. 只 mock 外部邊界

✅ 允許 mock：
- API client
- 瀏覽器 API（localStorage、fetch）
- 資料庫

❌ 禁止 mock：
- 正在測試的業務邏輯本身
- utility function（除非是外部依賴）

### D-2. 避免過度 mock

過度 mock 會隱藏真實行為。測試仍需驗證有意義的邏輯執行。

---

## E. 可維護性

### E-1. 結構清晰

採用 **AAA 結構**：

```
// Arrange：設定前置條件
// Act：執行目標邏輯
// Assert：驗證結果
```

### E-2. 一個測試一個邏輯關注點

不在同一個 `it()` 中測試多個不相關的行為。

---

## F. AI 完稿自我審查

> ⚠️ 不得只在心裡打勾——必須與實際撰寫的測試案例對照後才能確認。

**F-1. 覆蓋完整性（逐函式核對）**

1. 對每個被測函式，確認以下輸入型別都有對應 `it`：
   - 字串參數：正常值 / `null` / `undefined` / `""` / 含空白 / 無效格式
   - 數字參數：正常值 / `0` / 負數 / `NaN` / `Infinity` / 超出業務範圍
   - 物件參數：完整資料 / 空 `{}` / `null` / `undefined`
   > `null` 已測 ≠ `undefined` 已測 ≠ `""` 已測，三者必須各自驗證

2. 每個 `if` / `switch` / `||` / `??` 分支都被至少一個 `it` 觸發？
   - `||` 連鎖需測：第一項有值、第一項無值第二項有值、所有項均無值

3. 若函式依賴另一函式，被依賴函式的所有 exit path 都有對應測試？

**F-2. 品質確認**

4. 斷言驗證精確值（`toBe` / `toEqual`），而非只是 `toBeDefined()`？
5. 如果受測邏輯壞掉，這個測試會失敗嗎？
6. 測試是否確定性（無未受控的 `Math.random` / `Date.now`）？
7. 是否使用了不必要的 mock？
8. 每個 `it` 只測一個邏輯關注點？
