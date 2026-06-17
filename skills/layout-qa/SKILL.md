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

The CLI exits `0` on pass and `1` on failure. Treat a failing Layout QA command as actionable signal: report the failing flow, failed step, browser error, missing text, or screenshot/report path.

## Remote Tests

Remote Layout AI tests require a Layout organization API key. Use them only when the user asks for remote testing and `LAYOUT_API_KEY` or `--api-key` is available.

Example:

```bash
npx @trylayout/qa test "test checkout recovery" --repo owner/repo --ref feature-branch --api-key "$LAYOUT_API_KEY"
```

Do not invent API keys or upload URLs. If credentials are missing, explain that local checks can still run without an account.
