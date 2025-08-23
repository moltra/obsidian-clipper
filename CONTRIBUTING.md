# Contributing to Obsidian Web Clipper

Thank you for contributing! This document outlines how to propose changes and the standards used in this repository.

## Pull Request Process

- Keep PRs focused and small; separate unrelated changes.
- Include documentation updates for user/developer docs when behavior changes (`README.md`, docs/).
- Ensure builds pass and no new ESLint warnings are introduced.
- Reference related issues in the PR description (e.g., "Fixes #123").
- Maintainers may request changes for clarity, tests, or docs before merging.

## Branching & Commit Conventions

- Branch names: `feature/<short-desc>`, `fix/<short-desc>`, `docs/<short-desc>`.
- Use Conventional Commits for messages:
  - `feat: ...`, `fix: ...`, `docs: ...`, `refactor: ...`, `chore: ...`.
  - Scope examples: `feat(reader): add math support`.

## Development Setup

- Node: use LTS (pinned via `.nvmrc`).
- Install deps: `npm ci`.
- Dev builds:
  - Chromium: `npm run dev:chrome`  load `./dev` in `chrome://extensions`.
  - Firefox: `npm run dev:firefox`  load `./dev_firefox/manifest.json` via `about:debugging`.
  - Safari: `npm run dev:safari` and open the Xcode project under `xcode/`.
- Production build: `npm run build` (outputs `dist*/` and zips in `builds/`).

## Localization

- Requires `.env` with `OPENAI_API_KEY=...`.
- Update strings: `npm run update-locales`.
- Add locale: `npm run add-locale <lang>`.
- See `scripts/readme.md`.

## Content extraction backend (defuddle)

- Switch to local dev: `npm run defuddle-dev` (expects `../defuddle`).
- Switch back to published version: `npm run defuddle-prod`.
- Windows: `defuddle-prod` uses POSIX `rm -rf`; use Git Bash/WSL or remove `node_modules/defuddle` manually then `npm install`.

## Linting & Code Style

- ESLint is configured in `.eslintrc.json`.
- Zero warnings: `npx eslint "src/**/*.{ts,js}" --max-warnings=0`.
- Prefer TypeScript strictness; avoid `any`.
- Keep functions small and single-responsibility; favor readability and DRY.
- Use path aliases from `tsconfig.json`.
- Localize user-visible text (`src/_locales/`).

## Security & Permissions

- Do not inject unsanitized HTML; sanitize content (e.g., DOMPurify).
- Avoid `eval`/Function constructor; respect CSP (`script-src 'self'`).
- Minimize and justify permission changes across manifests; keep parity where possible.

## Testing Expectations

- Include tests when introducing core logic or complex flows where feasible.
- Cover edge cases and failure modes.

## Troubleshooting (Dev)

- Service worker logs: `chrome://extensions`  Inspect.
- Clear provider presets cache:
  ```js
  browser.storage.local.remove('provider_presets')
  ```
- Inspect sync storage (from `src/utils/storage-utils.ts`):
  ```js
  window.debugStorage()
  window.debugStorage('interpreter_settings')
  ```

## Releases

- Store publishing is handled by maintainers. PRs should not modify store listings or signing configs.
