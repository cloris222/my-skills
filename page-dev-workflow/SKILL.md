---
name: page-dev-workflow
description: 手動呼叫專用，不自動觸發。整合 openspec、spec 討論、superpowers TDD 規劃的頁面開發標準工作流程。僅在使用者明確以 /page-dev-workflow 呼叫時啟動。
metadata:
    github-path: page-dev-workflow
    github-ref: refs/heads/main
    github-repo: https://github.com/cloris222/my-skills
---

# 頁面開發標準工作流程

整合 openspec、spec 討論、superpowers 規劃，以 TDD 模式驅動頁面開發的完整流程。

**此 skill 手動呼叫專用**，請勿在對話途中自動啟動。

> **依賴 skills**：`openspec`、`superpowers:writing-plans`、`superpowers:test-driven-development`、`smart-commit`

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

根據收集到的素材，與使用者討論，逐步完善 spec 文件（`proposal.md`、`tasks.md`，必要時補充 `design.md`）。

> **重要**：等待使用者明確表示「spec 確認完成」後才進入下一步，不可自行假設 spec 已完成。

---

## 步驟五：規劃開發流程（TDD）

呼叫 `superpowers:writing-plans` skill，依據完成的 spec 規劃實作計畫。

規劃時明確指定以 **TDD 模式**實作：
- 每個功能單元先寫測試，測試通過才算完成
- 呼叫 `superpowers:test-driven-development` 指導 TDD 流程執行

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

## 步驟六：退場

規劃完成後，告知使用者：

> 開發計畫已建立，進入實作階段。
> 完成開發後，請手動呼叫 `/smart-commit` 執行 code review 與提交。

此 skill 退場，後續開發討論由使用者與 agent 自由進行，不受此 skill 約束。
