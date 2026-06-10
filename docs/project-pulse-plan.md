# Project Pulse — Implementation Plan

## Summary
Objective: build a small static "Project Pulse" dashboard for Mona's team. Deliver a single-page static app (app/index.html + app/styles.css) driven by a JSON data file (app/project-data.json) and a VS Code launch configuration (.vscode/launch.json) that opens/serves `app/index.html` with working cwd. Approach: produce a deterministic HTML scaffold with explicit CSS hooks and a stable JSON schema; Designer provides visual spec and class-naming contract; Coder implements HTML/JSON and launch config. Work split and dependencies are explicit so tasks can run in parallel where safe.

## Deliverables
- app/index.html
- app/styles.css
- app/project-data.json
- .vscode/launch.json
- docs/project-pulse-plan.md (this plan)

## Minimal JSON example (required structure)
```json
{
  "projects": [
    {
      "id": "proj-001",
      "name": "Website Redesign",
      "owner": "Mona",
      "status": "On Track",
      "progress": 72,
      "last_updated": "2026-06-09"
    }
  ]
}
```

## Ordered implementation steps (numbered, owner, files)
1. Create plan file (Orchestrator)  
   - Files: docs/project-pulse-plan.md (this document)  
2. Define JSON schema and produce seed data (Coder) — BLOCKER for data-driven checks  
   - Files: app/project-data.json  
3. Designer: produce visual spec & class contract (wireframe + CSS hooks list)  
   - Files: docs/project-pulse-design-spec.md (designer artifact), includes class names and breakpoints  
   - Required hooks: `.dashboard`, `.project-list`, `.project-card`, `.project-title`, `.project-owner`, `.status-badge`, `.progress-bar`, `.meta`, `.no-data`  
4. Coder: create HTML scaffold using Designer contract (deterministic IDs/CSS hooks)  
   - Files: app/index.html  
   - Include: semantic HTML, aria attributes, placeholders/IDs for JS-free rendering from JSON (server-side rendering not required; static HTML should include a simple static rendering produced from project-data.json or minimal JS-free approach — prefer serverless static content populated manually from JSON)  
5. Coder: add styles baseline (responsive, accessibility-ready) implementing Designer hooks  
   - Files: app/styles.css  
6. Coder: create .vscode/launch.json configured to open/serve app/index.html with cwd `${workspaceFolder}/app`  
   - Files: .vscode/launch.json  
7. Designer & Coder: polish (colors, responsive adjustments, accessibility checks)  
   - Files: app/styles.css (designer suggestions applied by coder)  
8. Final validation & acceptance (Orchestrator)  
   - Files checked: app/index.html, app/styles.css, app/project-data.json, .vscode/launch.json

## File assignment table (quick)
- app/project-data.json — Coder
- app/index.html — Coder (implements Designer contract)
- app/styles.css — Coder (based on Designer spec); Designer provides spec file
- .vscode/launch.json — Coder
- docs/project-pulse-design-spec.md — Designer
- docs/project-pulse-plan.md — Orchestrator (this file)

## Dependencies & blocking items
- Step 2 (project-data.json) must be completed before final validation and any data-driven display verification.
- Step 3 (design spec) should be available before final HTML styling; Coder can still scaffold HTML using a minimal agreed hook set.
- .vscode/launch.json should be created before QA that uses VS Code launch debugging.

## Parallel work decisions
- Parallel safe:
  - Designer building visual spec/docs (step 3) and Coder creating project-data.json (step 2) — separate files, non-overlapping.
  - Coder scaffolding index.html (step 4) while Designer finalizes colors/spacing (step 3) — agreement on hooks keeps work non-conflicting.
  - Styles (step 5) and launch.json (step 6) can be done in parallel.
- Must be sequential:
  - Final polish (step 7) must wait for HTML scaffold + CSS baseline.
  - Validation (step 8) requires prior steps complete.

