# Agent-Forge

**Agent-Forge** is a robust multi-agent automation framework designed to generate production-grade code through a rigorous "Plan-Execute-Audit" closed loop.

Unlike traditional "fire-and-forget" AI coding tools, agent-forge introduces a **Human-in-the-loop** planning confirmation mechanism and an **Audit-Driven** acceptance workflow. Code is only accepted after passing strict static analysis and verification standards.

## 🌟 Key Features

- **Interactive Planning**:
  - The Orchestrator automatically generates a detailed execution plan based on a `TASK_ID` (saved in `.claude/plan/`).
  - **User Confirmation Required**: Execution only begins after you verify and approve the plan.
- **Strict Quality Gates**:
  - An embedded `Audit Agent` forces a review of all artifacts.
  - Substandard code automatically enters a "Rework Loop" until it receives a `PASS` rating.
- **Adaptive Worker**:
  - The Worker Agent dynamically adopts roles such as "Backend Dev", "Frontend Dev", "QA Engineer", or "Architect" based on task requirements.
- **Session-based XML Logging**:
  - Logs are isolated by `TASK_ID` and `TIMESTAMP`.
  - **Action Log**: Tracks the system's operational timeline.
  - **Audit Log**: Records detailed code review feedback and scores.

## 🏗 Roles & Architecture

1.  **Orchestrator (Manager & Planner)**:
    - **Brain**: Decomposes tasks, generates plan files, and requests user approval.
    - **Dispatcher**: Manages Sub-agent calls and file paths.
    - **Constraint**: Does not write code directly.

2.  **Worker (Adaptive Executer)**:
    - **Polymorphic**: Accepts `role_definition` to implement logic and write tests.
    - **Responsive**: Fixes code based on specific Audit feedback.

3.  **Audit (Quality Control)**:
    - **Inspector**: Scores outputs against Acceptance Criteria.
    - **Output**: Appends XML-formatted reports to the specific session log.

4.  **Recorder (Scribe)**:
    - **Logger**: Maintains the "System Truth" by appending all events to the XML action log.

## 🚀 Installation & Setup

Ensure your `cc` (Claude Code) environment is configured as follows:

### 1. Directory Initialization

Create the configuration directories in your home folder:

```bash
mkdir -p ~/.claude/agents
mkdir -p ~/.claude/skills
```

_Note: During runtime, the system will automatically create `.claude/plan/`, `.claude/audit/`, and `.claude/action/` in your **current project root**._

### 2. Prompt Deployment (`~/.claude/agents/`)

Save the generated Prompt files here:

- `orchestrator.md`: Master logic with planning and confirmation.
- `worker.md`: Adaptive worker prompt with role injection.
- `audit.md`: Audit prompt with XML append capability.
- `recorder.md`: Recorder prompt with XML append capability.

## 📖 Usage Guide

1.  **Start**: Load the Orchestrator Agent.
2.  **Initialize**:
    - The system will request a **`TASK_ID`** (e.g., `Refactor_Login_Module`).
3.  **Confirm Plan**:
    - The Orchestrator generates a plan in `.claude/plan/`.
    - Review the plan and reply "Yes" or "Approve".
4.  **Auto-Execution**:
    - The system begins dispatching Workers and generating logs in `.claude/audit/` and `.claude/action/`.

## 📊 Logs & Auditing

All logs are stored in XML format and isolated by session:

- **Execution Plan**: `.claude/plan/plan_{TASK_ID}_{TIME}.md`
- **Action Stream**: `.claude/action/action_{TASK_ID}_{TIME}.xml`
- **Audit Details**: `.claude/audit/audit_{TASK_ID}_{TIME}.xml`
