# Copilot Instructions for this repository

This file helps future Copilot/Copilot-CLI sessions understand repository layout, how to validate changes, and repository-specific conventions for the Agent Orchestration exercise.

---

## 1) Build / test / lint commands

- This repository is an exercise/template (documentation + agent definitions). There are no build, test, or lint scripts included in the repo itself.
- Validation is primarily done by GitHub Actions workflows under `.github/workflows/` (e.g., "Step 1"). To run or trigger those locally/use the CLI:
  - Run a workflow via GitHub CLI (where a workflow exists):
    - gh workflow list
    - gh workflow run "Step 1" --ref main
  - Inspect workflow checks in the Actions tab for automated validation.

If tests or a runnable app are added later, put commands into a top-level Makefile, package.json scripts, or pyproject.toml and update this file with exact single-test invocation examples (e.g., `pytest path/to/test::test_name`, `npm test -- tests/specific.test.js`).

---

## 2) High-level architecture (big picture)

- Purpose: an exercise demonstrating agent orchestration to build a small static "Project Pulse" dashboard.
- Agent-oriented architecture (primary pattern):
  - Orchestrator: coordinates work, breaks plans into phases, delegates to subagents.
  - Planner: researches repository and outputs an actionable plan (file ownership, dependencies, edge cases).
  - Coder: implements code and deterministic runnable app artifacts. Responsible for creating support files when assigned (e.g., `.vscode/launch.json`).
  - Designer: handles UI/UX, CSS, and visual decisions for the dashboard.
- Agent definitions live under `.github/agents/*.agent.md` and include front-matter (name, description, model, and tools) and explicit rules (e.g., git control rules).
- Learning flow: a human (learner) uses GitHub Copilot CLI to orchestrate tasks with the Orchestrator and specialist agents rather than writing everything in one prompt.
- Exercise target (Project Pulse): a small static app exposing `app/index.html`, `app/styles.css`, `app/project-data.json` and a `.vscode/launch.json` configured to open the index page.
- CI/validation: workflows in `.github/workflows/` check for required docs and agent definitions rather than compiling or testing source code.

---

## 3) Key repository conventions and patterns (non-obvious)

- Agent files (.agent.md)
  - Located at `.github/agents/`.
  - Use YAML front-matter style metadata (name, model, tools). Copilot sessions should parse metadata to determine agent responsibilities and allowed tools.
  - Each agent file contains explicit rules and a Git control section. Many rules say: "Do not stage, commit, or push changes—learner controls git operations." Respect this in automated runs unless told otherwise.

- File-scope ownership and delegation
  - The Orchestrator delegates explicit file scopes to prevent merge conflicts. Subagents should only modify files the Orchestrator assigned.
  - When composing prompts for subagents, always include the file scope and validation expectations.

- Runnable-app support
  - If implementing the Project Pulse runnable app, create `.vscode/launch.json` with `cwd` set to `${workspaceFolder}/app` and an entry that opens `app/index.html` (strict JSON, no comments).
  - Use deterministic CSS hooks such as `.dashboard` and `.project-card` (Designer guidance in `.github/agents/designer.agent.md`).

- Runner/validation expectations
  - The GitHub Actions workflows validate presence and content of docs (`docs/agent-team.md`) and agent definitions using action-keyphrase-checker and file-exists helpers. When adding or changing agent docs, ensure the workflows' keyphrases remain satisfied.

- Documentation-first exercise
  - The exercise is documentation-driven: the workflow expects learners to update `docs/agent-team.md` and the `.github/agents/*` definitions. Follow the templates and include model/tool updates when applicable.

---

## 4) Where to look (quick pointers)

- Agent definitions: `.github/agents/*.agent.md`
- Exercise brief: `.github/project-pulse-brief.md`
- Steps & guidance for the training flow: `.github/steps/*.md` and `.github/outline/*.md`
- Workflows that validate progress: `.github/workflows/*.yml`
- Exercise README: `README.md` and `docs/agent-team.md` (starter file the learner must fill).

---

## 5) Editing guidance for Copilot sessions

- Before making edits, read the relevant `.agent.md` for rules and for the Orchestrator's required file-scope. The Planner should be asked to produce a file-assignment plan before the Coder writes files.
- When adding runnable app files, include deterministic launch configs and validate by opening `app/index.html` in the described `.vscode/launch.json` configuration.
- Do not auto-commit or push unless the human explicitly instructs the Copilot CLI to perform git operations.

---

If anything in this file should be expanded (for example adding explicit single-test commands when a test framework is introduced), update this file and the workflows that reference the repo's validation rules.
