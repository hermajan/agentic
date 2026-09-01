---
name: frontend-assets-pipeline
description: Building, compiling, and optimizing frontend assets (SCSS, JS/TS, images) using a Node-based bundler (Gulp, Vite, Webpack/Encore, or plain NPM scripts).
---

# Frontend Assets Pipeline Skill

Use this skill when modifying stylesheets, client-side JavaScript/TypeScript, managing frontend bundling, or optimizing web assets — for projects that build assets through a Node toolchain. Check `composer.json`/`package.json` first: a Symfony project using `symfony/asset-mapper` (with `symfonycasts/sass-bundle` for Sass) has no Node build step at all — use the `symfony-asset-mapper` skill instead, don't assume a `npm run build`/`npm run sass` step exists.

## 1. Tooling & Asset Structure

- **Asset Sources**: Usually located in `assets/`, `www/src/`, or module-specific asset folders.
- **Compiled Output**: Placed into `www/dist/`, `www/css/`, `www/js/`, or `public/build/`.
- **Bundler / Task Runner**: Gulp (`gulpfile.js`), Vite (`vite.config.js`), or Webpack (`webpack.config.js`).

## 2. Common Build Commands

```bash
# Install node dependencies
docker compose exec app npm install

# Build assets for production (minified + hashed)
docker compose exec app npm run build

# Start live development watcher / hot-reload
docker compose exec app npm run dev
# or:
docker compose exec app npm run watch
```

## 3. Best Practices

- **SCSS / CSS**:
  - Use modular SCSS files (variables, mixins, components, layouts).
  - Avoid deeply nested selectors (max 3 levels deep).
  - Ensure responsive breakpoints and accessibility standards (contrast ratios, focus states).
- **JavaScript / TypeScript**:
  - Avoid polluting global window objects; use ES modules.
  - Implement debouncing for search input handlers or real-time filter queries.
- **Cache Busting**:
  - Ensure compiled assets are referenced with content hashes or version query parameters (e.g., `main.css?v=1.2.0` or `main.[hash].js`).
