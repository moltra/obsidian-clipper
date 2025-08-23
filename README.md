Obsidian Web Clipper helps you highlight and capture the web in your favorite browser. Anything you save is stored as durable Markdown files that you can read offline, and preserve for the long term.

- **[Download Web Clipper](https://obsidian.md/clipper)**
- **[Documentation](https://help.obsidian.md/web-clipper)**
- **[Troubleshooting](https://help.obsidian.md/web-clipper/troubleshoot)**

## Get started

Install the extension by downloading it from the official directory for your browser:

- **[Chrome Web Store](https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf)** for Chrome, Brave, Arc, Orion, and other Chromium-based browsers.
- **[Firefox Add-Ons](https://addons.mozilla.org/en-US/firefox/addon/web-clipper-obsidian/)** for Firefox and Firefox Mobile.
- **[Safari Extensions](https://apps.apple.com/us/app/obsidian-web-clipper/id6720708363)** for macOS, iOS, and iPadOS.
- **[Edge Add-Ons](https://microsoftedge.microsoft.com/addons/detail/obsidian-web-clipper/eigdjhmgnaaeaonimdklocfekkaanfme)** for Microsoft Edge.

## Use the extension

Documentation is available on the [Obsidian Help site](https://help.obsidian.md/web-clipper), which covers how to use [highlighting](https://help.obsidian.md/web-clipper/highlight), [templates](https://help.obsidian.md/web-clipper/templates), [variables](https://help.obsidian.md/web-clipper/variables), [filters](https://help.obsidian.md/web-clipper/filters), and more.

## Contribute

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for PR process, coding standards, and commit conventions.

### Translations

You can help translate Web Clipper into your language. Submit your translation via pull request using the format found in the [/_locales](/src/_locales) folder.

### Features and bug fixes

See the [help wanted](https://github.com/obsidianmd/obsidian-clipper/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22) tag for issues where contributions are welcome.

## Roadmap

In no particular order:

- [ ] A separate icon for Web Clipper
- [ ] Translate UI into more languages — help is welcomed
- [ ] Annotate highlights
- [ ] Template directory
- [ ] Template validation
- [ ] Template logic (if/for)
- [x] Save images locally, [added in Obsidian 1.8.0](https://obsidian.md/changelog/2024-12-18-desktop-v1.8.0/)

## Developers

### Prerequisites

- Node.js 18+ (LTS) and npm
- Optional: Node version pinning via `.nvmrc` (use `nvm use`)
- macOS with Xcode for Safari development/signing (optional)

### Install dependencies

```bash
npm ci    # reproducible, uses package-lock.json
# or
npm install
```

### Quick start

```bash
npm ci
npm run dev:chrome
# then load the ./dev folder in chrome://extensions (see below)
```

To build the extension:

```
npm run build
```

This will create three directories:
- `dist/` for the Chromium version
- `dist_firefox/` for the Firefox version
- `dist_safari/` for the Safari version

### Develop (watch mode)

```bash
npm run dev:chrome   # Chromium
npm run dev:firefox  # Firefox
npm run dev:safari   # Safari
```

Outputs to:
- `dev/` (Chromium)
- `dev_firefox/` (Firefox)
- `dev_safari/` (Safari)

#### Load dev builds

- Chromium: load the `./dev` folder in `chrome://extensions`
- Firefox: use `about:debugging#/runtime/this-firefox` → Load Temporary Add-on → select `./dev_firefox/manifest.json`
- Safari: run via the Xcode project; `dev_safari/` is populated by Webpack

#### Generated artifacts

- Do not edit files under `dev*/`, `dist*/`, or `builds/`. They are generated.

### Packaging (production zips)

- Production builds also write zip archives to `builds/` as `obsidian-web-clipper-${version}-{chrome|firefox|safari}.zip`.

#### Release workflow (summary)

- Update `package.json` version.
- `npm ci && npm run build`
- Verify `dist*/` outputs and generated zips in `builds/`.
- Test each browser manually before publishing to stores.

### Developer utilities

- `npm run add-locale` — scaffold a new locale in `src/_locales/`.
- `npm run update-locales` — sync strings.
- `npm run check-strings` — find unused strings.
- `npm run defuddle-dev` / `npm run defuddle-prod` — toggle the content extraction backend.
- `defuddle-dev` expects a sibling repo at `../defuddle` (clone it next to this project).

### Localization workflow

- Create `.env` in the project root with an OpenAI key for localization scripts:
  ```env
  OPENAI_API_KEY=sk-...
  ```
- Update locales (translate missing and alphabetize):
  ```bash
  npm run update-locales
  ```
- Add a new locale (example for French):
  ```bash
  npm run add-locale fr
  ```
- See `scripts/readme.md` for details.

### Model providers & presets

- Presets are fetched from `https://raw.githubusercontent.com/obsidianmd/obsidian-clipper/refs/heads/main/providers.json` at runtime by `src/managers/interpreter-settings.ts` (`PROVIDERS_URL`). The file includes a `version` used to detect updates.
- Presets are cached for ~5 minutes in local storage under key `provider_presets`; on failure, the UI falls back to the cached copy.
- To force-refresh/remove the cache, clear Local Storage key `provider_presets` in DevTools, or run:
  ```js
  browser.storage.local.remove('provider_presets')
  ```
- Configure providers and models in the extension Settings UI (Interpreter). Popular models and docs links are shown per provider.
- API keys are stored in browser sync storage (`interpreter_settings.providers[*].apiKey`) via `src/utils/storage-utils.ts`. They never leave your browser unless your browser account syncs extensions data.
- You can inspect storage for debugging with:
  ```js
  // all sync storage
  window.debugStorage()
  // specific key
  window.debugStorage('interpreter_settings')
  ```
- To add or change default presets, submit a PR updating `providers.json`. Users can always create a Custom provider from the UI.

### Linting & code style

- ESLint is configured in `.eslintrc.json` (env: `browser`, `es2021`, `webextensions`; extends: `eslint:recommended`; indentation: tabs).
- Run lint manually:
  ```bash
  npx eslint "src/**/*.{ts,js}" --max-warnings=0
  # optional: auto-fix
  npx eslint "src/**/*.{ts,js}" --fix
  ```

### TypeScript configuration

- `tsconfig.json`: `target`=`es6`, `module`=`es6`, `strict`=true, `lib`=`es2017, dom`.
- Path aliases: `managers/*`, `utils/*`, `icons`. Types include `chrome`, `webextension-polyfill`, `node`, `webpack-env`.
- Source maps are enabled in development builds.

### Coding guidelines

- Keep modules small and focused; avoid `any` (strict mode on).
- Prefer path aliases from `tsconfig.json` over relative paths.
- Localize user-facing strings in `src/_locales/`.
- Do not commit generated artifacts (`dev*/`, `dist*/`, `builds/`).

### Security considerations

- Never inject unsanitized HTML into the DOM; use `DOMPurify`.
- Avoid `eval`/Function constructor. CSP is set to `script-src 'self'` in manifests.
- Minimize permissions and keep parity across manifests when changing capabilities.

### Project structure (high-level)

- `src/core/` — extension entry points like `popup.ts`, `settings.ts`.
- `src/managers/` — feature-specific logic and services.
- `src/_locales/` — i18n message bundles.
- `src/icons/` — extension icons.
- `scripts/` — maintenance utilities (locales, defuddle, checks).

### Permissions

- Core permissions: `activeTab`, `clipboardWrite`, `contextMenus`, `storage`, `scripting`
- Chromium-only: `sidePanel`
- Host permissions: `<all_urls>` (http/https)

#### Browser differences (manifest)

- Chromium: background `service_worker`
- Firefox: background `scripts`
- Safari: same surface as Chromium but packaged via Xcode project

### Keyboard shortcuts

- Open Clipper
- Quick Clip
- Toggle Highlighter
- Toggle Reader

Defaults vary by browser; customize via your browser’s “Extension shortcuts” settings.

### Debugging

- Dev builds (`npm run dev:*`) set `DEBUG_MODE=true` via Webpack.
- In DevTools Console, you can toggle verbose logs:
  ```js
  // enable/disable runtime debug logging
  toggleDebug('general')
  ```
- Logs are emitted only when `DEBUG_MODE` and runtime debug flag are both on, through `debugLog(tag, ...args)` in `src/utils/debug.ts`.
 - `process.env.NODE_ENV` is defined at build-time via Webpack's DefinePlugin.

### Windows note (defuddle scripts)

- `scripts/set-defuddle-prod.js` uses a POSIX command (`rm -rf`). On Windows, run from Git Bash/WSL, or manually delete `node_modules/defuddle` then run `npm install`.

### Safari development

- Use the Xcode project under `xcode/` for running/signing on Apple platforms. Webpack `dev:safari`/`build:safari` populate `dev_safari/` and `dist_safari/`.
- Open `xcode/Obsidian Web Clipper/Obsidian Web Clipper.xcodeproj` in Xcode.
- In Targets, configure both the application and extension targets:
  - Set Signing & Capabilities → Team to your Apple Developer Team.
  - Resolve any signing warnings (bundle IDs, provisioning).
- macOS: select a "My Mac" destination and Run. Then enable the extension in Safari → Settings → Extensions.
- iOS/iPadOS: select a Simulator or device and Run. Then enable in Settings → Safari → Extensions.
- If assets appear outdated, re-run `npm run dev:safari` or `npm run build:safari` so Xcode picks up the latest `dev_safari/` or `dist_safari/` contents.

### Install the extension locally

For Chromium browsers, such as Chrome, Brave, Edge, and Arc:

1. Open your browser and navigate to `chrome://extensions`
2. Enable **Developer mode**
3. Click **Load unpacked** and select the `dist` directory

For Firefox:

1. Open Firefox and navigate to `about:debugging#/runtime/this-firefox`
2. Click **Load Temporary Add-on**
3. Navigate to the `dist_firefox` directory and select the `manifest.json` file

If you want to run the extension permanently you can do so with the Nightly or Developer versions of Firefox.

1. Type `about:config` in the URL bar
2. In the Search box type `xpinstall.signatures.required`
3. Double-click the preference, or right-click and select "Toggle", to set it to `false`.
4. Go to `about:addons` > gear icon > **Install Add-on From File…**

### Troubleshooting (development)

- Service worker logs (Chromium): `chrome://extensions` → Inspect service worker in the extension card. Watch for errors on startup.
- Force reload after source changes: in `chrome://extensions`, toggle the extension off/on or click Reload.
- Clear provider presets cache if provider list looks stale:
  ```js
  // Run in the extension's DevTools console
  browser.storage.local.remove('provider_presets')
  ```
- Reset interpreter configuration if models/providers are corrupted:
  ```js
  browser.storage.sync.remove('interpreter_settings')
  ```
- Inspect sync storage for debugging (defined in `src/utils/storage-utils.ts`):
  ```js
  window.debugStorage()                     // dump all sync storage
  window.debugStorage('interpreter_settings')
  ```
- Localization scripts require an OpenAI key in `.env`. See `scripts/readme.md` and the Localization workflow above.
- Firefox temporary add-ons are removed on restart; re-load from `dev_firefox/` when needed.

## Third-party libraries

- [webextension-polyfill](https://github.com/mozilla/webextension-polyfill) for browser compatibility
- [defuddle](https://github.com/kepano/defuddle) for content extraction
- [turndown](https://github.com/mixmark-io/turndown) for HTML to Markdown conversion
- [dayjs](https://github.com/iamkun/dayjs) for date parsing and formatting
- [lz-string](https://github.com/pieroxy/lz-string) to compress templates to reduce storage space
- [lucide](https://github.com/lucide-icons/lucide) for icons
- [mathml-to-latex](https://github.com/asnunes/mathml-to-latex) for MathML to LaTeX conversion
- [dompurify](https://github.com/cure53/DOMPurify) for sanitizing HTML
