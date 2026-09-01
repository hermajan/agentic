# Project Guidelines & Agent Directives

## Architecture & Code Principles

1. **Layered Separation of Concerns**:
   - **Presenters / Controllers**: Thin controllers responsible only for HTTP request handling, input extraction, view model preparation, and delegation to facades/services.
   - **Facades & Services**: Business logic encapsulated in explicit domain services and facades. Keep presenters free of complex SQL queries or raw domain mutations.
   - **Entities & Repositories**: Doctrine entities represent domain state with explicit typed properties and getter/setter encapsulation. Repositories handle database queries.
2. **Dependency Injection**:
   - Always inject dependencies via constructor injection.
   - Avoid service locators or global container calls in application code.
3. **Defensive Coding & Safety**:
   - Strictly validate and sanitize user inputs before processing.
   - Guard against N+1 query performance regressions in loops.
   - Never commit sensitive secrets, credentials, or API keys directly to version control.

---

## Agent Skills Catalog

For detailed operational instructions, architectural guidelines, and specialized workflows, refer to the following Agent Skills located in `skills/`. Enable only the ones relevant to this project's stack (see `README.md` → *Select & Tune Relevant Skills*):

- **`docker-environment-operations`**: Container execution, Composer, NPM, and debugging in Docker.
- **`testing-static-analysis`**: PHPStan configuration & baselines, PHPUnit/Tester suites, and template-linting principles (framework-agnostic core).
- **`commit-contribution-guidelines`**: Commit message formatting rules (50-char subject, bulleted body, past-tense summary).
- **`web-security-hardening`**: CSRF tokens, XSS protection, SQL injection prevention, safe headers, and role-based permissions (framework-agnostic core).
- **`frontend-assets-pipeline`**: SCSS compilation, TypeScript/JS bundling via a Node toolchain (Vite/Webpack/Gulp), asset versioning.
- **`api-design-integration`**: REST API conventions, JSON response formatting, authentication tokens, Bruno/Postman collections.
- **`code-review-refactoring`**: Refactoring strategies, SOLID principles, performance tuning, and database index optimization.
- **`nette-php-engineering`** *(Nette projects)*: Presenter lifecycles, Latte templates, and Neon configuration.
- **`doctrine-database-migration`** *(Nette projects)*: Nettrine/Doctrine entities, migrations, schema synchronization, and fixtures.
- **`symfony-php-engineering`** *(Symfony projects)*: Controllers, DI autowiring, routing attributes, Event Listeners, Twig, and YAML config.
- **`symfony-doctrine-orm`** *(Symfony projects)*: Doctrine entities, migrations, and fixtures via `bin/console`.
- **`symfony-testing-quality`** *(Symfony projects)*: PHPUnit (Web/Kernel TestCase), the `test` environment, and Symfony-aware PHPStan.
- **`symfony-asset-mapper`** *(Symfony projects using AssetMapper)*: JS via import maps + Stimulus, Sass via SymfonyCasts SassBundle — no Node build step.
- **`easyadmin-crud-controllers`** *(Symfony projects using EasyAdminBundle)*: Dashboard/CrudController structure, fields, filters, actions, permissions.

---

## Loading These Skills Per AI Tool

This kit ships skills as one `SKILL.md` per folder under `skills/`. Each tool discovers/loads that content differently — set up the one(s) your team actually uses:

### Claude Code
Claude Code natively discovers modular skills. Copy (or symlink) the skills you selected into the project's skill directory, one subfolder per skill, keeping each `SKILL.md`'s YAML frontmatter (`name`, `description`) intact — Claude uses the `description` to decide when to load a skill automatically, and you can also invoke one explicitly:
```bash
mkdir -p .claude/skills
cp -r agentic/skills/<skill-name> .claude/skills/
```
This `agents.md` file itself is also useful as project-level context; keep it at the repo root.

### JetBrains Junie
Junie reads project instructions directly from `agents.md` at the project root (configured via `.idea/junie.xml` with `"guidelinesPath": "agents.md"`). As an AI agent, Junie loads modular skills on demand: whenever a task or issue requires expertise in a specific domain, Junie must open and read the corresponding skill file under `agentic/skills/<skill-name>/SKILL.md` (for example, `agentic/skills/symfony-php-engineering/SKILL.md`, `agentic/skills/symfony-doctrine-orm/SKILL.md`, `agentic/skills/easyadmin-crud-controllers/SKILL.md`, `agentic/skills/docker-environment-operations/SKILL.md`, etc.) to obtain detailed operational instructions, architectural guidelines, and terminal commands.

### Google Antigravity
Antigravity follows the open `AGENTS.md` convention and natively discovers modular skills:
1. **Project Instructions**: An `agents.md` (or `AGENTS.md`) file at the repository root is automatically loaded as project-level directives. Nested `AGENTS.md` files (e.g., `src/Admin/AGENTS.md`) are also respected for localized context.
2. **Modular Skills**: Copy or symlink selected skills into `.agents/skills/` (or `.gemini/skills/`), keeping each `SKILL.md`'s YAML frontmatter (`name`, `description`) intact:
   ```bash
   mkdir -p .agents/skills
   cp -r agentic/skills/<skill-name> .agents/skills/
   ```
   Antigravity automatically indexes and loads these skills based on their descriptions, or you can reference them on demand.
