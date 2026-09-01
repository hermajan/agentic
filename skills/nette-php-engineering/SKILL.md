---
name: nette-php-engineering
description: Specialized guidance for developing, reviewing, and maintaining applications built on Nette Framework, Latte templates, Neon configs, and PHP constraints.
---

# Nette Framework & PHP Engineering Skill

Use this skill when writing, reviewing, or refactoring PHP code, Presenters, UI Components, Neon configurations, and Latte templates.

## 1. Core Architecture & Conventions

- **Framework**: Nette Framework (`nette/nette` 3.x / 4.x).
- **PHP Compatibility**: Verify the target PHP version (e.g., PHP 7.4 vs PHP 8.x). Strictly avoid language features newer than the project target runtime (such as enums, readonly properties, or match expressions when on PHP 7.4).
- **Directory Structure**:
  - `app/` — Domain modules, Presenters, Forms, and Core logic.
  - `app/config/` — Neon configuration files.
  - `app/models/` — Shared Domain Facades, Entities, and Repositories.
  - `www/` — Public web root and entrypoint (`index.php`).

## 2. Presenter & Component Conventions

1. **Thin Presenters**: Presenters should only coordinate HTTP inputs, flash messages, redirects, and template assignments. Delegate all business logic and data mutations to Facades or Services.
2. **Lifecycle Methods**:
   - `startup()` — Authorization checks and presenter-wide initialization.
   - `action<Action>()` — Logic executed before view rendering (e.g., redirecting, fetching entities for editing).
   - `render<View>()` — Pass data to the template via `$this->template->variable = ...`.
   - `createComponent<Name>()` — Factory methods for nested UI components or forms.
3. **Form Handling**:
   - Always validate CSRF protection on forms.
   - Use `onSuccess[]` callbacks on Nette Forms to process valid submissions.

## 3. Configuration & Dependency Injection (Neon)

- **Neon Structure**:
  ```neon
  services:
      - App\Services\BlogService
  ```
- **Configuration Hierarchy**:
  1. `app/config/config.neon` (Base parameters and global services)
  2. `app/config/{EDITION}/config.neon` (Edition-specific overrides)
  3. `app/config/{EDITION}/env/{APP_ENV}.neon` (Environment-specific credentials)

## 4. Latte Template Conventions

- Always use the `{syntax latte}` and valid n:attributes (`n:if`, `n:foreach`, `n:href`).
- Escape user input automatically (standard in Latte); never use `{`!`$var}` unless explicitly outputting vetted HTML.
- Always lint Latte templates after editing:
  ```bash
  docker compose exec app php tests/latte-linter.php app
  ```
