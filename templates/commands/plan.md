---
description: 执行实现计划工作流程，使用计划模板生成设计产物。
handoffs:
  - label: 创建任务
    agent: speckit.tasks
    prompt: 将计划拆分为任务
    send: true
  - label: 创建检查清单
    agent: speckit.checklist
    prompt: 为以下领域创建检查清单...
scripts:
  sh: scripts/bash/setup-plan.sh --json
  ps: scripts/powershell/setup-plan.ps1 -Json
agent_scripts:
  sh: scripts/bash/update-agent-context.sh __AGENT__
  ps: scripts/powershell/update-agent-context.ps1 -AgentType __AGENT__
---

## 用户输入

```text
$ARGUMENTS
```

你**必须**在继续之前考虑用户输入（如果不为空）。

## 大纲

1. **设置**：从仓库根目录运行 `{SCRIPT}` 并解析 JSON 获取 FEATURE_SPEC、IMPL_PLAN、SPECS_DIR、BRANCH。对于参数中的单引号，如 "I'm Groot"，使用转义语法：例如 'I'\''m Groot'（或尽可能使用双引号："I'm Groot"）。

2. **加载上下文**：读取 FEATURE_SPEC 和 `/memory/constitution.md`。加载 IMPL_PLAN 模板（已复制）。

3. **执行计划工作流**：按照 IMPL_PLAN 模板的结构：
   - 填写技术上下文（将未知项标记为"需要澄清"）
   - 从宪章填写宪章检查部分
   - 评估关卡（如果违规未得到合理解释则报错）
   - 阶段 0：生成 research.md（解决所有"需要澄清"项）
   - 阶段 1：生成 data-model.md、contracts/、quickstart.md
   - 阶段 1：通过运行代理脚本更新代理上下文
   - 设计后重新评估宪章检查

4. **停止并报告**：命令在阶段 2 计划后结束。报告分支、IMPL_PLAN 路径和生成的产物。

## 阶段

### 阶段 0：大纲与研究

1. **从上述技术上下文中提取未知项**：
   - 对于每个"需要澄清"项 → 研究任务
   - 对于每个依赖项 → 最佳实践任务
   - 对于每个集成项 → 模式任务

2. **生成并分派研究代理**：

   ```text
   对于技术上下文中的每个未知项：
     任务："研究 {feature context} 中的 {unknown}"
   对于每个技术选择：
     任务："查找 {domain} 中 {tech} 的最佳实践"
   ```

3. **整合发现** 到 `research.md`，使用以下格式：
   - 决策：[选择了什么]
   - 理由：[为什么选择]
   - 考虑的替代方案：[还评估了什么]

**输出**：research.md，所有"需要澄清"项已解决

### 阶段 1：设计与契约

**前提条件**：`research.md` 已完成

1. **从功能规格提取实体** → `data-model.md`：
   - 实体名称、字段、关系
   - 来自需求的验证规则
   - 状态转换（如适用）

2. **从功能需求生成 API 契约**：
   - 对于每个用户操作 → 端点
   - 使用标准 REST/GraphQL 模式
   - 将 OpenAPI/GraphQL 模式输出到 `/contracts/`

3. **代理上下文更新**：
   - 运行 `{AGENT_SCRIPT}`
   - 这些脚本检测正在使用的 AI 代理
   - 更新相应的代理特定上下文文件
   - 仅添加当前计划中的新技术
   - 保留标记之间的手动添加内容

**输出**：data-model.md、/contracts/*、quickstart.md、代理特定文件

## 关键规则

- 使用绝对路径
- 关卡失败或未解决的澄清项时报错
