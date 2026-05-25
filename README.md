# my-skills

Claude Code 個人 skill 集合，透過 `gh skill install` 管理。

## 安裝

```bash
gh skill install cloris222/my-skills --agent claude-code --scope user
```

## Skills

| Skill | 用途 |
|-------|------|
| `smart-commit` | 提交前自動 code review，依專案規範產生 commit 訊息 |
| `tdd-loop` | TDD 自動循環：寫測試 → 紅燈確認 → 修實作 → 綠燈，最多 5 次自我修正 |
| `vue-test-writer` | 為 Vue 3 元件、store、utility 生成高品質 Vitest 測試案例 |

## 依賴

| Skill | 外部依賴 |
|-------|----------|
| `smart-commit` | 需安裝 `code-review` 插件（提供 `code-review-expert`） |
| `tdd-loop` | 需安裝 `superpowers` 插件（提供 `test-driven-development`） |
| `vue-test-writer` | 無外部依賴 |

## 更新

```bash
gh skill update --agent claude-code --scope user
```
