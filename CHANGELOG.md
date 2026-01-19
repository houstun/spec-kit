# 更新日志

<!-- markdownlint-disable MD024 -->

Specify CLI 和模板的所有重要更改都记录在此文档中。

格式基于 [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)，
本项目遵循 [语义化版本](https://semver.org/spec/v2.0.0.html)。

## [0.0.22] - 2025-11-07

- 支持 VS Code/Copilot 代理，从提示词转向具有任务移交功能的正式代理。
- 改为使用 `AGENTS.md` 用于 Copilot 工作负载，因为它已经开箱即用。
- 添加版本命令支持。([#486](https://github.com/github/spec-kit/issues/486))
- 修复 `create-new-feature.ps1` 脚本中的潜在错误，该错误在确定下一个功能编号时会忽略现有的功能分支 ([#975](https://github.com/github/spec-kit/issues/975))
- 为模板获取期间的 GitHub API 速率限制添加优雅降级和日志记录 ([#970](https://github.com/github/spec-kit/issues/970))

## [0.0.21] - 2025-10-21

- 修复 [#975](https://github.com/github/spec-kit/issues/975)（感谢 [@fgalarraga](https://github.com/fgalarraga)）。
- 添加 Amp CLI 支持。
- 添加 VS Code 任务移交支持，并将提示词升级为完整的聊天模式。
- 添加 `version` 命令支持（解决 [#811](https://github.com/github/spec-kit/issues/811) 和 [#486](https://github.com/github/spec-kit/issues/486)，感谢 [@mcasalaina](https://github.com/mcasalaina) 和 [@dentity007](https://github.com/dentity007)）。
- 添加 CLI 遇到速率限制错误时的渲染支持 ([#970](https://github.com/github/spec-kit/issues/970)，感谢 [@psmman](https://github.com/psmman))。

## [0.0.20] - 2025-10-14

### 新增

- **智能分支命名**：`create-new-feature` 脚本现在支持 `--short-name` 参数用于自定义分支名称
  - 当提供 `--short-name` 时：直接使用自定义名称（经过清理和格式化）
  - 当省略时：使用停用词过滤和长度过滤自动生成有意义的名称
  - 过滤常见停用词（I、want、to、the、for 等）
  - 移除少于 3 个字符的单词（除非是大写首字母缩写）
  - 从描述中提取 3-4 个最有意义的单词
  - **强制执行 GitHub 的 244 字节分支名称限制**，自动截断并发出警告
  - 示例：
    - "I want to create user authentication" → `001-create-user-authentication`
    - "Implement OAuth2 integration for API" → `001-implement-oauth2-integration-api`
    - "Fix payment processing bug" → `001-fix-payment-processing`
    - 超长描述会在单词边界自动截断以保持在限制范围内
  - 设计用于 AI 代理提供语义化短名称，同时保持独立可用性

### 变更

- 增强了 `create-new-feature.sh` 和 `create-new-feature.ps1` 脚本的帮助文档，添加了示例
- 分支名称现在会根据 GitHub 的 244 字节限制进行验证，必要时自动截断

## [0.0.19] - 2025-10-10

### 新增

- 支持 CodeBuddy（感谢 [@lispking](https://github.com/lispking) 的贡献）。
- 现在可以在 Specify CLI 中查看 Git 来源的错误。

### 变更

- 修复了 `plan.md` 中宪法文件的路径（感谢 [@lyzno1](https://github.com/lyzno1) 发现此问题）。
- 修复了 Gemini 生成的 TOML 文件中的反斜杠转义问题（感谢 [@hsin19](https://github.com/hsin19) 的贡献）。
- 实现命令现在确保添加正确的忽略文件（感谢 [@sigent-amazon](https://github.com/sigent-amazon) 的贡献）。

## [0.0.18] - 2025-10-06

### 新增

- 支持在 `specify init .` 命令中使用 `.` 作为当前目录的简写，等同于 `--here` 标志，但对用户更直观。
- 使用 `/speckit.` 命令前缀可以轻松发现与 Spec Kit 相关的命令。
- 重构提示词和模板以简化其功能及追踪方式。不再在不需要测试时污染内容。
- 确保每个用户故事创建任务（简化测试和验证）。
- 添加对 Visual Studio Code 提示快捷方式和自动脚本执行的支持。

### 变更

- 所有命令文件现在都以 `speckit.` 为前缀（例如 `speckit.specify.md`、`speckit.plan.md`），以便在 IDE/CLI 命令面板和文件浏览器中更好地发现和区分

## [0.0.17] - 2025-09-22

### 新增

- 新的 `/clarify` 命令模板，用于针对现有规格提出最多 5 个有针对性的澄清问题，并将答案保存到规格的澄清部分。
- 新的 `/analyze` 命令模板，提供非破坏性的跨工件差异和对齐报告（规格、澄清、计划、任务、宪法），插入在 `/tasks` 之后和 `/implement` 之前。
  - 注意：宪法规则被明确视为不可协商的；任何冲突都是需要工件修复的严重发现，而不是弱化原则。

## [0.0.16] - 2025-09-22

### 新增

- `init` 命令的 `--force` 标志，用于在非空目录中使用 `--here` 时绕过确认，直接进行合并/覆盖文件。

## [0.0.15] - 2025-09-21

### 新增

- 支持 Roo Code。

## [0.0.14] - 2025-09-21

### 变更

- 错误消息现在显示一致。

## [0.0.13] - 2025-09-21

### 新增

- 支持 Kilo Code。感谢 [@shahrukhkhan489](https://github.com/shahrukhkhan489) 在 [#394](https://github.com/github/spec-kit/pull/394) 中的贡献。
- 支持 Auggie CLI。感谢 [@hungthai1401](https://github.com/hungthai1401) 在 [#137](https://github.com/github/spec-kit/pull/137) 中的贡献。
- 项目配置完成后显示代理文件夹安全提示，警告用户某些代理可能在其代理文件夹中存储凭据或认证令牌，建议将相关文件夹添加到 `.gitignore` 以防止意外的凭据泄露。

### 变更

- 显示警告以确保用户知道他们可能需要将代理文件夹添加到 `.gitignore`。
- 清理了 `check` 命令的输出。

## [0.0.12] - 2025-09-21

### 变更

- 为 OpenAI Codex 用户添加了额外的上下文 - 他们需要设置额外的环境变量，如 [#417](https://github.com/github/spec-kit/issues/417) 中所述。

## [0.0.11] - 2025-09-20

### 新增

- Codex CLI 支持（感谢 [@honjo-hiroaki-gtt](https://github.com/honjo-hiroaki-gtt) 在 [#14](https://github.com/github/spec-kit/pull/14) 中的贡献）
- Codex 感知的上下文更新工具（Bash 和 PowerShell），使功能计划可以在不手动编辑的情况下刷新 `AGENTS.md` 以及现有的助手。

## [0.0.10] - 2025-09-20

### 修复

- 解决了 [#378](https://github.com/github/spec-kit/issues/378)，其中 GitHub 令牌可能在为空时附加到请求中。

## [0.0.9] - 2025-09-19

### 变更

- 改进了代理选择器 UI，使用青色高亮显示代理键名，灰色括号显示全名

## [0.0.8] - 2025-09-19

### 新增

- Windsurf IDE 支持作为额外的 AI 助手选项（感谢 [@raedkit](https://github.com/raedkit) 在 [#151](https://github.com/github/spec-kit/pull/151) 中的工作）
- GitHub 令牌支持 API 请求，以处理企业环境和速率限制（由 [@zryfish](https://github.com/@zryfish) 在 [#243](https://github.com/github/spec-kit/pull/243) 中贡献）

### 变更

- 更新 README 添加 Windsurf 示例和 GitHub 令牌使用说明
- 增强发布工作流以包含 Windsurf 模板

## [0.0.7] - 2025-09-18

### 变更

- 更新了 CLI 中的命令说明。
- 清理代码，在通用信息时不渲染代理特定信息。

## [0.0.6] - 2025-09-17

### 新增

- opencode 支持作为额外的 AI 助手选项

## [0.0.5] - 2025-09-17

### 新增

- Qwen Code 支持作为额外的 AI 助手选项

## [0.0.4] - 2025-09-14

### 新增

- 通过 `httpx[socks]` 依赖项支持企业环境的 SOCKS 代理

### 修复

无

### 变更

无
