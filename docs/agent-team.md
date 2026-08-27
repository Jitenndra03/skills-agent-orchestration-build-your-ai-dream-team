# Agent team

This team will use GitHub Copilot CLI in a Codespace to orchestrate the work for
Mona's Project Pulse dashboard.

## Team roles

| Agent | Model | Definition | Responsibility |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | `.github/agents/orchestrator.agent.md` | Coordinates the team, breaks the dashboard work into phases, assigns explicit file scopes, manages dependencies and parallel work, and verifies the integrated result. |
| **Planner** | Claude Opus 4.7 (copilot) | `.github/agents/planner.agent.md` | Researches the repository, documentation, dependencies, edge cases, risks, and validation needs, then produces the implementation plan. The Planner does not write code. |
| **Coder** | GPT-5.5 (copilot) | `.github/agents/coder.agent.md` | Implements the dashboard behavior and runnable application support within the assigned files, keeps the code deterministic and testable, and validates the implementation. |
| **Designer** | Gemini 3.1 Pro (copilot) | `.github/agents/designer.agent.md` | Designs and implements the user experience within the assigned files, including information hierarchy, accessibility, responsive behavior, visual clarity, project cards, status badges, and priority treatment. |

## How the team will work

1. The Orchestrator asks the Planner to inspect the repository and create an
	implementation plan for Project Pulse, including file assignments,
	dependencies, edge cases, and validation expectations.
2. The Orchestrator turns that plan into phases with non-overlapping file
	ownership. Planning must finish first because the later tasks depend on its
	file assignments and technical decisions.
3. The Designer and Coder work from the approved plan. Their tasks can run in
	parallel when their file scopes do not overlap: the Designer shapes the
	polished dashboard experience, while the Coder implements the data,
	behavior, and assigned launch configuration.
4. The Orchestrator integrates the results, checks that the frontend and
	implementation agree, verifies the dashboard is runnable, and reports the
	final outcome and any remaining risks.

All agents stay within their assigned file scopes and do not stage, commit, or
push Git changes. GitHub Copilot CLI in the Codespace remains the entry point
for orchestrating these handoffs.
