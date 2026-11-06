# Agent team

I am using GitHub Copilot CLI in a Codespace to orchestrate the work of building Mona's Project Pulse dashboard. The Orchestrator delegates to three specialist agents.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | GPT-6 (copilot) | Gets the Planner's plan, breaks it into phases, assigns explicit file scopes, coordinates specialists, surfaces blockers and verifies the integrated result. Does not implement. | `.github/agents/orchestrator.agent.md` |
| Planner | GPT-6 (copilot) | Researches the repo, docs and edge cases; returns ordered steps, file assignments, dependencies and validation expectations. Writes no code. | `.github/agents/planner.agent.md` |
| Coder | GPT-6 (copilot) | Implements code and logic within its assigned files, with explicit errors and testable behavior. When assigned, creates `.vscode/launch.json` using strict JSON, cwd `${workspaceFolder}/app` and `index.html` as the page to open. Validates changes and reports remaining risks. | `.github/agents/coder.agent.md` |
| Designer | Gemini 4 Argon (copilot) | Owns UI/UX, accessibility and styling: project cards, status badges, priority treatment, responsive layout, `.dashboard` and `.project-card` hooks. | `.github/agents/designer.agent.md` |

The handoff is Planner → Orchestrator → Coder and Designer → integrated review by the Orchestrator. Specialists work in parallel only when their file scopes do not overlap and there are no data dependencies. Overlapping work, dependent tasks and tasks needing approval run sequentially.

All AGENTS are forbidden from staging, committing or pushing; I control git through Copilot CLI prompts.
