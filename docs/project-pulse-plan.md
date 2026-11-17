# Project Pulse implementation plan

## At a glance

- **Sequence:** Agree on the interface contract → implement the page, data, and design → integrate and verify.
- **Dependency:** Implementation starts after the shared fields and presentation hooks are agreed; integration starts after all implementation files are ready.
- **Parallel work:** After the contract is fixed, Designer owns `app/styles.css`; Coder owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.
- **Validation:** Check required files and content, parse both JSON files, run repository acceptance checks, then verify runtime, responsive behavior, and accessibility.

## Goal

Build a lightweight, static Project Pulse dashboard that helps contributors scan project names, owners, status, recent activity, priority or risk, and a short summary. Use the existing brief in `.github/project-pulse-brief.md` as the product requirements. The app should open as a dashboard page, not a directory listing, from the VS Code **Run Project Pulse Dashboard** launch configuration.

## Repository findings and assumptions

- The brief and Step 3 exercise define the required project fields, page behavior, styling hooks, and launch expectations.
- The custom agent definitions establish that Designer owns UX guidance and Coder implements assigned files; Coder may create `launch.json` when assigned.
- `.vscode/tasks.json` exists but does not define a dashboard preview. There is no app framework or existing app implementation to extend; `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` do not yet exist.
- Assume a dependency-free static app using HTML, CSS, JSON, and browser JavaScript. Keep the requested file set; do not introduce a build tool or package manifest.
- Use the brief's required JSON fields and include a concise `summary` per project to meet its contributor-friendly-summary goal.

## Ordering

Work proceeds in three steps: first agree on the interface contract, then implement the assigned files, and finally integrate and verify the assembled app. Integration and runtime verification happen after implementation, not alongside it.

## Work steps and file assignments

### 1. Agree on the interface contract

- **Owner:** Orchestrator, with Designer and Coder.
- Confirm the content model: top-level `projects` array; each object has `name`, `owner`, `status`, `recentActivity`, and `priority`, plus `summary`.
- Confirm stable presentation hooks before implementation: `.dashboard`, `.project-list`, `.project-card`, and a status/priority badge hook. Keep field names consistent between data and rendering.

### 2. Implement the page, data, and visual design in parallel

- **Designer — `app/styles.css`:** Create polished, readable Project Pulse styling for the shared class contract. Define clear information hierarchy, card and badge treatments, spacing, color contrast, visible focus states, and responsive layouts for narrow screens. Include `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- **Coder — `app/index.html`:** Create a semantic page with the exact title **Project Pulse**, a clear page heading, stylesheet reference, and a project-list container. Load `project-data.json`, render one visible `.project-card` per project, and display each required field and summary. Include meaningful loading, empty, and error states; insert untrusted JSON strings as text rather than HTML.
- **Coder — `app/project-data.json`:** Create representative, useful project records under a top-level `projects` array. Populate every required field and `summary`; use consistent status and priority labels.
- **Coder — `.vscode/launch.json`:** Create strict JSON with a **Run Project Pulse Dashboard** configuration. Set `cwd` to `${workspaceFolder}/app`, run `python3 -m http.server 5500`, and use a server-ready action that opens `http://localhost:%s/index.html`.

### 3. Integrate and verify

- **Owners:** Coder integrates; Designer checks visual/accessibility outcomes; Orchestrator checks the complete result.
- Verify that HTML references the stylesheet and JSON data, and that data keys map to the fields rendered on cards. Adjust the page or styling if the rendered structure diverges from the agreed contract.

## Dependencies

Prerequisite relationships between the work steps:

- **Step 1 → Step 2:** The interface contract (data fields and presentation hooks) must be agreed before implementation begins; both the page and stylesheet depend on that shared contract.
- **Step 2 → Step 3:** Integration depends on all four assigned implementation files being present: `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
- **Step 3 validation dependencies:** Browser checks depend on the page, data, and styling being connected. Launch verification depends on the launch configuration and app files existing.

## Parallel work

Within Step 2, Designer can implement only `app/styles.css` while Coder creates `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. This work is parallel because the assigned files do not overlap; the shared interface contract must be fixed first to prevent integration drift. Keep Coder's ownership of HTML/data/launch explicit and Designer's stylesheet ownership exclusive. Orchestrator coordinates but does not edit implementation files.

## Edge cases and risks

- No projects or an empty array: show a useful empty state rather than a blank dashboard.
- Missing, malformed, or unavailable JSON: show a readable error/retry message; do not leave a permanent loading state.
- Missing optional summary or unexpected field values: render a safe fallback without breaking the remaining cards.
- Project names, activity, and other data may contain HTML-like text: render as text to avoid interpreting it as markup.
- Long names/activity and narrow viewports: allow wrapping and prevent horizontal overflow.
- Status/priority values may vary: keep labels readable and distinguishable without relying on color alone.
- Port `5500` may already be occupied: surface the server startup failure and document that the learner can stop the existing process before launch. The required launch configuration should still use the exercise-specified command and port.
- Accessibility risks include low contrast, missing heading structure, unlabeled status, and keyboard-invisible focus; check these during design review.

## Validation expectations

1. **Static checks:** Confirm the three app files and `.vscode/launch.json` exist. Confirm `app/index.html` contains the exact title, references `styles.css` and `project-data.json`, and renders `project-card` markup plus status, `recentActivity`, and priority. Confirm CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
2. **Data/config parsing:** Run `python3 -m json.tool app/project-data.json >/dev/null` and `python3 -m json.tool .vscode/launch.json >/dev/null`. Check that every project has `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary`, and that the launch name, working directory, command, and URL target are correct.
3. **Repository acceptance checks:** Run the Step 3 workflow's equivalent content checks, including the launch name and `index.html` target. The workflow currently validates file presence, key phrases, and JSON syntax; it does not prove browser rendering or launch execution.
4. **Runtime/manual checks:** Start **Run Project Pulse Dashboard** in VS Code. Confirm it serves `app/` and opens `http://localhost:<port>/index.html`, showing the UI rather than a directory listing. Check that project cards display all fields, and test the empty/error states with temporary local test data. Remove test alterations afterward.
5. **Responsive/accessibility review:** Inspect a narrow and wide viewport; check keyboard navigation and visible focus, semantic heading structure, readable contrast, wrapped long text, and status/priority distinctions that do not depend on color alone. Stop the preview server after validation.

## Open questions

- The brief does not prescribe project examples, status/priority vocabularies, or exact visual branding. Use representative sample projects and a small consistent label set unless the product owner supplies preferred values.
- The exercise expects a static JSON-backed app but does not prescribe how to handle fetch errors or empty data. This plan assumes explicit in-page loading, empty, and error states.
- The requested server command and fixed port are specified by Step 3. If the local VS Code environment cannot use the chosen launch type or port, report the limitation and agree on a compatible launch mechanism without changing the required launch name or `index.html` target.
