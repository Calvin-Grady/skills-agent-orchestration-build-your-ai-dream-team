# Project Pulse implementation plan

## Goal

Build a lightweight static dashboard that lets contributors quickly understand
active projects, ownership, status, recent activity, and priority or risk. The
dashboard should present the information in an accessible, responsive card
layout and open directly in a browser from VS Code.

## Implementation phases

### 1. Define the dashboard experience

- **Designer** owns the visual and accessibility direction for the dashboard:
  information hierarchy, card layout, status and priority treatments,
  responsive behavior, typography, spacing, and accessible contrast/semantics.
- Establish the shared UI contract: a `.dashboard` container and a
  `.project-card` for each project, with clear display of name, owner, status,
  recent activity, and priority.
- **Dependency:** agree on the UI contract before integrating the rendered
  project data.

### 2. Build the static dashboard

- **Coder** owns `app/index.html`, `app/project-data.json`, and
  `.vscode/launch.json`.
- **Designer** owns `app/styles.css`, applying the approved layout and
  accessibility direction.
- Define a top-level `projects` array in `app/project-data.json`; each project
  includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- In `app/index.html`, set the title to **Project Pulse**, reference
  `styles.css` and `project-data.json`, and render the data as visible project
  cards using the `project-card` class.
- In `app/styles.css`, provide the `.dashboard` and `.project-card` selectors,
  polished card styling including rounded corners and shadows, and responsive
  layout.
- Configure `.vscode/launch.json` as strict JSON with a **Run Project Pulse
  Dashboard** configuration. Serve from `${workspaceFolder}/app` using
  `python3 -m http.server 5500` and open
  `http://localhost:%s/index.html` so the browser displays the app rather than
  a directory listing.
- **Dependencies:** the HTML rendering depends on the agreed fields in the
  JSON data. The launch configuration depends on the static app path and server
  command.

### 3. Integrate and validate

- **Orchestrator** checks that Designer's direction and Coder's implementation
  align with this plan, and resolves any file-contract mismatch.
- Validate that the JSON parses and has the required top-level key and fields.
- Check that the HTML references both supporting files and renders one
  `.project-card` per project with all required information.
- Check that the CSS includes the required selectors and readable responsive
  styling.
- Parse `.vscode/launch.json` as JSON and confirm its name, working directory,
  command, and browser URL match the requirements.
- Run **Run Project Pulse Dashboard** in VS Code and confirm the browser opens
  `index.html` and shows the dashboard. Stop the preview server after checking.

## Dependencies and parallel work

- Designer's initial UI direction and Coder's data/markup contract should be
  agreed before final styling and rendering are integrated.
- After that contract is set, Designer can style `app/styles.css` while Coder
  builds `app/project-data.json` and `.vscode/launch.json` in parallel.
- Coder's `app/index.html` can be developed alongside the JSON shape once the
  field names and card hooks are fixed; final integration must verify that the
  renderer uses the same fields and classes as the stylesheet.
- The Orchestrator performs integration review after the parallel file work;
  validation and launch testing follow integration.

## Edge cases

- Ensure the page handles an empty `projects` array with a clear empty state
  instead of appearing broken.
- Treat project values as text content so data is displayed safely and remains
  readable when summaries or names are long.
- Keep status and priority understandable through text and not color alone.
- Check narrow viewports and long activity summaries for overflow or cramped
  cards.
- Fetching local JSON requires serving the `app/` directory over HTTP; opening
  the HTML as a `file://` URL is not the supported launch path.
- Use the configured port consistently and ensure the launch URL targets
  `index.html`, not the server root.

## Validation expectations

- `python3 -m json.tool app/project-data.json`
- `python3 -m json.tool .vscode/launch.json`
- Inspect `app/index.html` to confirm title, stylesheet and JSON references,
  accessible page structure, and project-card rendering of every required
  field.
- Inspect `app/styles.css` for `.dashboard`, `.project-card`, responsive
  behavior, readable spacing, `border-radius`, and `box-shadow`.
- Start the VS Code launch configuration and verify it serves `app/` and opens
  the dashboard UI at `/index.html`.

## File assignments

| File | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Dashboard structure, data loading, and project-card rendering |
| `app/styles.css` | Designer | Responsive visual styling and accessible information hierarchy |
| `app/project-data.json` | Coder | Sample projects and the required data contract |
| `.vscode/launch.json` | Coder | VS Code launch configuration for serving and opening the dashboard |
| `docs/project-pulse-plan.md` | Orchestrator | Record this plan and the implementation handoff |
