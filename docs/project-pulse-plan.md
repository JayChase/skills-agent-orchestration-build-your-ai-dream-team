# Project Pulse implementation plan

## Goal and scope

Build Mona's Project Pulse dashboard as a polished, accessible, responsive static app showing project names, owners, statuses, recent activity, and priority levels. Include short project summaries where available.

This plan was created by the Planner after reviewing the project brief, custom agent definitions, exercise instructions and checks, and Codespace configuration. GitHub Copilot CLI in a Codespace will orchestrate the implementation. This document plans the work; it does not implement the dashboard.

Limit implementation to the four files below. Keep rendering JavaScript inline in `app/index.html`; do not introduce a separate script, dependencies, or changes to existing tasks or dev container configuration.

## File assignments and responsibilities

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic page structure, inline data loading and rendering, project cards, loading/empty/error states, and accessible field labels. |
| `app/styles.css` | Designer | Visual hierarchy, typography, spacing, responsive grid, project cards, status and priority badges, contrast, and long-text handling. |
| `app/project-data.json` | Coder | Strict JSON sample data matching the agreed project schema, with varied statuses, priorities, and text lengths. |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration that serves `app/` and opens the dashboard's `index.html`. |

The Planner defines steps, dependencies, risks, and validation expectations without writing application code. The Orchestrator assigns explicit file scopes, coordinates handoffs, routes fixes to each owner, and verifies the integrated result without implementing it directly.

The Designer may review HTML and recommend semantic or accessibility changes, but the Coder owns those edits. The Coder runs executable checks; the Designer reports design decisions and review recommendations. Agents do not stage, commit, or push; the learner controls git operations through Copilot CLI.

## Shared contract before parallel implementation

Freeze these interfaces before assigning implementation work:

- Data is a top-level object with a `projects` array. Each project has nonempty string fields `name`, `owner`, `status`, `recentActivity`, and `priority`; `summary` is an optional string.
- Proposed status values are `Active`, `At risk`, `Blocked`, `On hold`, and `Completed`. Proposed priorities are `High`, `Medium`, and `Low`. These are planning assumptions, not requirements specified by the brief.
- Use fictional sample projects covering all agreed statuses and priorities, including long text and a project without an optional summary.
- HTML links to `styles.css` and fetches `project-data.json` using relative paths.
- Use `main.dashboard`, a header with one `h1` reading "Project Pulse", a live `.dashboard-message`, and `ul.project-grid` containing articles with `.project-card`.
- Each card has an `h2.project-name`, `.project-owner`, an optional `.project-summary`, `.project-activity`, `.status-badge`, and `.priority-badge`. Recent activity uses a field heading and value. Preserve literal `status`, `recentActivity`, and `priority` hooks in the HTML.
- Map agreed status and priority values to fixed lowercase, hyphenated modifiers, such as `.status-badge--at-risk`, `.priority-badge--high`, and `.project-card--priority-high`. Define neutral `--unknown` styles; do not derive arbitrary classes from unchecked data.
- CSS provides `.dashboard`, `.project-card`, rounded corners, shadows, readable spacing, responsive layout, and explicit message-state styles. Status and priority remain understandable through text, not color alone.
- Avoid external fonts, images, or CDNs. CSS custom properties belong to the Designer; Coder markup must not depend on undocumented styling details.

If either specialist needs an interface change, pause affected work and have the Orchestrator communicate the updated contract to both agents before continuing.

## Implementation phases

### 1. Contract handoff - sequential gate

The Orchestrator gives both specialists the shared contract and their file assignments. Resolve proposed schema values and any necessary markup hooks before starting parallel work.

**Exit expectation:** both specialists have the same interface and non-overlapping edit scopes.

### 2. Implementation - two parallel tracks

**Designer track: `app/styles.css` only**

1. Establish the color, spacing, and typography system.
2. Style the dashboard header, responsive project grid, and polished cards.
3. Style every agreed status, priority, unknown-value, and message-state modifier.
4. Ensure readable contrast, text wrapping, narrow-screen usability, and visible focus for any interactive elements. Respect reduced-motion preferences if motion is introduced.
5. Report design decisions and validation recommendations.

**Coder track: `app/project-data.json`, `app/index.html`, and `.vscode/launch.json`**

1. Create representative project data against the agreed schema.
2. Create semantic HTML and inline rendering logic against the shared CSS hooks.
3. Handle loading, empty data, HTTP failures, malformed JSON, and invalid records explicitly. Insert project text using `textContent`, not data-driven `innerHTML`.
4. Create the launch configuration with:
   - Name: `Run Project Pulse Dashboard`
   - Working directory: `${workspaceFolder}/app`
   - Command: `python3 -m http.server 5500`
   - Explicit browser destination ending in `/index.html`
5. Run JSON, schema, markup, and launch-configuration self-checks.

The proposed launch approach uses a `node-terminal` launch configuration and `serverReadyAction` to capture the HTTP server port and open `http://localhost:%s/index.html`. Verify this behavior in the actual Codespace rather than assuming browser forwarding works.

### 3. Integration and fixes - sequential

After both tracks finish, the Coder serves the complete app and checks rendering, asset loading, and runtime states. The Designer reviews the actual markup and styling for hierarchy, contrast, responsiveness, and accessibility.

