# AGENTS.md

## 关于 Spec Kit 和 Specify

**GitHub Spec Kit** 是一个用于实现规格驱动开发（Spec-Driven Development, SDD）的综合工具包——这是一种强调在实现之前创建清晰规格说明的方法论。该工具包包含模板、脚本和工作流，引导开发团队采用结构化的方法来构建软件。

**Specify CLI** 是一个命令行界面，用于使用 Spec Kit 框架初始化项目。它设置必要的目录结构、模板和 AI 代理集成，以支持规格驱动开发工作流。

该工具包支持多种 AI 编码助手，允许团队使用他们偏好的工具，同时保持一致的项目结构和开发实践。

---

## 通用实践

- 对 Specify CLI 的 `__init__.py` 进行的任何更改都需要在 `pyproject.toml` 中更新版本号，并在 `CHANGELOG.md` 中添加相应条目。

## 添加新的代理支持

本节说明如何向 Specify CLI 添加对新 AI 代理/助手的支持。在将新 AI 工具集成到规格驱动开发工作流时，请将本指南作为参考。

### 概述

Specify 通过在初始化项目时生成特定于代理的命令文件和目录结构来支持多种 AI 代理。每个代理都有自己的约定：

- **命令文件格式**（Markdown、TOML 等）
- **目录结构**（`.claude/commands/`、`.windsurf/workflows/` 等）
- **命令调用模式**（斜杠命令、CLI 工具等）
- **参数传递约定**（`$ARGUMENTS`、`{{args}}` 等）

### 当前支持的代理

| 代理                       | 目录                   | 格式     | CLI 工具        | 描述                        |
| -------------------------- | ---------------------- | -------- | --------------- | --------------------------- |
| **Claude Code**            | `.claude/commands/`    | Markdown | `claude`        | Anthropic 的 Claude Code CLI |
| **Gemini CLI**             | `.gemini/commands/`    | TOML     | `gemini`        | Google 的 Gemini CLI         |
| **GitHub Copilot**         | `.github/agents/`      | Markdown | N/A（基于 IDE） | VS Code 中的 GitHub Copilot   |
| **Cursor**                 | `.cursor/commands/`    | Markdown | `cursor-agent`  | Cursor CLI                  |
| **Qwen Code**              | `.qwen/commands/`      | TOML     | `qwen`          | 阿里巴巴的 Qwen Code CLI     |
| **opencode**               | `.opencode/command/`   | Markdown | `opencode`      | opencode CLI                |
| **Codex CLI**              | `.codex/commands/`     | Markdown | `codex`         | Codex CLI                   |
| **Windsurf**               | `.windsurf/workflows/` | Markdown | N/A（基于 IDE） | Windsurf IDE 工作流          |
| **Kilo Code**              | `.kilocode/rules/`     | Markdown | N/A（基于 IDE） | Kilo Code IDE               |
| **Auggie CLI**             | `.augment/rules/`      | Markdown | `auggie`        | Auggie CLI                  |
| **Roo Code**               | `.roo/rules/`          | Markdown | N/A（基于 IDE） | Roo Code IDE                |
| **CodeBuddy CLI**          | `.codebuddy/commands/` | Markdown | `codebuddy`     | CodeBuddy CLI               |
| **Qoder CLI**              | `.qoder/commands/`     | Markdown | `qoder`         | Qoder CLI                   |
| **Amazon Q Developer CLI** | `.amazonq/prompts/`    | Markdown | `q`             | Amazon Q Developer CLI      |
| **Amp**                    | `.agents/commands/`    | Markdown | `amp`           | Amp CLI                     |
| **SHAI**                   | `.shai/commands/`      | Markdown | `shai`          | SHAI CLI                    |
| **IBM Bob**                | `.bob/commands/`       | Markdown | N/A（基于 IDE） | IBM Bob IDE                 |

### 逐步集成指南

按照以下步骤添加新代理（以假设的新代理为例）：

#### 1. 添加到 AGENT_CONFIG

**重要提示**：使用实际的 CLI 工具名称作为键，而不是缩写版本。

