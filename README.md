# my-skills

Cloris 的 Claude Code 個人 skill 集合。

---

## Skills 生態系架構

Claude Code 的 skill 系統分為三層：

```
┌─────────────────────────────────────────────────┐
│  Plugins（套件層）                               │
│  ~/.claude/plugins/                             │
│  安裝來源：marketplace（anthropics / 第三方）     │
│  包含多個 sub-skills，有版號管理                  │
├─────────────────────────────────────────────────┤
│  Skills（工作流層）                              │
│  ~/.claude/skills/                              │
│  安裝來源：GitHub repo（個人 / 第三方）           │
│  單一 SKILL.md 文件，定義觸發條件與執行流程        │
├─────────────────────────────────────────────────┤
│  Commands（指令層）                              │
│  ~/.claude/commands/                            │
│  手動建立的 slash commands（/command-name）       │
└─────────────────────────────────────────────────┘
```

---

## 本 Repo 的 Skills（個人自訂）

透過 `npx skills add cloris222/my-skills@<skill-name> -g -y` 安裝。

| Skill | 用途 | 觸發方式 |
|-------|------|----------|
| `smart-commit` | 提交前自動 code review，依專案規範產生 commit 訊息 | 自動（說「commit」即觸發）|
| `tdd-loop` | TDD 自動循環：寫測試 → 紅燈確認 → 修實作 → 綠燈，最多 5 次自我修正 | 自動（說「寫測試」即觸發）|
| `vue-test-writer` | 為 Vue 3 元件、store、utility 生成高品質 Vitest 測試案例 | 自動（說「幫我寫測試」即觸發）|
| `ui-builder` | 三階段 UI 開發流程（Plan → 實作 → 自我驗證），強制 i18n 與元件庫 API 確認 | 自動（說「建立 UI」即觸發）|
| `page-dev-workflow` | 整合 openspec + TDD 規劃的頁面開發完整流程 | **手動**（需明確呼叫 `/page-dev-workflow`）|
| `spec-sync` | 分析 git diff，判斷哪些改動需同步更新 openspec 規格文件 | **手動**（需明確呼叫 `/spec-sync`）|
| `setup-ui-guidelines` | 問答問卷，為新專案生成客製化 `ui-guidelines` 規範文件集 | **手動**（需明確呼叫 `/setup-ui-guidelines`，新專案初始化時執行一次）|

### Skills 之間的依賴關係

```
smart-commit
  └─ code-review-expert（第三方 skill，見下方）

tdd-loop
  └─ superpowers:test-driven-development（官方 plugin）

page-dev-workflow
  ├─ openspec（第三方 skill，見下方）
  ├─ superpowers:writing-plans（官方 plugin）
  └─ smart-commit（本 repo）

spec-sync
  └─ openspec（第三方 skill，見下方）

ui-builder
  ├─ docs/ui-guidelines/（專案規範文件，需先執行 setup-ui-guidelines 生成）
  └─ context7（第三方 plugin，供查詢元件庫 API）

setup-ui-guidelines
  └─ ui-builder/templates/（本 repo 內的 6 份模板文件）
```

---

## 必要的第三方 Plugins（官方 Marketplace）

來源：`anthropics/claude-plugins-official`

| Plugin | 用途 | 提供的 sub-skills |
|--------|------|-------------------|
| `superpowers` | AI 工作流核心套件 | brainstorming、writing-plans、test-driven-development、systematic-debugging、verification-before-completion、requesting-code-review、finishing-a-development-branch 等 |
| `code-review` | 程式碼審查指令 | `/code-review`、`/ultrareview` |
| `claude-md-management` | CLAUDE.md 管理 | `revise-claude-md`、`claude-md-improver` |
| `skill-creator` | 建立與改善 skill | `skill-creator` |
| `frontend-design` | 前端設計系統 | `frontend-design` |
| `context7` | 即時查詢函式庫文件 | `context7` MCP 工具 |
| `code-simplifier` | 程式碼簡化 | `/simplify` |
| `explanatory-output-style` | 教學型輸出模式 | 自動加入 Insight 說明區塊 |

安裝指令（每行執行一次）：

```bash
npx skills add anthropics/claude-plugins-official@superpowers -g -y
npx skills add anthropics/claude-plugins-official@code-review -g -y
npx skills add anthropics/claude-plugins-official@claude-md-management -g -y
npx skills add anthropics/claude-plugins-official@skill-creator -g -y
npx skills add anthropics/claude-plugins-official@frontend-design -g -y
npx skills add anthropics/claude-plugins-official@context7 -g -y
npx skills add anthropics/claude-plugins-official@code-simplifier -g -y
npx skills add anthropics/claude-plugins-official@explanatory-output-style -g -y
```

---

## 必要的第三方 Plugins（外部 Marketplace）

| Plugin | 來源 | 用途 |
|--------|------|------|
| `claude-reflect` v3.0.1 | `bayramannakov/claude-reflect` | 對話結束後反思並改善 skills |

安裝指令：

```bash
npx skills add bayramannakov/claude-reflect -g -y
```

---

## 必要的第三方 Skills

透過 `npx skills add` 安裝的獨立 skill 文件。

