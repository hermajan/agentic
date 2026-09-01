---
name: symfony-asset-mapper
description: Managing JavaScript and CSS/Sass assets in Symfony via AssetMapper (PHP-native import maps) and SymfonyCasts SassBundle — no Node/npm build step involved.
---

# Symfony Asset Mapper & Sass Skill

Use this skill for any JavaScript, Stimulus controller, or stylesheet work in a Symfony project that uses `symfony/asset-mapper` (and, for Sass, `symfonycasts/sass-bundle`) instead of a Node bundler. This is Symfony's modern default asset pipeline: no `package.json`, no `npm install`/`npm run build`, no Webpack/Vite config — everything is driven by Composer packages and `bin/console`. Check `composer.json` for `symfony/asset-mapper` and `symfonycasts/sass-bundle` before assuming this applies; if the project instead has a `package.json` with a build script (Encore, a standalone Vite/Webpack setup), use `frontend-assets-pipeline` instead.

## 1. JavaScript via AssetMapper + Import Maps

- **Source**: Plain ES modules under `assets/` (e.g. `assets/app.js`, `assets/controllers/*.js`) — no bundling/transpilation step; what you write is what ships (modern browser-native ES modules and `importmap`).
- **Third-party packages**: Never `npm install` a JS dependency — resolve it through the import map instead:
  ```bash
  # Add a package (downloads it into assets/vendor/ and registers it in importmap.php)
  php bin/console importmap:require <package-name>

  # Remove a package
  php bin/console importmap:remove <package-name>

  # Update all pinned packages to their latest matching version
  php bin/console importmap:update

  # Re-download vendor assets after a fresh checkout (also runs automatically via composer post-install in most setups)
  php bin/console importmap:install
  ```
- **Registry**: `importmap.php` at the project root is the source of truth for every mapped package/entrypoint — read it before adding a duplicate or guessing a package is missing.
- **Stimulus controllers**: Registered in `assets/controllers.json` (and autoloaded from `assets/controllers/`) via `symfony/stimulus-bundle` — use `php bin/console make:stimulus-controller <name>` to scaffold one rather than hand-wiring the registration.
- **Referencing assets in Twig**: Use `{{ asset('styles/app.css') }}` / the `importmap()` Twig function for entrypoints — never hardcode a `/build/...` or `/assets/...` path, since AssetMapper adds a content hash in production.

## 2. Sass via SymfonyCasts SassBundle

- **Config**: `config/packages/symfonycasts_sass.yaml` lists each root `.scss` entrypoint to compile (e.g. `assets/styles/app.scss`); partials (`_*.scss`) are imported by an entrypoint, not compiled standalone — check `asset_mapper.yaml`'s `excluded_patterns` for how partials are kept out of the asset map directly.
- **Compiling**:
  ```bash
  # One-off build of all configured entrypoints
  php bin/console sass:build

  # Watch and rebuild on change during local development
  php bin/console sass:build --watch
  ```
- No Dart Sass / node-sass install is required — the bundle manages its own Sass binary. Don't reach for `npm run sass` or a Gulp task; there isn't one.
- Keep shared variables/mixins in partials imported by the root entrypoint(s) listed in `symfonycasts_sass.yaml`, same modular-SCSS discipline as `frontend-assets-pipeline` describes generically.

## 3. Production Build

```bash
# Compile and version-hash all mapped assets for deployment (run once, e.g. in CI/deploy)
php bin/console asset-map:compile
```

Run `sass:build` before `asset-map:compile` if compiled CSS isn't already checked in/up to date — `asset-map:compile` maps whatever is currently on disk, it does not invoke Sass compilation itself.

## 4. Common Pitfalls

- Don't add a `package.json`/npm step "to be safe" alongside AssetMapper — it's redundant and the two pipelines will drift.
- Don't hand-edit `importmap.php`'s downloaded vendor shims under `assets/vendor/`; re-run `importmap:require`/`importmap:update` instead so the lockfile-equivalent state stays consistent.
- Cache/version issues after adding an asset are usually fixed by `php bin/console cache:clear` + `php bin/console asset-map:compile`, not by clearing browser cache first.