在 `src/specify_cli/__init__.py` 的 `AGENT_CONFIG` 字典中添加新代理。这是所有代理元数据的**唯一真实来源**：

```python
AGENT_CONFIG = {
    # ... 现有代理 ...
    "new-agent-cli": {  # 使用实际的 CLI 工具名称（用户在终端中输入的内容）
        "name": "New Agent Display Name",
        "folder": ".newagent/",  # 代理文件的目录
        "install_url": "https://example.com/install",  # 安装文档的 URL（如果是基于 IDE 的则为 None）
        "requires_cli": True,  # 如果需要 CLI 工具则为 True，基于 IDE 的代理则为 False
    },
}
```

**关键设计原则**：字典键应与用户安装的实际可执行文件名称匹配。例如：

- ✅ 使用 `"cursor-agent"` 因为 CLI 工具的名称就是 `cursor-agent`
- ❌ 如果工具是 `cursor-agent`，不要使用 `"cursor"` 作为快捷方式

这消除了在整个代码库中进行特殊情况映射的需要。

**字段说明**：

- `name`：向用户显示的人类可读的显示名称
- `folder`：存储代理特定文件的目录（相对于项目根目录）
- `install_url`：安装文档 URL（对于基于 IDE 的代理设置为 `None`）
- `requires_cli`：代理在初始化期间是否需要 CLI 工具检查

#### 2. 更新 CLI 帮助文本

更新 `init()` 命令中 `--ai` 参数的帮助文本以包含新代理：

```python
ai_assistant: str = typer.Option(None, "--ai", help="AI assistant to use: claude, gemini, copilot, cursor-agent, qwen, opencode, codex, windsurf, kilocode, auggie, codebuddy, new-agent-cli, or q"),
```

同时更新列出可用代理的任何函数文档字符串、示例和错误消息。

#### 3. 更新 README 文档

更新 `README.md` 中的 **Supported AI Agents** 部分以包含新代理：

- 将新代理添加到表格中，并标注适当的支持级别（Full/Partial）
- 包含代理的官方网站链接
- 添加有关代理实现的任何相关注释
- 确保表格格式保持对齐和一致

#### 4. 更新发布包脚本

修改 `.github/workflows/scripts/create-release-packages.sh`：

##### 添加到 ALL_AGENTS 数组

```bash
ALL_AGENTS=(claude gemini copilot cursor-agent qwen opencode windsurf q)
```

##### 为目录结构添加 case 语句

```bash
case $agent in
  # ... 现有案例 ...
  windsurf)
    mkdir -p "$base_dir/.windsurf/workflows"
    generate_commands windsurf md "\$ARGUMENTS" "$base_dir/.windsurf/workflows" "$script" ;;
esac
```

#### 4. 更新 GitHub 发布脚本

修改 `.github/workflows/scripts/create-github-release.sh` 以包含新代理的包：

```bash
gh release create "$VERSION" \
  # ... 现有包 ...
  .genreleases/spec-kit-template-windsurf-sh-"$VERSION".zip \
  .genreleases/spec-kit-template-windsurf-ps-"$VERSION".zip \
  # 在此处添加新代理包
```

#### 5. 更新代理上下文脚本

##### Bash 脚本（`scripts/bash/update-agent-context.sh`）

添加文件变量：

```bash
WINDSURF_FILE="$REPO_ROOT/.windsurf/rules/specify-rules.md"
```

添加到 case 语句：

```bash
case "$AGENT_TYPE" in
  # ... 现有案例 ...
  windsurf) update_agent_file "$WINDSURF_FILE" "Windsurf" ;;
  "")
    # ... 现有检查 ...
    [ -f "$WINDSURF_FILE" ] && update_agent_file "$WINDSURF_FILE" "Windsurf";
    # 更新默认创建条件
    ;;
esac
```

##### PowerShell 脚本（`scripts/powershell/update-agent-context.ps1`）

添加文件变量：

```powershell
$windsurfFile = Join-Path $repoRoot '.windsurf/rules/specify-rules.md'
```

