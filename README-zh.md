# Agent-Forge

**Agent-Forge** 是一个高强度的自动化多 Agent 开发框架，旨在通过严格的“规划-执行-审计”闭环来生成生产级代码。

与传统的“一问一答”模式不同，agent-forge 引入了 **Human-in-the-loop (人机回环)** 的规划确认机制，以及 **Audit-Driven (审计驱动)** 的代码验收机制。只有通过了静态分析和验收标准的代码，才会被系统认可。

## 🌟 核心特性

- **交互式规划 (Interactive Planning)**:
  - Orchestrator 会根据 `TASK_ID` 自动生成详细执行计划 (`.claude/plan/`)。
  - **必须经过用户确认**，系统才会开始执行代码生成。
- **严格质量门控 (Strict Quality Gates)**:
  - 引入 `Audit Agent`，对所有产出进行强制审计。
  - 不合格的代码自动进入“返工 (Rework)”循环，直到 `PASS` 为止。
- **自适应执行干员 (Adaptive Worker)**:
  - Worker Agent 根据任务需求，动态扮演“后端开发”、“前端开发”、“测试工程师”或“架构师”角色。
- **会话级结构化日志 (Session-based XML Logging)**:
  - 日志按 `TASK_ID` 和 `TIMESTAMP` 隔离，避免混淆。
  - **Action Log**: 记录系统行为流水线。
  - **Audit Log**: 记录详细的代码审查意见和评分。

## 🏗 架构角色

1.  **Orchestrator (主控 & 规划师)**:
    - **大脑**: 负责任务拆解、生成计划文件、请求用户批准。
    - **调度**: 管理 Sub-agents 的调用路径和文件句柄。
    - **限制**: 不直接写代码。

2.  **Worker (执行者 - Adaptive)**:
    - **多面手**: 接受 `role_definition`，负责具体实现和测试编写。
    - **响应**: 根据 Audit 的反馈进行针对性修复。

3.  **Audit (审计官)**:
    - **质检**: 对照 Acceptance Criteria 进行评分。
    - **输出**: 生成 XML 格式的审计报告并追加到日志文件。

4.  **Recorder (书记员)**:
    - **记录**: 维护“真理来源”，将所有事件追加写入 XML 日志。

## 🚀 安装与配置

请确保您的 `cc` (Claude Code) 环境配置如下：

### 1. 目录初始化

在您的用户根目录下创建配置目录：

```bash
mkdir -p ~/.claude/agents
mkdir -p ~/.claude/skills
```

_注意：项目运行时，系统会自动在**当前项目根目录**下创建 `.claude/plan/`, `.claude/audit/`, `.claude/action/` 等文件夹。_

### 2. 提示词部署 (`~/.claude/agents/`)

请将生成的 Prompt 文件保存到此目录：

- `orchestrator.md`: 包含规划和确认逻辑的主控提示词。
- `worker.md`: 支持角色注入的 Worker 提示词。
- `audit.md`: 支持 XML 追加写入的审计提示词。
- `recorder.md`: 支持 XML 追加写入的记录员提示词。

## 📖 使用指南

1.  **启动**: 加载 Orchestrator Agent。
2.  **初始化**:
    - 系统会要求你提供 **`TASK_ID`** (例如: `Refactor_Login_Module`)。
3.  **确认计划**:
    - Orchestrator 会在 `.claude/plan/` 生成计划文件。
    - 你审查计划后回复 "Yes" 或 "同意"。
4.  **自动执行**:
    - 系统开始调度 Worker 干活，并在 `.claude/audit/` 和 `.claude/action/` 中生成日志。

## 📊 日志查看

所有日志均为 XML 格式，按 Session 隔离：

- **执行计划**: `.claude/plan/plan_{TASK_ID}_{TIME}.md`
- **动作流水**: `.claude/action/action_{TASK_ID}_{TIME}.xml`
- **审计详情**: `.claude/audit/audit_{TASK_ID}_{TIME}.xml`
