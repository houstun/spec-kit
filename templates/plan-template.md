# 实现计划：[功能]

**分支**：`[###-feature-name]` | **日期**：[日期] | **规范**：[链接]
**输入**：来自 `/specs/[###-feature-name]/spec.md` 的功能规范

**注意**：此模板由 `/speckit.plan` 命令填写。执行工作流请参见 `.specify/templates/commands/plan.md`。

## 摘要

[从功能规范中提取：主要需求 + 研究得出的技术方案]

## 技术上下文

<!--
  需要操作：将本节内容替换为项目的技术细节。
  此处结构仅作为指导迭代过程的建议。
-->

**语言/版本**：[例如 Python 3.11、Swift 5.9、Rust 1.75 或 需要澄清]
**主要依赖**：[例如 FastAPI、UIKit、LLVM 或 需要澄清]
**存储**：[如适用，例如 PostgreSQL、CoreData、文件 或 不适用]
**测试**：[例如 pytest、XCTest、cargo test 或 需要澄清]
**目标平台**：[例如 Linux 服务器、iOS 15+、WASM 或 需要澄清]
**项目类型**：[单一/Web/移动 - 决定源代码结构]
**性能目标**：[领域特定，例如 1000 请求/秒、10k 行/秒、60 fps 或 需要澄清]
**约束条件**：[领域特定，例如 <200ms p95、<100MB 内存、支持离线 或 需要澄清]
**规模/范围**：[领域特定，例如 1 万用户、100 万行代码、50 个页面 或 需要澄清]

## 宪章检查

*关卡：必须在阶段 0 研究前通过。阶段 1 设计后重新检查。*

[根据宪章文件确定的关卡]

## 项目结构

### 文档（本功能）

```text
specs/[###-feature]/
├── plan.md              # 本文件（/speckit.plan 命令输出）
├── research.md          # 阶段 0 输出（/speckit.plan 命令）
├── data-model.md        # 阶段 1 输出（/speckit.plan 命令）
├── quickstart.md        # 阶段 1 输出（/speckit.plan 命令）
├── contracts/           # 阶段 1 输出（/speckit.plan 命令）
└── tasks.md             # 阶段 2 输出（/speckit.tasks 命令 - 不由 /speckit.plan 创建）
```

### 源代码（仓库根目录）
<!--
  需要操作：将下面的占位符目录树替换为此功能的具体布局。
  删除未使用的选项，并将所选结构扩展为真实路径
 （例如 apps/admin、packages/something）。交付的计划中
  不得包含选项标签。
-->

```text
# [如未使用则删除] 选项 1：单一项目（默认）
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# [如未使用则删除] 选项 2：Web 应用（当检测到"前端"+"后端"时）
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# [如未使用则删除] 选项 3：移动端 + API（当检测到"iOS/Android"时）
api/
└── [同上述 backend 结构]

ios/ 或 android/
└── [平台特定结构：功能模块、UI 流程、平台测试]
```

**结构决策**：[记录所选结构并引用上面捕获的真实目录]

## 复杂度追踪

> **仅在宪章检查有违规且必须说明理由时填写**

| 违规项 | 为何需要 | 拒绝更简单替代方案的原因 |
|--------|----------|--------------------------|
| [例如 第 4 个项目] | [当前需求] | [为何 3 个项目不够] |
| [例如 仓储模式] | [具体问题] | [为何直接访问数据库不够] |