| Skill | 來源 | 用途 |
|-------|------|------|
| `code-review-expert` | `sanyuan0704/code-review-expert`（[marketplace](https://claudemarketplaces.com/skills/sanyuan0704/code-review-expert/code-review-expert)）| 進階程式碼審查（SOLID、安全性、效能），`smart-commit` 的依賴 |
| `openspec` | `TimLai666/installed-skills` | Spec-driven development CLI 整合 |
| `context7` skill | `dirnbauer/webconsulting-skills` | 函式庫文件查詢（skill 層） |

安裝指令：

```bash
npx skills add sanyuan0704/code-review-expert -g -y
npx skills add TimLai666/installed-skills@openspec -g -y
npx skills add dirnbauer/webconsulting-skills@context7 -g -y
```

---

## 必要的系統工具

| 工具 | 安裝方式 | 用途 |
|------|----------|------|
| `openspec` CLI | `npm install -g openspec` | spec-sync、page-dev-workflow 依賴 |

---

## 建議的 Hooks 設定（`~/.claude/settings.json`）

以下 hooks 讓 Claude Code 在特定事件自動執行動作。

### SessionStart — 強制語言

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo '{\"hookSpecificOutput\": {\"hookEventName\": \"SessionStart\", \"additionalContext\": \"本次對話所有回覆一律使用繁體中文。\"}}'",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

### Notification — macOS 系統通知（需要 Claude 請求確認時提示）

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "MSG=$(jq -r '.message // \"需要你的確認才能繼續\"' 2>/dev/null || echo '需要你的確認才能繼續') && osascript -e \"display notification \\\"$MSG\\\" with title \\\"Claude Code ⚠️\\\" sound name \\\"Glass\\\"\" 2>/dev/null || true",
            "async": true
          }
        ]
      }
    ]
  }
}
```

---

## 建議的專案層 Settings（`.claude/settings.local.json`）

放在專案根目錄的 `.claude/settings.local.json`，控制 Claude Code 在該專案的自動允許權限。

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(npm run *)",
      "Bash(npx vitest *)",
      "mcp__plugin_context7_context7__query-docs"
    ]
  }
}
```

---

## 新專案 Setup SOP

從零開始一個新專案，依序執行以下步驟：

### Step 1：確認前置環境

```bash
node --version       # 需要 18+
npx --version        # 確認 npx 可用
claude --version     # 確認 Claude Code CLI 已安裝
npm list -g openspec # 確認 openspec CLI 已安裝（若無：npm install -g openspec）
```

### Step 2：安裝官方 Plugins

```bash
npx skills add anthropics/claude-plugins-official@superpowers -g -y
npx skills add anthropics/claude-plugins-official@code-review -g -y
npx skills add anthropics/claude-plugins-official@claude-md-management -g -y
npx skills add anthropics/claude-plugins-official@skill-creator -g -y
npx skills add anthropics/claude-plugins-official@frontend-design -g -y
npx skills add anthropics/claude-plugins-official@context7 -g -y
npx skills add anthropics/claude-plugins-official@code-simplifier -g -y
npx skills add anthropics/claude-plugins-official@explanatory-output-style -g -y
```

### Step 3：安裝外部 Marketplace Plugin

```bash
npx skills add bayramannakov/claude-reflect -g -y
```

### Step 4：安裝個人 Skills（本 Repo）

```bash
npx skills add cloris222/my-skills@smart-commit -g -y
npx skills add cloris222/my-skills@tdd-loop -g -y
npx skills add cloris222/my-skills@vue-test-writer -g -y
npx skills add cloris222/my-skills@ui-builder -g -y
npx skills add cloris222/my-skills@page-dev-workflow -g -y
npx skills add cloris222/my-skills@spec-sync -g -y
npx skills add cloris222/my-skills@setup-ui-guidelines -g -y
```

### Step 5：安裝第三方 Skills

```bash
npx skills add sanyuan0704/code-review-expert -g -y
npx skills add TimLai666/installed-skills@openspec -g -y
npx skills add dirnbauer/webconsulting-skills@context7 -g -y
```

### Step 6：設定全域 CLAUDE.md（`~/.claude/CLAUDE.md`）

確認包含以下關鍵設定：

- **語言設定**：所有回覆使用繁體中文
- **Guardrail**：commit 前必須執行 `/smart-commit`
- **工具函式低耦合原則**：utilities 禁止 import Vue API

### Step 7：建立專案 CLAUDE.md（`<project>/CLAUDE.md`）

使用 `/init` 指令自動生成，或手動建立，包含：

- 專案概述與常用指令
- 架構總覽（路由、狀態管理、API 層）
- 部署流程
- 重要開發注意事項

### Step 8：設定專案權限（`.claude/settings.local.json`）

```bash
mkdir -p .claude
cat > .claude/settings.local.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(npm run *)",
      "Bash(npx vitest *)",
      "mcp__plugin_context7_context7__query-docs"
    ]
  }
}
EOF
```

根據專案需求調整 allow 清單（例如加入 `Bash(git *)`、`Bash(docker *)`）。

### Step 9：初始化 openspec（若使用 spec-driven 開發）

```bash
openspec init
```

### Step 10：確認所有 Skills 已載入

開啟 Claude Code，在對話中輸入：

```
請列出目前已載入的所有 skills
```

確認看到 `smart-commit`、`tdd-loop`、`vue-test-writer`、`ui-builder`、`page-dev-workflow`、`spec-sync`、`code-review-expert`、`openspec`、`superpowers:*` 等都在清單中。

---

## 更新 Skills

### 更新個人 Skills（本 Repo 有新版本時）

```bash
npx skills update -g
```

### 強制重裝特定 Skill

```bash
npx skills add cloris222/my-skills@smart-commit -g -y --force
```

### 檢查哪些 Skills 有更新

```bash
npx skills check
```

---

## 相關資源

- Skills 生態系：[skills.sh](https://skills.sh/)
- Claude Marketplaces：[claudemarketplaces.com](https://claudemarketplaces.com/)
- OpenSpec CLI：[openspec 文件](https://github.com/TimLai666/installed-skills)
- Claude Code 官方文件：[docs.anthropic.com](https://docs.anthropic.com/claude/docs/claude-code)
