# Layout QA Codex Plugin

This repository contains the Codex plugin for [Layout QA](https://github.com/Layout-App/layout-qa).

The plugin teaches Codex when and how to use the `@trylayout/qa` npm package for frontend visual QA:

- initialize `.layout/qa.json`
- run local Playwright-backed browser checks
- serve deterministic mock API scenarios
- inspect Layout QA screenshots and HTML reports
- trigger remote Layout tests when a Layout API key is available

## Structure

```text
.codex-plugin/plugin.json
assets/layout-qa-logo.svg
skills/layout-qa/SKILL.md
```

The plugin does not vendor the Layout QA CLI. It invokes the public npm package with commands such as:

```bash
npx @trylayout/qa check --start-app --skip-install --json
npx @trylayout/qa check --target-url http://localhost:5173 --scenario happy_path --json
npx @trylayout/qa init
```

## Local Development

Validate the plugin with the plugin-creator validator:

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py .
```

For local testing in Codex, point a personal marketplace entry at this repo or copy the repo contents into `~/plugins/layout-qa`.

## Related Package

- npm package: [`@trylayout/qa`](https://www.npmjs.com/package/@trylayout/qa)
- source repository: [`Layout-App/layout-qa`](https://github.com/Layout-App/layout-qa)