## Designer responsibilities (precise)
- Produce visual spec (single-page wireframe + color palette + typography).
- Provide exact class names/CSS hooks (must include at minimum): `.dashboard`, `.project-list`, `.project-card`, `.project-title`, `.project-owner`, `.status-badge`, `.progress-bar`, `.meta`, `.no-data`.
- Accessibility checklist:
  - semantic structure (header/main/section), roles where needed,
  - color contrast >= 4.5:1 for body text,
  - ensure status badges have both color and text label (not color-only),
  - alt text for any icons,
  - keyboard focus styles.
- Responsive breakpoints: mobile (<=600px), tablet (601–1024px), desktop (>=1025px).
- Provide CSS token suggestions (variables) to ease implementation.

## Coder responsibilities (precise)
- Implement deterministic HTML structure matching Designer hooks.
- Consume/reflect app/project-data.json in static rendering (for this exercise, include a static rendering consistent with project-data.json; ensure project IDs used as deterministic ids: `id="project-proj-001"`).
- Include aria attributes (aria-live on update area, role="list"/"listitem" for project list).
- Validate and commit app/project-data.json to follow schema: object with "projects": array of objects with fields: id (string), name (string), owner (string), status (string enum), progress (int 0–100), last_updated (YYYY-MM-DD).
- Create .vscode/launch.json exactly setting a launch configuration that opens/serves app/index.html and sets cwd to `${workspaceFolder}/app`.
  - Example keys: name, type (pwa-msedge or pwa-chrome) or use "server-ready" pattern; ensure path is explicit and valid JSON.
- Ensure CSS hooks/class names appear in HTML for Designer to style.

## Validation expectations / Acceptance criteria
For "done" each deliverable must satisfy:
- Files exist at paths: app/index.html, app/styles.css, app/project-data.json, .vscode/launch.json.
- project-data.json: valid JSON and conforms to schema above. Validate with: jq . app/project-data.json and check `jq -e '.projects and (.projects | type=="array")' app/project-data.json`.
- index.html: contains elements with required classes and deterministic ids for first project (e.g., `id="project-proj-001"`), semantic tags, and aria attributes.
- styles.css: referenced by index.html and contains rules (selectors) for all required hooks (grep for ".dashboard" ".project-card" etc).
- .vscode/launch.json: opens/serves app/index.html with cwd `${workspaceFolder}/app`. Manually validate by opening VS Code, selecting Run → Start Debugging using the created configuration, or run `npx http-server app -p 8080` and open http://localhost:8080/index.html to verify.
- Manual steps commands:
  - JSON validate: `jq . app/project-data.json`
  - Start local server: `npx http-server app -p 8080` (or use VS Code launch)
  - Open http://localhost:8080/index.html and confirm layout, badges, progress bars, and that content matches project-data.json.
- Optional CI checks: add a workflow that runs `jq` validation and `grep` for required CSS hooks in index.html.

## Edge cases, open questions, increments
- Edge cases: empty projects array (render `.no-data`), invalid progress values, unknown status strings — design should display fallback states (e.g., "Unknown" badge).
- Open questions: should project rendering be generated client-side JS or pre-rendered? (Prefer static pre-filled HTML for exercise simplicity; if client-side is allowed, define a small vanilla JS consumer.)
- Suggested increments:
  1. MVP: static HTML for 3 projects + CSS baseline + JSON seed.
  2. Add responsive refinements, color/polish, accessibility fixes.
  3. Optional: small client-side filter by status.

## Commit / PR strategy (suggested)
- PR 1 (Scaffold MVP): add app/index.html (static content for 3 projects), app/project-data.json (seed), .vscode/launch.json — coder
- PR 2 (Design spec): add docs/project-pulse-design-spec.md — designer
- PR 3 (Styles & polish): app/styles.css updates per design and accessibility polish — coder
- PR 4 (Polish & fixes): small UX fixes, add CI validation workflow (optional) — coder/orchestrator
- Keep commits small and file-scoped (one logical change per PR). Include screenshots in PR descriptions for visual changes.

Open any clarifying questions before assigning tasks (client-side rendering choice, allowed status values, color palette).