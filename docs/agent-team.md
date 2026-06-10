# Agent team

This repository defines a 4-agent team used to implement Mona's Project Pulse dashboard. Each agent has an agent definition under `.github/agents/` and a focused responsibility:

- Orchestrator — Model: Claude Opus 4.7 (copilot)
  - Responsibility: Coordinate the Planner, Coder, and Designer; break plans into phases, assign file scopes, and verify integrated results.
  - Definition: `.github/agents/orchestrator.agent.md`

- Planner — Model: Claude Opus 4.7 (copilot)
  - Responsibility: Research the repo, produce an ordered, actionable implementation plan with file assignments, dependencies, edge cases, and validation expectations.
  - Definition: `.github/agents/planner.agent.md`

- Coder — Model: GPT-5.5 (copilot)
  - Responsibility: Implement code and deterministic runnable artifacts (e.g., `app/index.html`, `.vscode/launch.json`) within file scopes assigned by the Orchestrator. Validate changes before reporting completion.
  - Definition: `.github/agents/coder.agent.md`

- Designer — Model: Gemini 3.1 Pro (copilot)
  - Responsibility: UI/UX, accessibility, information hierarchy, and polished styling for the Project Pulse frontend (CSS hooks such as `.dashboard` and `.project-card`).
  - Definition: `.github/agents/designer.agent.md`

Note: the learning flow expects a human to orchestrate these agents from a Codespace using GitHub Copilot CLI; per-agent rules forbid autonomous git commits — the learner controls staging/commits/pushes.