添加到 switch 语句：

```powershell
switch ($AgentType) {
    # ... 现有案例 ...
    'windsurf' { Update-AgentFile $windsurfFile 'Windsurf' }
    '' {
        foreach ($pair in @(
            # ... 现有配对 ...
            @{file=$windsurfFile; name='Windsurf'}
        )) {
            if (Test-Path $pair.file) { Update-AgentFile $pair.file $pair.name }
        }
        # 更新默认创建条件
    }
}
```

#### 6. 更新 CLI 工具检查（可选）

对于需要 CLI 工具的代理，在 `check()` 命令和代理验证中添加检查：

```python
# 在 check() 命令中
tracker.add("windsurf", "Windsurf IDE (optional)")
windsurf_ok = check_tool_for_tracker("windsurf", "https://windsurf.com/", tracker)

# 在 init 验证中（仅当需要 CLI 工具时）
elif selected_ai == "windsurf":
    if not check_tool("windsurf", "Install from: https://windsurf.com/"):
        console.print("[red]Error:[/red] Windsurf CLI is required for Windsurf projects")
        agent_tool_missing = True
```

**注意**：CLI 工具检查现在根据 AGENT_CONFIG 中的 `requires_cli` 字段自动处理。无需在 `check()` 或 `init()` 命令中进行额外的代码更改——它们会自动循环遍历 AGENT_CONFIG 并根据需要检查工具。

## 重要设计决策

### 使用实际 CLI 工具名称作为键

**关键提示**：向 AGENT_CONFIG 添加新代理时，始终使用**实际的可执行文件名称**作为字典键，而不是缩写或便捷版本。

**为什么这很重要：**

- `check_tool()` 函数使用 `shutil.which(tool)` 在系统 PATH 中查找可执行文件
- 如果键与实际的 CLI 工具名称不匹配，您将需要在整个代码库中进行特殊情况映射
- 这会造成不必要的复杂性和维护负担

**示例 - Cursor 的教训：**

❌ **错误做法**（需要特殊情况映射）：

```python
AGENT_CONFIG = {
    "cursor": {  # 与实际工具不匹配的简写
        "name": "Cursor",
        # ...
    }
}

# 然后到处都需要特殊情况：
cli_tool = agent_key
if agent_key == "cursor":
    cli_tool = "cursor-agent"  # 映射到真实的工具名称
```

✅ **正确做法**（无需映射）：

```python
AGENT_CONFIG = {
    "cursor-agent": {  # 匹配实际的可执行文件名称
        "name": "Cursor",
        # ...
    }
}

# 无需特殊情况——直接使用 agent_key！
```

**这种方法的好处：**

- 消除散布在整个代码库中的特殊情况逻辑
- 使代码更易于维护和理解
- 减少添加新代理时出现错误的机会
- 工具检查"开箱即用"，无需额外映射

#### 7. 更新 Devcontainer 文件（可选）

对于具有 VS Code 扩展或需要 CLI 安装的代理，更新 devcontainer 配置文件：

##### 基于 VS Code 扩展的代理

对于可作为 VS Code 扩展使用的代理，将它们添加到 `.devcontainer/devcontainer.json`：

```json
{
  "customizations": {
    "vscode": {
      "extensions": [
        // ... 现有扩展 ...
        // [新代理名称]
        "[新代理扩展 ID]"
      ]
    }
  }
}
```

##### 基于 CLI 的代理

对于需要 CLI 工具的代理，将安装命令添加到 `.devcontainer/post-create.sh`：

```bash
#!/bin/bash

# 现有安装...

echo -e "\n🤖 Installing [New Agent Name] CLI..."
# run_command "npm install -g [agent-cli-package]@latest" # 基于 node 的 CLI 示例
# 或其他安装说明（必须是非交互式的，并与 Linux Debian "Trixie" 或更高版本兼容）...
echo "✅ Done"

```

**快速提示：**

