---
allowed-tools: "Bash(git add:*) Bash(git status:*) Bash(git diff:*) Bash(git commit:*) Bash(git log:*) Bash(git branch:*) Read Glob"
description: 自動偵測變更並參照專案 commit 規範發出 commit。優先讀取 CLAUDE.md 或 docs/commit-convention.md 的規範，若找不到則套用預設 Conventional Commits 格式。觸發時機：當使用者說「幫我 commit」、「發 commit」、「提交」、「git commit」、「存一個版本」、「幫我存檔」、「把這個 commit 起來」、「push 之前先 commit」，或任何需要建立 git commit 的情境時自動觸發，不需使用者手動輸入 /smart-commit。
disable-model-invocation: true
---

## 執行環境資訊

- 目前分支：!`git branch --show-current`
- Staged 變更：!`git diff --cached --stat`
- Unstaged 變更：!`git diff --stat`
- 未追蹤檔案：!`git status --short`
- 近期 commit 記錄（供參考風格）：!`git log --oneline -5`

---

## Stage 0：Code Review 前置審查

在提交前，先透過 `/code-review-expert` 對目前的 git 變更進行程式碼審查。

1. **調用 `code-review-expert` skill** 執行完整審查流程（SOLID、安全性、程式碼品質）
2. **審查完成後**，根據發現的問題等級決定後續動作：

   - **無任何問題（全部通過）** → 直接進入 Stage 1，不需詢問使用者
   - **發現任何問題（無論 P0 / P1 / P2 / P3）** → 列出每個問題的完整說明（問題描述、影響範圍、建議修正方式），**取代** code-review-expert 原本的「Next Steps」問句，改以以下選項詢問使用者：

     ```
     發現 X 個問題（P0: _, P1: _, P2: _, P3: _）。

     是否要在提交前先修復問題？

     1. 修復後再提交 — 請修復問題，完成後再執行 /smart-commit
     2. 直接提交 — 忽略問題，繼續進行提交
     3. 取消 — 中止本次提交流程
     ```

   - 若使用者選擇「修復後再提交」→ **停止流程**，等待使用者修復完成後重新執行
   - 若使用者選擇「直接提交」→ 繼續進入 Stage 1
   - 若使用者選擇「取消」→ **停止流程**，不執行任何 git 操作

---

## Stage 1：偵測 Commit 規範

依以下優先順序尋找規範：

1. 讀取 `CLAUDE.md`，尋找其中是否有 commit 規範章節或連結
2. 讀取 `docs/commit-convention.md`（若存在）
3. 若以上皆無，使用預設規範（見下方）

### 預設規範（fallback）

若專案內找不到任何規範，使用 Conventional Commits 格式：

```
<type>(<scope>): <subject>
```

| Type | 說明 |
|------|------|
| `feat` | 新增功能 |
| `fix` | 修復 bug |
| `refactor` | 重構（不影響功能） |
| `style` | 樣式或格式調整（不影響邏輯） |
| `chore` | 雜項維護（依賴更新、設定調整等） |
| `docs` | 文件變更 |
| `test` | 測試相關 |

範例：`feat(auth): 新增 Google OAuth 登入`

---

## Stage 2：分析變更內容

根據 `git diff HEAD` 與 `git status` 的輸出，理解：

- 哪些檔案被新增、修改、刪除
- 變更的核心目的（功能、修復、重構…）
- 影響範圍（哪個模組或功能域）

---

## Stage 3：套用規範產生 commit 訊息

根據 Stage 1 找到的規範（專案規範或預設規範），產生最符合的 commit 訊息：

- 主旨簡述本次變更，**不超過 15 字**（中文字符計算）
- 使用繁體中文描述（若專案規範未強制英文）
- 選擇最能描述「做了什麼、為什麼」的 type

---

## Stage 4：執行 commit

1. 若有 unstaged 的相關變更，先執行 `git add` 將其加入 staging
2. 使用 `git commit -m` 發出 commit
3. 不使用 `--no-verify`，讓 pre-commit hook 正常執行

請直接執行，不需向使用者確認 commit 訊息（除非變更內容不明確或有衝突風險）。

---

## 完成後回報

```
✅ Commit 完成

Code Review：[通過 / 有輕微問題 / 已確認忽略問題]
Commit hash：（提交後顯示）
變更檔案數：
Commit 訊息：
```
