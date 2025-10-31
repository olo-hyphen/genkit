## Quick orientation for AI coding agents

This repository is a multi-language monorepo for the Genkit SDKs and developer tools. Be concise and action-oriented: your goal is to produce small, focused edits (code + tests/docs) that follow the repository conventions below.

High-level layout (important files/dirs):
- `js/` — JavaScript/TypeScript SDK, CLI, and many sample apps (production-ready). See `js/` for API surface and tests.
- `genkit-tools/` — CLI, schema exporter, telemetry server and shared tooling. Scripts here generate TypeScript/JSON schema used across SDKs (see `genkit-tools/scripts/schema-exporter.ts`).
- `go/` — Go SDK and server-side implementation (production-ready). See `go/go.mod` for third-party integrations.
- `py/` — Python workspace (Alpha). Configuration lives in `py/pyproject.toml` (tool.uv, pytest, mypy/ruff settings).
- `samples/` and `tests/` — runnable examples and higher-level test suites.

Key developer workflows (use these exact npm/pnpm scripts when applicable):
- Full JS/tooling setup: run the root setup task which installs and builds JS pieces and links the CLI: `pnpm run setup` (root `package.json` → `setup`).
- Build everything (JS + genkit-tools): `pnpm run build`.
- Link the local CLI for development (done by `pnpm run setup` but can be run manually): `cd genkit-tools/cli && npm link` (root script: `link-genkit-cli`).
- Run JS unit tests: `pnpm run test:js` (runs tests under `js/`).
- Run genkit-tools tests: `pnpm run test:genkit-tools` (invokes `genkit-tools/cli` and `genkit-tools/common` tests).
- E2E local flow: `pnpm run test:e2e-local` (builds, packs, then runs `tests/`).
- Formatting and lint: root has Biome + Prettier helpers — prefer `pnpm run format` and `pnpm run format:biome` / `pnpm run lint:biome` where appropriate. Pre-commit runs `format:check`.

Python notes:
- The Python workspace is configured in `py/pyproject.toml`. Tests are run with `pytest` (see `[tool.pytest.ini_options]`). The project uses `uv` groups (tool.uv) and lists `dev`/`lint` groups. If you change Python packaging or tests, update `py/pyproject.toml` accordingly.

Conventions & patterns to follow
- Package linking: the monorepo uses local linking for development. Root `package.json` contains "genkit": "link:js/genkit" and `pnpm` overrides. Avoid changing packaging without updating linkage scripts.
- Schema-first generation: Shared schemas live in `genkit-tools/genkit-schema.json` and are exported with `genkit-tools/scripts/schema-exporter.ts`. Changes to schema consumers often require running `pnpm --filter genkit-tools run export:schemas` (or the root `build:genkit-tools` flow).
- Small, self-contained changes: prefer edits that include a test, a tiny doc update (README or sample) and a build command that verifies the change (e.g., `pnpm run build:js` + `pnpm run test:js`).
- Formatting: run the repo format scripts before opening a PR. The repo enforces `pnpm` as the package manager (`preinstall` uses `only-allow pnpm`).

Integration & third-party notes
- The Go SDK depends on many Google Cloud libs (see `go/go.mod`) — changes touching cloud integrations may require updating that module and running `go test ./...` inside `go/`.
- The project integrates with multiple model providers via plugins. Look for plugin folders under `genkit-tools`, `plugins/` and `py/plugins` (samples reference providers in READMEs).

Examples you can copy/paste
- Build-and-test JS locally:

    pnpm run build:js
    pnpm run test:js

- Regenerate & export schemas (when changing schema or `genkit-tools` TypeScript):

    cd genkit-tools
    pnpm run export:schemas

What to avoid / gotchas
- Don't replace `pnpm` with `npm` or `yarn` in CI scripts; the repo enforces `pnpm` via `only-allow` and linked packages use `pnpm` semantics.
- Avoid wide-scope refactors in a single PR — prefer small steps that include updated builds/tests and schema exports when relevant.

References (where I looked to infer this):
- root README: `README.md`
- root scripts and orchestration: `package.json`
- genkit-tools scripts: `genkit-tools/package.json` and `genkit-tools/scripts/schema-exporter.ts`
- Go dependencies: `go/go.mod`
- Python workspace: `py/pyproject.toml`

If any of this is unclear or you'd like extra sections (CI notes, PR checklist, or example mini-tasks for agents), tell me which area to expand and I'll iterate.
