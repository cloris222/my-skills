---
name: page-dev-workflow
description: 整合 openspec、spec 討論、superpowers TDD 規劃的頁面開發標準工作流程。觸發時機：使用者提出「要求實際動工」的開發需求時自動觸發，例如「開發」、「幫我做一個功能」、「新增...功能」、「加一個頁面」、「做一個新頁面」、「修正頁面」、「加入...邏輯」、「幫我加上」、「實作...」、「建立...頁面/功能」等；若使用者只是要分析、比較、找 bug 原因、或詢問建議（尚未要求動工，例如「我想知道」「請問」「確認是否」「評估」「分析」開頭的訊息），不觸發此 skill，改依 CLAUDE.md 程式碼修改原則僅提供報告與建議。也可用 /page-dev-workflow 手動呼叫。
metadata:
    github-path: page-dev-workflow
    github-ref: refs/heads/main
    github-repo: https://github.com/cloris222/my-skills
---

# 頁面開發標準工作流程

整合 openspec、spec 討論、superpowers 規劃，以 TDD 模式驅動頁面開發的完整流程。

**此 skill 可依上方 description 的觸發時機自動啟動，亦可用 `/page-dev-workflow` 手動呼叫。** 判斷觸發與否時，優先確認使用者是否已明確要求動工（而非僅要求分析或建議）。

> **依賴 skills**：`openspec`、`grilling`（grill-me 規格壓力測試）、`superpowers:writing-plans`、`superpowers:test-driven-development`、`smart-commit`

---

## 步驟一：確認工作類型

若使用者尚未說明，詢問這次的工作類型：

1. **建立新頁面**
2. **針對舊頁面重構**
3. **針對舊頁面新增功能或修正邏輯**

記住類型供後續步驟使用。

---

## 步驟二：查找既有 spec（新舊頁面皆詢問）

詢問使用者：

> 是否有相關的既有 spec 需要參考？例如：相依頁面、相似功能架構、共用 API。

- **有** → 執行 `openspec spec list --long` 搜尋，閱讀相關 spec 建立背景知識後進入步驟三
- **無** → 直接進入步驟三

---

## 步驟三：建立 openspec change

呼叫 `openspec` skill，建立本次工作的新 change。

- change-id 使用 kebab-case、動詞開頭（`add-`、`refactor-`、`update-`、`fix-`）
- proposal 此時只需填入初步描述，後續步驟會補充細節

---

## 步驟四：收集需求素材並完善 spec

依序詢問使用者（每項皆可跳過）：

1. **API 規格**：是否有新的 endpoint 或參數變更需要說明？
2. **UI 截圖或設計稿**：是否有視覺參考？
3. **功能說明**：用自己的話描述這個功能或修改的目的與預期行為

根據收集到的素材，草擬 spec 文件（`proposal.md`、`tasks.md`，必要時補充 `design.md`）。

### 步驟四之一：grilling 規格壓力測試訪談

spec 草稿完成後，詢問使用者：

> spec 草稿已完成。是否進行 grilling 規格壓力測試訪談？我會針對規格的每個決策分支逐一提問（一次一題、附建議答案），直到雙方對規格達成共識。建議新頁面與較大的功能變更執行；小型修正可跳過。

- **執行** → 呼叫 `grilling` skill 進行訪談，並遵守以下回寫規則：
  - 訪談中每確認一項決策，隨即回寫至 `proposal.md`（行為與範圍）或 `design.md`（技術取捨），不可等訪談全部結束才憑記憶補寫
  - 可從 codebase 或 `docs/api.json` 查證的「事實」自行查證，不拿來問使用者；「決策」才逐題交由使用者定奪
  - 訪談結束後向使用者摘要本次訪談新增／修改的 spec 內容
- **跳過** → 直接與使用者討論完善 spec

> **重要**：無論是否執行 grilling，皆須等待使用者明確表示「spec 確認完成」後才進入下一步，不可自行假設 spec 已完成。

---

## 步驟五：規劃開發流程（TDD）

