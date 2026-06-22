---
name: layout-qa
description: Use when the user asks to set up, run, debug, or interpret Layout QA visual/browser tests for a frontend app, including local protocol checks, mock API scenarios, screenshots, and HTML reports.
---

# Layout QA

Use this skill to run frontend QA for coding agents with the `@trylayout/qa`
npm package. The product surface is an open source repo-local protocol:
`trylayout setup` creates the protocol files, and `trylayout test` runs a local
browser QA pass from the active coding session.

## When To Invoke

Use Layout QA when the user asks for visual QA, browser QA, frontend QA,
layout testing, screenshot reports, deterministic mock API scenarios, or to
validate a UI change in a browser.

Do not use it for unit tests, pure backend changes, static code review, or
generic Playwright authoring unless the user specifically wants Layout QA.

## Package

- Canonical package: `@trylayout/qa`
- Alias package: `layout-qa`
- CLI binaries: `trylayout` and `layout-qa`
- Requires Node.js 18 or newer.

Prefer `npx @trylayout/qa ...` in commands so the canonical package is
explicit. The shorter `npx layout-qa ...` alias is acceptable when the local
project or user already uses it.

## Standard Workflow

1. Inspect the project for frontend package scripts, existing `.layout/qa.json`,
   mock API configuration, and the app's expected local URL.

2. If the repo is not set up for Layout QA, run:

```bash
npx @trylayout/qa setup
```

Then review `.layout/qa.json` and update it to match the app's real install
command, start command, QA environment flags, useful viewports, and high-value
flows.

3. If Chromium for Playwright is missing, run:

```bash
npx @trylayout/qa install-browsers
```

4. Run the normal QA pass:

```bash
npx @trylayout/qa test "test the changed checkout flow" --json
```

Use a natural-language intent that matches the user's request. The current
local protocol uses the manifest flows and artifacts as the source of truth, so
the intent is helpful task context for the local QA pass.

5. If the app is already running and the user wants to test that instance, run:

```bash
npx @trylayout/qa test "check the current page" \
  --target-url http://localhost:5173 \
  --json
```

6. Inspect `result.json` and the generated screenshots before summarizing. Cite
the artifact paths and report concrete issues the coding agent should fix.

## Explicit Flow Checks

Use explicit flow checks when the user names a particular manifest flow or when
you need narrower CI-style coverage:

```bash
npx @trylayout/qa check smoke --start-app --skip-install --json
```

Use `--open` only when the user wants the generated HTML report opened locally.

## Mock API Scenarios

Layout QA can serve deterministic mock responses from manifest services such as
`.layout/api/scenarios`.

Start only the mock API when the app needs a mock backend in a separate
terminal:

```bash
npx @trylayout/qa mock-api --scenario happy_path
```

When `.layout/qa.json` has manifest services, `test` and
`check --start-app` can start them and expose service URLs to the app command.

Common manifest QA environment entries are project-specific, but often include:

```json
{
  "LAYOUT_QA": "1",
  "VITE_LAYOUT_QA": "1",
  "VITE_API_BASE_URL": "$services.api.url"
}
```

## Reports And Results

Layout QA writes local artifacts under
`.layout/runs/<timestamp-scenario-viewport>/` by default:

- `index.html`
- `result.json`
- `screenshots/`

The CLI exits `0` on pass and `1` on failure. Treat a failing Layout QA command
as actionable signal: report the failing flow, failed step, browser error,
missing text, or screenshot/report path.

## Visual Review Requirements

Do not treat a passed Layout QA run as proof that the UI looks correct. Layout
QA can verify browser health and scripted assertions, but visual QA requires
direct screenshot review.

Before saying a page or flow "looks good":

1. Build a screenshot inventory from the report artifacts and group screenshots
   by viewport, route, page family, scenario, and component state.
2. Open or render the relevant screenshots. If there are related pages at the
   same viewport, inspect them together instead of one at a time.
3. Compare shared layout landmarks across the group:
   - header and body left/right edges
   - nav, logo, and content container widths
   - hero, article, card, and footer alignment
   - vertical rhythm between shared sections
   - text wrapping, clipping, overflow, and collision
   - repeated component sizing and spacing
4. Look for cross-page inconsistencies even when every individual page is
   readable. Related pages can each render successfully while their header
   contents and body contents are misaligned because they use different
   max-width containers.
5. Report visual findings separately from automated pass/fail status. Use
   language such as "automated checks passed, but visual review found..." when
   screenshots show alignment, spacing, hierarchy, or responsive issues.

For multi-page site QA, capture the same viewport for each important route, then
compare those screenshots as a set. For desktop documentation/legal pages,
explicitly verify that the header, article body, and footer use a coherent
horizontal grid. For mobile, explicitly check that nav, headings, and body
content do not overflow or shift unexpectedly between sibling pages.