- **基于扩展的代理**：添加到 `devcontainer.json` 的 `extensions` 数组中
- **基于 CLI 的代理**：将安装脚本添加到 `post-create.sh`
- **混合代理**：可能需要同时安装扩展和 CLI
- **彻底测试**：确保安装在 devcontainer 环境中正常工作

## 代理类别

### 基于 CLI 的代理

需要安装命令行工具：

- **Claude Code**：`claude` CLI
- **Gemini CLI**：`gemini` CLI
- **Cursor**：`cursor-agent` CLI
- **Qwen Code**：`qwen` CLI
- **opencode**：`opencode` CLI
- **Amazon Q Developer CLI**：`q` CLI
- **CodeBuddy CLI**：`codebuddy` CLI
- **Qoder CLI**：`qoder` CLI
- **Amp**：`amp` CLI
- **SHAI**：`shai` CLI

### 基于 IDE 的代理

在集成开发环境中工作：

- **GitHub Copilot**：内置于 VS Code/兼容的编辑器
- **Windsurf**：内置于 Windsurf IDE
- **IBM Bob**：内置于 IBM Bob IDE

## 命令文件格式

### Markdown 格式

使用者：Claude、Cursor、opencode、Windsurf、Amazon Q Developer、Amp、SHAI、IBM Bob

**标准格式：**

```markdown
---
description: "命令描述"
---

包含 {SCRIPT} 和 $ARGUMENTS 占位符的命令内容。
```

**GitHub Copilot 聊天模式格式：**

```markdown
---
description: "命令描述"
mode: speckit.command-name
---

包含 {SCRIPT} 和 $ARGUMENTS 占位符的命令内容。
```

### TOML 格式

使用者：Gemini、Qwen

```toml
description = "命令描述"

prompt = """
包含 {SCRIPT} 和 {{args}} 占位符的命令内容。
"""
```

## 目录约定

- **CLI 代理**：通常是 `.<agent-name>/commands/`
- **IDE 代理**：遵循 IDE 特定的模式：
  - Copilot：`.github/agents/`
  - Cursor：`.cursor/commands/`
  - Windsurf：`.windsurf/workflows/`

## 参数模式

不同的代理使用不同的参数占位符：

- **基于 Markdown/提示词的**：`$ARGUMENTS`
- **基于 TOML 的**：`{{args}}`
- **脚本占位符**：`{SCRIPT}`（替换为实际脚本路径）
- **代理占位符**：`__AGENT__`（替换为代理名称）

## 测试新代理集成

1. **构建测试**：在本地运行包创建脚本
2. **CLI 测试**：测试 `specify init --ai <agent>` 命令
3. **文件生成**：验证正确的目录结构和文件
4. **命令验证**：确保生成的命令与代理正常工作
5. **上下文更新**：测试代理上下文更新脚本

## 常见陷阱

1. **使用简写键而不是实际的 CLI 工具名称**：始终使用实际的可执行文件名称作为 AGENT_CONFIG 键（例如 `"cursor-agent"` 而不是 `"cursor"`）。这可以避免在整个代码库中进行特殊情况映射。
2. **忘记更新脚本**：添加新代理时必须同时更新 bash 和 PowerShell 脚本。
3. **`requires_cli` 值不正确**：仅对实际具有 CLI 工具需要检查的代理设置为 `True`；对于基于 IDE 的代理设置为 `False`。
4. **参数格式错误**：为每种代理类型使用正确的占位符格式（Markdown 使用 `$ARGUMENTS`，TOML 使用 `{{args}}`）。
5. **目录命名**：严格遵循代理特定的约定（查看现有代理的模式）。
6. **帮助文本不一致**：一致地更新所有面向用户的文本（帮助字符串、文档字符串、README、错误消息）。

## 未来考虑

添加新代理时：

- 考虑代理的原生命令/工作流模式
- 确保与规格驱动开发流程的兼容性
- 记录任何特殊要求或限制
- 用所学到的经验更新本指南
- 在添加到 AGENT_CONFIG 之前验证实际的 CLI 工具名称

---

*每当添加新代理时，应更新此文档以保持准确性和完整性。*
