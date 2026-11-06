# Project Pulse implementation plan

## Outcome and planning basis

Build Mona's Project Pulse as a lightweight, polished static dashboard showing project names, owners, status, recent activity, priority/risk, and contributor-friendly summaries.

The Planner reviewed `.github/project-pulse-brief.md`, the four `.github/agents/` definitions, `docs/agent-team.md`, the step 2-4 guides and workflow checks, `scripts/validate-exercise.sh`, and the existing VS Code configuration. This document records the plan; it does not implement the application.

## Requirements and proposed choices

Confirmed requirements:

- Create `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
- Use the title "Project Pulse", visible `.project-card` elements, a `.dashboard` container, status badges, clear priority treatment, readable spacing, rounded corners, shadows, and a responsive layout.
- Reference `styles.css` and `project-data.json` from `index.html`. Show each project's `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Store data as valid JSON with a top-level `projects` array and the five required fields on every project.
- Use strict JSON without comments for `.vscode/launch.json`. Name the configuration `Run Project Pulse Dashboard`, set `cwd` to `${workspaceFolder}/app`, run `python3 -m http.server 5500`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`, not a directory listing.
- Keep specialists within assigned files. No agent stages, commits, or pushes.

Proposed choices for the Orchestrator to confirm before implementation:

- Keep JavaScript inline in `index.html`; the exercise checks look there for `project-card`, `status`, `recentActivity`, and `priority`.
- Include an optional `summary` string to meet the brief's contributor-friendly summary goal.
- Use fictional sample projects, human-readable activity strings, and explicit status/priority vocabularies. Suggested statuses are `Active`, `On track`, `At risk`, `Blocked`, and `Completed`; suggested priorities are `High`, `Medium`, and `Low`.
- Use VS Code's `node-terminal` launch type, subject to confirming server readiness and browser opening in the Codespace.
- Keep overview counts optional; they are not required for the initial dashboard.

## File assignments

| File | Assigned writer | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic dashboard shell, inline data loading and rendering, accessible status messages, explicit error handling, and safe text rendering. |
| `app/styles.css` | Designer | Dashboard layout, project cards, badges, priority styling, typography, spacing, contrast, responsive behavior, and loading/empty/error presentation. |
| `app/project-data.json` | Coder | Valid sample project data matching the agreed schema and covering the agreed status and priority styles. |
| `.vscode/launch.json` | Coder | Deterministic launch configuration that serves `app/` on port 5500 and opens `index.html`. |

The Orchestrator coordinates assignments, dependencies, specialist reports, and integrated review without implementing files. The Designer reviews markup read-only and sends requested HTML changes to the Coder through the Orchestrator. The Planner provides the plan and clarifies planning questions without implementing the app.

Leave `.vscode/tasks.json`, agent definitions, course workflows, and unrelated local changes untouched. Do not introduce additional application files unless the scope is explicitly revised.

## Shared contract

Before parallel work starts, the Orchestrator confirms:

- Data: `{ "projects": [{ "name": "...", "owner": "...", "status": "...", "recentActivity": "...", "priority": "...", "summary": "..." }] }`, with `summary` optional.
- Markup hooks: `.dashboard`, `.dashboard-header`, `.project-grid`, `.project-card`, `.project-name`, `.project-owner`, `.status-badge`, `.priority-tag`, `.project-activity`, `.project-summary`, and `.dashboard-message`.
- Status and priority modifier classes map agreed values to styles; unknown values retain neutral styling and visible text.
- Semantic structure: one page `h1`, project headings, a project list, and accessible loading/error announcements. Status and priority must not rely on color alone.

Designer markup recommendations needed by the Coder must be supplied before HTML integration. Any later contract change must be coordinated rather than independently guessed.

## Ordered phases and dependencies

| Phase | Assignment and deliverable | Prerequisites |
| --- | --- | --- |
| 0. Confirm contract | Orchestrator confirms assumptions, schema, CSS hooks, accessibility expectations, and file ownership. | Planner's plan. |
| 1A. Design styles | Designer creates `app/styles.css` and reports markup guidance and design tradeoffs. | Approved Phase 0 contract. |
| 1B. Prepare data and launch | Coder creates `app/project-data.json` and `.vscode/launch.json`. | Approved schema and launch requirements. The launch file can be authored now but requires the page for runtime validation. |
| 2. Integrate dashboard | Coder creates `app/index.html`, connects the data and stylesheet, and handles loading, empty, and error states. | Phase 1B data and Phase 1A markup guidance; stylesheet available for integrated review. |
| 3. Review experience | Designer reviews the rendered dashboard, accessibility, and responsive behavior; reports HTML changes and applies assigned CSS corrections. | Page, styles, data, and a running preview. |
| 4. Resolve findings | Coder fixes assigned HTML/data/launch issues; Designer fixes assigned CSS issues. Skip if no findings. | Phase 3 findings and explicit file assignments. |
| 5. Validate integration | Orchestrator coordinates checks, verifies the combined outcome, and reports files, results, blockers, and remaining risks. | All implementation and required corrections complete. |

## Parallel work decisions

- Run Phases 1A and 1B in parallel: file ownership does not overlap, and the approved contract removes the need to wait for each other's implementation.
- Keep Phase 2 after its data and markup-guidance dependencies. Review must wait for the integrated page; final validation must wait for required fixes.
- Phase 4 HTML and CSS fixes may run in parallel only when they are independent. If a fix changes shared markup or class names, agree the revised contract first and sequence dependent edits.
- Never assign simultaneous writes to the same file or start dependent work before its prerequisite output is available. The Orchestrator reports progress and blockers after each phase.

## Implementation and edge cases

- Use semantic HTML, a viewport meta tag, a linked stylesheet, and visible field labels. Build cards with DOM APIs and `textContent`, not data interpolated into `innerHTML`.
- Check HTTP success before parsing JSON, then validate the top-level array and required non-empty string fields. Report malformed data explicitly; do not silently replace invalid content with success-shaped defaults.
- Show loading, empty-project, and actionable error messages. Distinguish an empty array from a failed fetch or invalid schema.
- Explain how to launch the server when opened through `file://`. Handle missing data files, HTTP errors, malformed JSON, unknown badge values, and long text.
- Allow absent optional summaries without treating them as errors. Unknown status/priority text remains visible with neutral styling.
- Designer ensures adequate text contrast, wrapping without horizontal overflow, responsive cards, visible focus where applicable, and reduced-motion support if animation is introduced.
- Treat port 5500 conflicts as explicit blockers; do not stop unrelated processes. Confirm launch output matches the readiness pattern and the browser opens the page through Codespaces forwarding.

