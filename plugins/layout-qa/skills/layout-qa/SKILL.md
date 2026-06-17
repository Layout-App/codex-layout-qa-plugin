---
name: layout-qa
description: Use when the user asks to set up, run, debug, or interpret Layout QA visual/browser tests for a frontend app, including local Playwright-backed checks, mock API scenarios, screenshots, HTML reports, or Layout remote AI tests.
---

# Layout QA

Use this skill to run browser QA for frontend changes with the `@trylayout/qa` npm package.

## When To Invoke

Use Layout QA when the user asks for visual QA, browser QA, layout testing, frontend smoke tests, screenshot reports, deterministic mock API scenarios, or to validate a UI change in a browser.

Do not use it for unit tests, pure backend changes, static code review, or generic Playwright authoring unless the user specifically wants Layout QA.

## Package

- Canonical package: `@trylayout/qa`
- Alias package: `layout-qa`
- CLI binaries: `trylayout` and `layout-qa`
- Requires Node.js 18 or newer.

Prefer `npx @trylayout/qa ...` in commands so the canonical package is explicit. The shorter `npx layout-qa ...` alias is acceptable when the local project or user already uses it.

## Standard Workflow

1. Inspect the project for frontend package scripts, existing `.layout/qa.json`, mock API configuration, and the app's expected local URL.
2. If Chromium for Playwright is missing, run:

```bash
npx @trylayout/qa install-browsers
```

3. If the repo already has `.layout/qa.json` with an `app` block, prefer:

```bash
npx @trylayout/qa check --start-app --skip-install --json
```

Use `--open` only when the user wants the generated HTML report opened locally.

4. If the app is already running, run a targeted check:

```bash
npx @trylayout/qa check --target-url http://localhost:5173 --scenario happy_path --json
```

5. If no Layout manifest exists and the user asked to set up visual QA, create the starter files:

```bash
npx @trylayout/qa init
```

Then update `.layout/qa.json` to match the app's real start command, QA environment flags, useful viewports, and high-value flows.

## Mock API Scenarios

Layout QA can serve deterministic mock responses from `.layout/mocks/scenarios`.

Start only the mock API when the app needs a mock backend in a separate terminal:

```bash
npx @trylayout/qa mock-api --scenario happy_path
```

When `.layout/qa.json` has `mockApi` and `app` configuration, `check --start-app` automatically serves mocks and exposes `$LAYOUT_MOCK_API_URL` to the app command.

Common QA environment variables are project-specific, but often include:

```bash
LAYOUT_QA=1
VITE_LAYOUT_QA=1
VITE_API_BASE_URL=$LAYOUT_MOCK_API_URL
```

## Reports And Results

Layout QA writes local artifacts under `.layout/runs/<timestamp-scenario-viewport>/` by default:

- `index.html`
- `result.json`
- `screenshots/`

After a run, inspect `result.json` for pass/fail status and cite the report path. When visual quality matters, inspect screenshots before concluding that the change is good.

## Visual Review Requirements

Do not treat a passed Layout QA run as proof that the UI looks correct. Layout QA can verify browser health and scripted assertions, but visual QA requires direct screenshot review.

Before saying a page or flow "looks good":

1. Build a screenshot inventory from the report artifacts and group screenshots by viewport, route, page family, scenario, and component state.
2. Open or render the relevant screenshots. If there are related pages at the same viewport, inspect them together instead of one at a time.
3. Compare shared layout landmarks across the group:
   - header and body left/right edges
   - nav, logo, and content container widths
   - hero, article, card, and footer alignment
   - vertical rhythm between shared sections
   - text wrapping, clipping, overflow, and collision
   - repeated component sizing and spacing
4. Look for cross-page inconsistencies even when every individual page is readable. For example, a docs page and a privacy page can each render successfully while their header contents and body contents are misaligned because they use different max-width containers.
5. Report visual findings separately from automated pass/fail status. Use language such as "automated checks passed, but visual review found..." when screenshots show alignment, spacing, hierarchy, or responsive issues.

For multi-page site QA, capture the same viewport for each important route, then compare those screenshots as a set. For desktop documentation/legal pages, explicitly verify that the header, article body, and footer use a coherent horizontal grid. For mobile, explicitly check that nav, headings, and body content do not overflow or shift unexpectedly between sibling pages.

The CLI exits `0` on pass and `1` on failure. Treat a failing Layout QA command as actionable signal: report the failing flow, failed step, browser error, missing text, or screenshot/report path.

## Remote Tests

Remote Layout AI tests require a Layout organization API key. Use them only when the user asks for remote testing and `LAYOUT_API_KEY` or `--api-key` is available.

Example:

```bash
npx @trylayout/qa test "test checkout recovery" --repo owner/repo --ref feature-branch --api-key "$LAYOUT_API_KEY"
```

Do not invent API keys or upload URLs. If credentials are missing, explain that local checks can still run without an account.
