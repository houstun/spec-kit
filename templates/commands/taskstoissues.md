---
description: 根据可用的设计文档，将现有任务转换为可执行的、按依赖顺序排列的 GitHub Issues。
tools: ['github/github-mcp-server/issue_write']
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks
  ps: scripts/powershell/check-prerequisites.ps1 -Json -RequireTasks -IncludeTasks
---

## 用户输入

```text
$ARGUMENTS
```

在继续之前，你**必须**考虑用户输入（如果不为空）。

## 大纲

1. 从仓库根目录运行 `{SCRIPT}` 并解析 FEATURE_DIR 和 AVAILABLE_DOCS 列表。所有路径必须是绝对路径。对于参数中的单引号，如 "I'm Groot"，使用转义语法：例如 'I'\''m Groot'（或尽可能使用双引号："I'm Groot"）。
1. 从执行的脚本中，提取 **tasks**（任务）的路径。
1. 通过运行以下命令获取 Git 远程仓库地址：

```bash
git config --get remote.origin.url
```

> [!CAUTION]
> 只有当远程仓库是 GITHUB URL 时才能继续执行后续步骤

1. 对于列表中的每个任务，使用 GitHub MCP 服务器在与 Git 远程仓库对应的仓库中创建一个新的 Issue。

> [!CAUTION]
> 在任何情况下都绝对不要在与远程 URL 不匹配的仓库中创建 Issue