The Orchestrator routes CSS fixes to the Designer and HTML, data, or launch fixes to the Coder. Changes to a shared interface are coordinated sequentially, with Coder markup changes followed by dependent Designer styling changes. Recheck affected behavior after fixes.

### 4. Final preview and handoff - sequential

The Orchestrator confirms the integrated result against the validation expectations. The learner selects **Run Project Pulse Dashboard** in VS Code, confirms that the styled dashboard opens rather than a directory listing, and stops the preview server when finished.

Only the learner stages, commits, and pushes the four implementation files. A separate final-handoff document is outside this implementation scope.

## Dependencies and parallel work decisions

| Work or dependency | Decision | Reason |
| --- | --- | --- |
| Shared contract before implementation | Sequential | Both specialists need agreed field names, classes, and ownership. |
| Designer CSS and Coder implementation track | Parallel | File scopes do not overlap; the frozen interface avoids waiting for each other's output. |
| Data, HTML, and launch work within the Coder track | Sequential | One owner implements the schema first, then its renderer and runnable support. |
| HTML rendering and project data | Data dependency | Renderer and sample data must agree on the schema; both belong to the Coder. |
| HTML structure and CSS selectors | Interface dependency | Parallel creation is safe only while the agreed hooks remain stable. |
| Launch configuration and `app/index.html` | Runtime dependency | Configuration can be written independently, but preview requires the app files to exist. |
| JSON fetching and HTTP server | Runtime dependency | Serve over HTTP; opening the page via `file://` is not a supported preview. |
| Integrated visual and runtime review | Sequential after both tracks | Meaningful review requires HTML, CSS, data, and launch configuration together. |
| Shared-interface fixes | Sequential | Communicate changes before dependent edits; prevent conflicting assumptions. |
| Learner preview and git operations | Sequential after validation | Confirm the integrated dashboard before learner-controlled delivery. |

## Edge cases and error behavior

- Show explicit loading, empty-project, and error messages with accessible live announcements.
- Check HTTP response status, JSON parsing, the top-level schema, and individual records. Report invalid data visibly and log diagnostic details; do not silently present fabricated values as valid data.
- Keep valid projects usable when a record is malformed, while clearly identifying the invalid record.
- Preserve unknown status or priority text with neutral styling and a clear validation warning.
- Include a JavaScript-disabled message and explain that local data loading requires the HTTP preview.
- Check long names, long activity text, missing optional summaries, and larger project lists without clipping or fixed-count assumptions.
- Handle an occupied port as an explicit startup failure. Do not terminate unrelated processes; stop only the server session started for this work.

## Validation expectations

### Static and data checks - Coder

- Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`; both must parse as strict JSON.
- Verify the `projects` array and required nonempty string fields, agreed status/priority values, and representative sample cases.
- Confirm HTML references `styles.css` and `project-data.json`, contains "Project Pulse", and includes project-card and required field hooks.
- Confirm CSS includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, and covers the agreed component and modifier classes.
- Confirm launch name, command, `${workspaceFolder}/app` working directory, and explicit `index.html` destination.

### Runtime and behavior checks - Coder

- Start the HTTP server from `app/` and verify HTTP 200 responses for `/index.html`, `/styles.css`, and `/project-data.json`.
- In a browser, verify that the number and content of rendered cards match the JSON, styles load, badges show readable labels, and there are no unexpected console errors.
- Exercise empty projects, malformed JSON, missing required fields, unknown status/priority, and a failed data request using temporary fixtures. Restore the intended sample data afterward.
- Confirm project text containing HTML characters renders as text, not executable markup.
- Verify loading ends in the correct success, empty, or error state. Stop the test server after checks.

### Visual and accessibility review - Designer with Coder/learner runtime support

- Check a 320px viewport and wider screens for readable cards, sensible grid reflow, and no horizontal overflow.
- Confirm one page `h1`, logical card headings, main/list semantics, visible status/priority text, and accessible error announcements.
- Check WCAG AA normal-text contrast of at least 4.5:1, including badges and secondary text.
- Check long-text wrapping, keyboard focus where applicable, and reduced-motion behavior if motion exists.

### Codespace launch acceptance - learner

- Launch **Run Project Pulse Dashboard** through VS Code Run and Debug.
- Verify the terminal serves `app/` and the browser opens the forwarded dashboard URL ending in `/index.html`.
- Confirm a polished Project Pulse frontend appears, not a directory listing or error page.
- Stop the launched server after preview.

Exercise workflow checks cover file existence, key phrases, and JSON parsing; they do not replace browser validation. Do not treat `scripts/validate-exercise.sh` as application acceptance: it checks the exercise template and expects learner answer files to remain untracked.

## Assumptions and uncertainties

- Fictional projects, optional summaries, free-text recent activity, and the proposed status/priority vocabulary are defaults pending product confirmation. No date field, filtering, or additional risk field is assumed.
- A dashboard summary strip is optional; decide before freezing the markup contract.
- The proposed `serverReadyAction` behavior and Codespaces forwarded-port browser opening require actual launch verification. Python output buffering may require `PYTHONUNBUFFERED=1` while preserving the specified server command.
- Browser popup blocking or port-access prompts may require learner interaction; report that limitation rather than claiming automatic launch succeeded.
