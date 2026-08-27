# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's **Project Pulse** dashboard as a lightweight static app for contributors. The dashboard will show multiple projects, their owners, current statuses, recent activity, priority or risk, and short contributor-friendly summaries in an accessible, responsive card layout.

The repository currently contains the exercise brief and custom agents under `.github/agents/`, but the learner deliverables do not yet exist:

- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`
- `docs/project-pulse-plan.md`

There is no package manifest, frontend framework, application test suite, or external runtime dependency. The existing universal dev container provides Python 3 for the local static server. `.vscode/tasks.json` already configures the folder-open Copilot CLI task and should not be modified for this work.

## Agent Responsibilities

### Orchestrator

- Coordinate Planner, Designer, and Coder through GitHub Copilot CLI.
- Use this plan to establish file ownership and phase ordering.
- Provide the Designer with the visual and accessibility requirements.
- Provide the Coder with the HTML/data contract and launch requirements.
- Prevent overlapping edits to the same file.
- Review the integrated dashboard and report validation results.
- Do not stage, commit, or push files; those operations remain learner-controlled.

### Planner

- Own `docs/project-pulse-plan.md`.
- Research the repository brief, custom-agent definitions, existing VS Code configuration, and workflow checks.
- Define implementation phases, dependencies, parallel work, file ownership, edge cases, and validation expectations.
- Ensure the plan explicitly names all four required implementation files.

### Designer

- Own the visual implementation in `app/styles.css`.
- Define the information hierarchy for the dashboard header, summary content, project cards, status badges, activity details, and priority treatment.
- Provide a responsive card-grid layout with readable spacing and clear typography.
- Include deterministic hooks required by the exercise, especially `.dashboard` and `.project-card`.
- Include polished visual affordances such as `border-radius`, `box-shadow`, sufficient contrast, and clear focus states.
- Account for keyboard navigation, semantic structure, readable labels, and responsive behavior.
- Communicate the expected class names and markup contract to Coder without editing Coder-owned files.

### Coder

- Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.
- Implement the semantic dashboard shell and the Project Pulse heading.
- Reference `styles.css` and `project-data.json` from `app/index.html`.
- Load the JSON data and render visible project cards using the `project-card` class.
- Render each project's `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Use a top-level `projects` array in the JSON file with multiple project records.
- Implement explicit loading, empty-data, and data-load-error states rather than silently failing.
- Create the strict JSON launch configuration that runs and opens the dashboard.
- Keep implementation deterministic and limited to the assigned files.

## File Assignments

| File | Owner | Required responsibilities |
|---|---|---|
| `docs/project-pulse-plan.md` | Planner | This implementation plan, including phases, dependencies, parallel work, ownership, edge cases, and validation. |
| `app/styles.css` | Designer | Complete responsive visual system for the dashboard, including `.dashboard`, `.project-card`, status/priority treatments, spacing, contrast, rounded cards, shadows, and focus states. |
| `app/project-data.json` | Coder | Valid JSON with a top-level `projects` array containing multiple projects. Every project must include `name`, `owner`, `status`, `recentActivity`, and `priority`. |
| `app/index.html` | Coder | Accessible dashboard markup, exact visible title `Project Pulse`, stylesheet and data references, data loading, and visible `project-card` elements that show status, recent activity, and priority. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration named `Run Project Pulse Dashboard`, serving from `app/` with `python3 -m http.server 5500` and opening `http://localhost:%s/index.html`. |

No new JavaScript file, dependency manifest, framework, or build system is required. If behavior needs scripting, keep the small data-loading script in `app/index.html` so the implementation remains within the required file scope.

## Ordered Implementation Steps

### 1. Establish the implementation contract

**Owner:** Orchestrator and Planner  
**File:** `docs/project-pulse-plan.md`

Confirm the requirements from `.github/project-pulse-brief.md` and the custom agent definitions:

- Project Pulse is a static contributor dashboard.
- The data schema is a top-level `projects` array.
- Each project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
- The app must open `index.html`, not a directory listing.
- The launch configuration must serve from the `app` directory.
- All agent work must use explicit file scopes.

### 2. Create the data and visual foundation

**Owners:** Coder and Designer  
**Files:** `app/project-data.json`, `app/styles.css`

These tasks can proceed in parallel because the data schema and stylesheet are independent.

Coder creates representative data for multiple projects using consistent status and priority values. The records should contain concise, contributor-friendly content and enough variation for the UI to demonstrate active, completed, blocked, or at-risk states.

Designer creates the stylesheet around the agreed markup contract. The stylesheet must include:

- `.dashboard`
- `.project-card`
- responsive layout behavior
- `border-radius`
- `box-shadow`
- readable spacing and typography
- status badge and priority/risk styling
- visible keyboard focus states
- mobile-friendly card behavior

### 3. Implement the dashboard integration

**Owner:** Coder  
**File:** `app/index.html`

This step must follow the data and CSS contract from Step 2.

The HTML should:

- Use a valid document structure and an exact `Project Pulse` title or heading.
- Link to `styles.css`.
- Reference `project-data.json`.
- Provide an accessible dashboard container using the `.dashboard` class.
- Load the JSON through the local HTTP server.
- Render one `.project-card` per project.
- Display each project's `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Include meaningful labels and semantic elements for scanability.
- Show a clear loading state.
- Show a clear empty state when `projects` is empty.
- Surface a useful error message if the JSON cannot be loaded or has an invalid shape.

The implementation should not depend on opening the file directly with `file://`; the launch configuration in the next step supplies the required HTTP context for fetching JSON.

### 4. Add the runnable VS Code configuration

