# Layout QA Codex Marketplace

This repository publishes the Codex plugin marketplace for [Layout](https://trylayout.com).

It currently includes the Layout QA plugin, which teaches Codex when and how to use the `@trylayout/qa` npm package for frontend visual QA:

- initialize `.layout/qa.json`
- run local Playwright-backed browser checks
- serve deterministic mock API scenarios
- inspect Layout QA screenshots and HTML reports
- trigger remote Layout tests when a Layout API key is available

## Install

Add the Layout marketplace:

```bash
codex plugin marketplace add Layout-App/codex-layout-qa-plugin --ref main
```

Install the Layout QA plugin:

```bash
codex plugin add layout-qa@layout
```

After installing, start a new Codex thread so the plugin skill is available.

## Structure

```text
.agents/plugins/marketplace.json
plugins/layout-qa/.codex-plugin/plugin.json
plugins/layout-qa/assets/layout-qa-logo.svg
plugins/layout-qa/skills/layout-qa/SKILL.md
```

The plugin does not vendor the Layout QA CLI. It invokes the public npm package with commands such as:

```bash
npx @trylayout/qa check --start-app --skip-install --json
npx @trylayout/qa check --target-url http://localhost:5173 --scenario happy_path --json
npx @trylayout/qa init
```

## Local Development

Validate the plugin:

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py plugins/layout-qa
```

Test the marketplace locally:

```bash
codex plugin marketplace add /path/to/codex-layout-qa-plugin
codex plugin add layout-qa@layout
```

## Related Package

- npm package: [`@trylayout/qa`](https://www.npmjs.com/package/@trylayout/qa)
- source repository: [`Layout-App/layout-qa`](https://github.com/Layout-App/layout-qa)