## Validation expectations

1. Parse both JSON files with `python3 -m json.tool`; validate the `projects` schema and required non-empty fields.
2. Check that `index.html` references the stylesheet and data file, contains the required title and rendering hooks, and displays all required fields. Check `.dashboard`, `.project-card`, rounded corners, shadows, and responsive styling.
3. Check launch configuration name, command, working directory, readiness pattern, and URL. Run **Run Project Pulse Dashboard** in VS Code and confirm it opens the dashboard rather than a directory listing.
4. Serve from `app/` and confirm successful HTTP responses for `index.html`, `styles.css`, and `project-data.json`. In a browser, confirm one card per project, correct content, and no unexpected console errors.
5. Inspect narrow mobile and desktop widths for readable hierarchy, appropriate card columns, long-text wrapping, and no horizontal scrolling. Check headings, text contrast, non-color status/priority labels, and accessible announcements.
6. Exercise loading, empty data, HTTP failure, malformed JSON/schema, missing required fields, unknown badge values, and HTML-like text with temporary browser mocks or isolated fixtures; do not leave test mutations in the committed sample data. Confirm errors are visible and data is rendered as text.
7. Check direct `file://` opening produces actionable launch guidance. Stop only preview processes started for validation when finished.
8. Use the relevant exercise workflow checks as acceptance criteria. The Planner identified `scripts/validate-exercise.sh` as a course-template check that can fail after learner files are committed; it is not the sole dashboard acceptance test.
9. Confirm only assigned files changed and preserve pre-existing local changes. Each specialist reports what changed and what was checked; the Orchestrator reports integrated results and any unverified checks explicitly.

## Open decisions

The Orchestrator must confirm the proposed status/priority vocabulary, optional summary field, inline-script approach, and launch type before assigning implementation. Overview counts remain optional and must not block the required dashboard.