呼叫 `superpowers:writing-plans` skill，依據完成的 spec 規劃實作計畫。

規劃時明確指定以 **TDD 模式**實作：
- 每個功能單元先寫測試，測試通過才算完成
- 呼叫 `superpowers:test-driven-development` 指導 TDD 流程執行

若本次涉及 UI 實作（新增/修改畫面、元件），規劃前先確認專案內是否存在 UI 開發規範文件（例如 `reference-docs/ui-guidelines/` 或類似路徑）：

- **有** → 規劃時 SHALL 將其核心原則內化到 `tasks.md` 的任務描述中（不呼叫 `ui-builder` skill，避免與本流程的主導權衝突）：
  - UI 元件優先順序：共用元件 → UI 庫元件（如 Element Plus）→ utility class → scoped CSS → raw HTML，由上而下優先採用
  - 開發前先搜尋專案既有共用元件／樣式，若已有相同或相似用途的元件，SHALL 優先重用或擴充，不重新開發相似樣式
  - `tasks.md` 中涉及 UI 元件的任務，應註記「元件來源與理由」（沿用既有元件的名稱／新建的原因），供實作與 code review 時核對
- **無** → 詢問使用者是否要透過 `setup-ui-guidelines` skill（問答問卷）建立一份專案專屬的 UI 開發規範文件；使用者同意則呼叫該 skill 建立後，回頭依上述「有」的做法納入原則；使用者婉拒則略過此步驟，直接依原有 TDD 規劃進行

規劃前詢問使用者是否啟用 **Playwright 實機目視驗證**（可選步驟）：

> 實作完成後，是否要以 Playwright 自動進行實機目視驗證（啟動 dev server、逐頁截圖比對）？

- **啟用** → 計畫的最終任務加入「Playwright 實機目視驗證」：
  - 啟動本機開發伺服器（`npm run dev`）
  - 以 Playwright MCP 依實際使用者操作路徑（登入 → 導航）逐一到達所有受影響頁面
  - 逐頁截圖，比對本次變更的視覺與行為結果是否符合 spec；驗證用截圖閱後即刪，不入版控
  - 測試資料的選擇先詢問使用者是否有指定（不同資料的完整度可能不同）
  - 若環境沒有 Playwright 工具，改為列出手動驗證清單，請使用者自行確認後回報
- **不啟用** → 計畫的最終任務改為列出手動驗證清單，由使用者自行驗證

---

## 步驟六：建議使用 /goal 自動推進、退場

規劃完成後（`tasks.md` 已列出具體任務，此時**尚未開始任何實作**），告知使用者：

> 開發計畫已建立，`tasks.md` 列出待完成任務，尚未開始實作。是否要使用 `/goal` 讓 Claude 自動跨多輪推進這些任務（依 TDD 紅綠燈流程），直到全部完成、測試與 lint 皆通過為止？
>
> 1. **是** — 請直接複製以下指令並手動輸入啟動（`/goal` 為 Claude Code 原生指令，須由使用者親自輸入才會生效，agent 無法代為觸發）：
>    ```
>    /goal 完成 openspec/changes/<change-id>/tasks.md 裡所有任務（依 TDD 紅綠燈流程），npm test 全數通過，且本次新增／修改的檔案 lint 無 error（不含既有 lint error）
>    ```
>    （`<change-id>` 請代入本次實際的 change 名稱；lint 條件務必限定在本次新增／修改的檔案，不可寫成整專案零 error——若專案本身已有既有、與本次無關的 lint error，寫成整專案零 error 會讓這個 goal 永遠無法達成，且 agent 無法自行執行 `/goal clear` 解除）
> 2. **否** — 由使用者與 agent 在對話中逐項手動推進實作

無論使用者選擇為何，最後都告知：

> 完成開發（所有任務與測試皆通過）後，請手動呼叫 `/smart-commit` 執行 code review 與提交。

此 skill 退場，後續實作與 TDD 推進（含 `/goal` 執行期間）由使用者與 agent 自由進行，不受此 skill 約束。
