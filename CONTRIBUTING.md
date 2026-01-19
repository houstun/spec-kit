# 为 Spec Kit 做贡献

您好！我们非常高兴您有兴趣为 Spec Kit 做贡献。对本项目的贡献将以[项目的开源许可证](LICENSE)向公众[发布](https://help.github.com/articles/github-terms-of-service/#6-contributions-under-repository-license)。

请注意，本项目发布时附带了[贡献者行为准则](CODE_OF_CONDUCT.md)。参与本项目即表示您同意遵守其条款。

## 运行和测试代码的前提条件

这些是一次性安装，用于在 pull request (PR) 提交过程中本地测试您的更改。

1. 安装 [Python 3.11+](https://www.python.org/downloads/)
1. 安装 [uv](https://docs.astral.sh/uv/) 用于包管理
1. 安装 [Git](https://git-scm.com/downloads)
1. 准备好一个[可用的 AI 编程 agent](README.md#-supported-ai-agents)

<details>
<summary><b>💡 如果您使用 <code>VSCode</code> 或 <code>GitHub Codespaces</code> 作为 IDE 的提示</b></summary>

<br>

如果您的机器上安装了 [Docker](https://docker.com)，您可以通过这个 [VSCode 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 来使用 [Dev Containers](https://containers.dev)，借助项目根目录下的 `.devcontainer/devcontainer.json` 文件，轻松设置您的开发环境，上述工具已预先安装和配置好。

只需执行以下步骤：

- 检出仓库
- 使用 VSCode 打开
- 打开[命令面板](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)并选择 "Dev Containers: Open Folder in Container..."

在 [GitHub Codespaces](https://github.com/features/codespaces) 上更加简单，因为它会在打开 codespace 时自动使用 `.devcontainer/devcontainer.json`。

</details>

## 提交 pull request

> [!NOTE]
> 如果您的 pull request 引入了对 CLI 或仓库其余部分有重大影响的大型更改（例如，您正在引入新的 templates、arguments 或其他重大更改），请确保这些更改已经与项目维护者**讨论并达成一致**。未经事先沟通和同意的大型更改 pull request 将被关闭。

1. Fork 并克隆仓库
1. 配置并安装依赖：`uv sync`
1. 确保 CLI 在您的机器上正常工作：`uv run specify --help`
1. 创建新分支：`git checkout -b my-branch-name`
1. 进行更改、添加测试，并确保一切仍然正常工作
1. 如果相关，使用示例项目测试 CLI 功能
1. 推送到您的 fork 并提交 pull request
1. 等待您的 pull request 被审查和合并。

以下是一些可以增加您的 pull request 被接受可能性的建议：

- 遵循项目的编码规范。
- 为新功能编写测试。
- 如果您的更改影响面向用户的功能，请更新文档（`README.md`、`spec-driven.md`）。
- 保持更改尽可能集中。如果您想进行多个相互独立的更改，请考虑将它们作为单独的 pull request 提交。
- 编写[良好的 commit message](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)。
- 使用 Spec-Driven Development 工作流测试您的更改以确保兼容性。

## 开发工作流

在开发 spec-kit 时：

1. 在您选择的编程 agent 中使用 `specify` CLI 命令（`/speckit.specify`、`/speckit.plan`、`/speckit.tasks`）测试更改
2. 验证 `templates/` 目录中的 templates 是否正常工作
3. 测试 `scripts/` 目录中的脚本功能
4. 如果进行了重大流程更改，请确保更新 memory 文件（`memory/constitution.md`）

### 在本地测试 template 和 command 更改

运行 `uv run specify init` 会拉取已发布的包，不会包含您的本地更改。
要在本地测试您的 templates、commands 和其他更改，请按照以下步骤操作：

1. **创建发布包**

   运行以下命令生成本地包：

   ```bash
   ./.github/workflows/scripts/create-release-packages.sh v1.0.0
   ```

2. **将相关包复制到您的测试项目**

   ```bash
   cp -r .genreleases/sdd-copilot-package-sh/. <path-to-test-project>/
   ```

3. **打开并测试 agent**

   导航到您的测试项目文件夹并打开 agent 以验证您的实现。

## Spec Kit 中的 AI 贡献

> [!IMPORTANT]
>
> 如果您在为 Spec Kit 做贡献时使用了**任何形式的 AI 辅助**，
> 必须在 pull request 或 issue 中披露。

我们欢迎并鼓励使用 AI 工具来帮助改进 Spec Kit！许多有价值的贡献都通过 AI 辅助在代码生成、问题检测和功能定义方面得到了增强。

话虽如此，如果您在为 Spec Kit 做贡献时使用了任何形式的 AI 辅助（例如 agents、ChatGPT），
**必须在 pull request 或 issue 中披露**，并说明 AI 辅助的使用程度（例如，文档注释与代码生成）。

如果您的 PR 回复或评论是由 AI 生成的，也请披露。

作为例外，微小的空格或拼写错误修复不需要披露，只要更改仅限于代码的小部分或短语。

披露示例：

> 这个 PR 主要由 GitHub Copilot 编写。

或更详细的披露：

> 我咨询了 ChatGPT 来理解代码库，但解决方案
> 完全由我自己手动编写。

不披露首先是对 pull request 另一端的人类操作者的不尊重，而且也使得难以确定对贡献应施加多少审查力度。

在理想世界中，AI 辅助会产生与任何人类同等或更高质量的工作。但这不是我们今天生活的世界，在大多数没有人类监督或专业知识参与的情况下，它生成的代码无法被合理地维护或演进。

### 我们寻找的内容

提交 AI 辅助贡献时，请确保包含：

- **明确披露 AI 使用** - 您对 AI 使用及其在贡献中的使用程度保持透明
- **人类理解和测试** - 您已亲自测试更改并理解它们的作用
- **清晰的理由** - 您能解释为什么需要这个更改以及它如何符合 Spec Kit 的目标
- **具体证据** - 包括测试用例、场景或示例来展示改进
- **您自己的分析** - 分享您对端到端开发者体验的想法

### 我们会关闭的内容

我们保留关闭以下贡献的权利：

- 未经验证就提交的未测试更改
- 不针对特定 Spec Kit 需求的通用建议
- 显示没有人类审查或理解的批量提交

### 成功指南

关键是展示您理解并验证了您提议的更改。如果维护者能轻易看出贡献完全由 AI 生成，没有人类输入或测试，那么它在提交前可能需要更多工作。

持续提交低质量 AI 生成更改的贡献者可能会在维护者的酌情下被限制进一步贡献。

请尊重维护者并披露 AI 辅助。

## 资源

- [Spec-Driven Development 方法论](./spec-driven.md)
- [如何为开源做贡献](https://opensource.guide/how-to-contribute/)
- [使用 Pull Requests](https://help.github.com/articles/about-pull-requests/)
- [GitHub 帮助](https://help.github.com)