**Owner:** Coder  
**File:** `.vscode/launch.json`

Create strict JSON with no comments. Add a configuration named exactly:

`Run Project Pulse Dashboard`

The configuration must:

- Run `python3 -m http.server 5500`.
- Set `cwd` to `${workspaceFolder}/app`.
- Use a server-ready action that captures the port.
- Open `http://localhost:%s/index.html`.
- Open the dashboard page directly rather than the server directory root.
- Use stable, deterministic launch settings compatible with the existing VS Code/Codespace setup.

### 5. Integrate and review

**Owner:** Orchestrator with Designer and Coder**

Review all four implementation files together rather than validating them in isolation:

- Confirm the HTML class names match the Designer's CSS selectors.
- Confirm the JSON property names match the fields rendered by the HTML.
- Confirm the launch working directory makes `index.html` and `project-data.json` available at the expected relative paths.
- Confirm the dashboard remains usable at narrow viewport widths.
- Confirm no agent has modified unrelated exercise infrastructure or raw Git history.

## Dependencies

The primary dependency chain is:

```text
Project brief and agent definitions
        |
        v
Plan and markup/data/style contract
        |
        +--> Designer: app/styles.css
        |
        +--> Coder: app/project-data.json
                      |
                      v
              Coder: app/index.html
                      |
                      +--> Coder: .vscode/launch.json
                                      |
                                      v
                              Integrated preview and review
```

Specific dependencies:

- `app/index.html` depends on the JSON field names in `app/project-data.json`.
- `app/index.html` depends on the CSS hooks and layout contract from `app/styles.css`.
- Fetching `project-data.json` depends on serving the application over HTTP.
- `.vscode/launch.json` must be correct before the end-to-end preview can be confirmed.
- Final validation must occur after all four files are present and integrated.

## Parallel Work Decisions

Work that can proceed in parallel:

- Designer work on `app/styles.css`.
- Coder work on `app/project-data.json`.
- Initial planning and repository/workflow inspection, before implementation begins.

Work that should be sequential:

1. Planner completes the plan and establishes the file contract.
2. Designer and Coder independently complete CSS and data work.
3. Coder implements `app/index.html` after receiving the CSS hooks and confirming the data schema.
4. Coder creates or finalizes `.vscode/launch.json`.
5. Orchestrator performs integrated review and preview validation.

The same agent should not have concurrent edits to a single file. Designer owns the stylesheet; Coder owns the HTML, data, and launch configuration. Any required cross-file adjustment must be coordinated by the Orchestrator and performed as a deliberate handoff.

## Edge Cases and Risks

- **JSON load failure:** Display an explicit error message if `project-data.json` cannot be fetched or parsed.
- **Unexpected data shape:** Handle missing or non-array `projects` data without rendering a misleading successful dashboard.
- **Empty project list:** Show an accessible empty-state message instead of a blank card area.
- **Missing fields:** Use clear fallback text or mark the record invalid; do not silently omit required information.
- **Direct file opening:** Document through the implementation behavior that the dashboard should be launched through the HTTP server because browser fetch behavior may block local JSON access under `file://`.
- **Directory listing:** Ensure the launch URL ends in `/index.html`.
- **Port conflicts:** Use the required port `5500`; if it is unavailable during manual validation, stop the conflicting process before retrying.
- **Responsive layout:** Prevent cards, badges, and activity text from overflowing on narrow screens.
- **Accessibility:** Preserve heading hierarchy, label status and priority values, maintain readable contrast, and provide visible keyboard focus.
- **Scope drift:** Do not modify `.vscode/tasks.json`, dev container scripts, workflows, or agent definitions unless explicitly requested.

## Validation Expectations

### Structural validation

Confirm these files exist:

- `docs/project-pulse-plan.md`
- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`

The existing Step 2 workflow checks that the plan contains the exact concepts and paths for Project Pulse, Designer, Coder, dependencies, parallel work, validation, and all four assigned files.

### Content validation

Confirm:

- `app/index.html` contains `Project Pulse`.
- `app/index.html` references `styles.css` and `project-data.json`.
- `app/index.html` contains or generates `project-card` markup.
- The rendered UI exposes `status`, `recentActivity`, and `priority`.
- `app/styles.css` contains `.dashboard` and `.project-card`.
- `app/styles.css` includes `border-radius`, `box-shadow`, responsive rules, and accessible focus treatment.
- `app/project-data.json` contains a top-level `projects` key.
- Each project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json` contains `Run Project Pulse Dashboard`, `python3 -m http.server 5500`, the `app` working directory, and `index.html`.

### Syntax validation

Run the existing JSON parser checks:

```bash
python3 -m json.tool app/project-data.json >/dev/null
python3 -m json.tool .vscode/launch.json >/dev/null
```

The launch file must remain strict JSON with no comments.

### Browser and launch validation

From VS Code Run and Debug:

1. Select `Run Project Pulse Dashboard`.
2. Start the configuration.
3. Confirm the browser opens `http://localhost:5500/index.html`.
4. Confirm the Project Pulse dashboard appears instead of a directory listing.
5. Confirm multiple project cards are visible and show owner, status, recent activity, and priority.
6. Test keyboard navigation, readable focus states, and a narrow viewport.
7. Stop the preview server after validation.

The existing `scripts/validate-exercise.sh` is primarily a template and exercise-infrastructure validator; it checks that learner output files are not tracked in the template. It should not replace the targeted JSON, content, browser, and launch checks above.

## Open Questions

No blocking questions remain. The Designer may choose the final color palette, typography scale, and exact status/priority visual treatments as long as the required selectors, fields, accessibility behavior, and responsive layout are preserved.
