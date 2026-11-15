# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate this custom agent team to build Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the specialists, turns the plan into phases with explicit file scopes and dependencies, and verifies the integrated dashboard without implementing it directly. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and produces an implementation plan covering file assignments, dependencies, edge cases, risks, and validation expectations; does not write code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements dashboard logic and assigned runnable-app support, follows repository patterns, makes errors explicit, and validates changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Creates a polished, accessible, responsive dashboard with clear information hierarchy, project cards, status badges, and priority styling. | `.github/agents/designer.agent.md` |

The Orchestrator starts with the Planner's plan, then delegates implementation and design to the Coder and Designer. Tasks run in parallel only when file scopes do not overlap and there are no data dependencies; otherwise, they run sequentially. All agents leave staging, commits, and pushes to the learner through Copilot CLI prompts.
