# Agentic Kit for Web Projects

A standardized, plug-and-play collection of **AI Agent Skills**, **Agent Guidelines (`agents.md`)**, and **Development Playbooks** designed for web applications.

This directory serves as a central blueprint that can be adapted and dropped into any new or existing web project to empower AI coding assistants (such as Junie, Antigravity, Claude Code, Cursor, Windsurf, Copilot, etc.).

---

## 📁 Directory Structure

```text
agentic/
├── README.md                           # Overview and adoption guide
├── agents.md                           # Master template for project-level AI guidelines
├── playbooks/
│   └── web-development-playbook.md     # Standardized agent workflow & quality checklist
└── skills/                             # Modular Agent Skills (YAML frontmatter + Markdown)
    ├── commit-contribution-guidelines/ # Git commit conventions & PR guidelines
    │   └── SKILL.md
    ├── docker-environment-operations/  # Container lifecycle, Composer, NPM, CLI tools
    │   └── SKILL.md
    ├── doctrine-database-migration/   # Schema management, entities, migrations & fixtures (Nette/Nettrine)
    │   └── SKILL.md
    ├── nette-php-engineering/          # Nette Framework (3.x/4.x), Latte, Neon & architecture
    │   └── SKILL.md
    ├── symfony-php-engineering/        # Symfony Controllers, DI, routing attributes, Twig & config
    │   └── SKILL.md
    ├── symfony-doctrine-orm/           # Doctrine ORM & migrations via bin/console (Symfony)
    │   └── SKILL.md
    ├── symfony-testing-quality/        # PHPUnit (Web/Kernel TestCase), test env, PHPStan for Symfony
    │   └── SKILL.md
    ├── symfony-asset-mapper/           # AssetMapper (import maps) + SassBundle, no Node build step
    │   └── SKILL.md
    ├── easyadmin-crud-controllers/     # EasyAdminBundle Dashboard, CrudController, fields & actions
    │   └── SKILL.md
    ├── testing-static-analysis/        # PHPStan, Nette Tester/PHPUnit, Latte linting
    │   └── SKILL.md
    ├── web-security-hardening/         # OWASP best practices, sanitization, auth & CSP
    │   └── SKILL.md
    ├── frontend-assets-pipeline/       # Gulp/Vite/Webpack, SCSS, JS/TS, caching
    │   └── SKILL.md
    ├── api-design-integration/         # REST API design, payload validation, Bruno/Postman
    │   └── SKILL.md
    └── code-review-refactoring/        # SOLID patterns, refactoring & performance tuning
        └── SKILL.md
```

---

## 🚀 How to Use in a New or Existing Project

### Step 1: Copy Guidelines & Skills
Copy the relevant files into your target project:

```bash
# agents.md always goes at the project root — Claude Code, Junie, and Antigravity
# all read it (see agents.md → "Loading These Skills Per AI Tool" for the per-tool details)
cp agentic/agents.md /path/to/project/agents.md
```

Then load the skill files themselves the way your tool expects — see `agents.md`'s **Loading These Skills Per AI Tool** section for exact steps per tool (Claude Code: `.claude/skills/`; Junie: on-demand reading from `agentic/skills/`; Antigravity: `.agents/skills/` or `.gemini/skills/`).

### Step 2: Customize `agents.md`
Open the copied `agents.md` and replace the placeholder sections (`{{PROJECT_NAME}}`, `{{FRAMEWORK}}`, `{{DOCKER_SERVICE}}`, etc.) with your project's specific details.

### Step 3: Select & Tune Relevant Skills
Enable only the skills your project needs. For example:
- If using Nette + Doctrine: keep `nette-php-engineering`, `doctrine-database-migration`, `testing-static-analysis`, etc.
- If using Symfony: keep `symfony-php-engineering`, `symfony-doctrine-orm`, `testing-static-analysis` + `symfony-testing-quality`; add `easyadmin-crud-controllers` if the project uses EasyAdminBundle. For frontend assets, use `symfony-asset-mapper` (drop `frontend-assets-pipeline`) if the project uses `symfony/asset-mapper` — most new Symfony projects do — otherwise keep `frontend-assets-pipeline` for an Encore/Vite/Webpack setup. Drop the Nette-specific skills (`nette-php-engineering`, `doctrine-database-migration`).
- If using Laravel: adjust `doctrine-database-migration` or framework-specific engineering skills accordingly.

---

## 🛠 Included Skills Catalog

| Skill Name                           | Description                                                              | Key Focus Areas                                                                        |
|:-------------------------------------|:-------------------------------------------------------------------------|:---------------------------------------------------------------------------------------|
| **`commit-contribution-guidelines`** | Enforces clean, conventional commit messages and contribution standards. | Conventional commits, 50-char subject, bulleted body, changelog clarity.               |
| **`docker-environment-operations`**  | Commands and procedures for containerized web development.               | `docker compose exec`, composer, npm/yarn, console/CLI entrypoints.                    |
| **`doctrine-database-migration`**    | Entity modeling, migration versioning, schema updates, fixtures.         | Nettrine/Doctrine ORM, schema diffs, migration safety, indexes.                        |
| **`nette-php-engineering`**          | Best practices for Nette Framework applications.                         | Presenters, components, Latte templating, Neon configs, DI container, facades.         |
| **`testing-static-analysis`**        | Test runners and static analyzers for code quality.                      | PHPStan (baseline & levels), Tester / PHPUnit, template syntax linters.                |
| **`web-security-hardening`**         | Security guidelines and defensive programming.                           | CSRF tokens, XSS protection, SQL injection prevention, input sanitization, headers.    |
| **`frontend-assets-pipeline`**       | Asset compilation, bundling, and client-side optimization.               | Gulp, Vite, Webpack, SCSS compilation, cache busting, image compression.               |
| **`api-design-integration`**         | REST APIs, external service integrations, and API testing.               | Endpoint design, JSON payloads, authentication tokens, Bruno/Postman collections.      |
| **`code-review-refactoring`**        | Clean code principles, architectural refactoring, performance.           | SOLID, domain layer separation, N+1 query elimination, caching strategies.             |
| **`symfony-php-engineering`**        | Best practices for Symfony (5–8) applications.                           | Controllers, DI autowiring, routing attributes, Event Listeners, Twig, YAML config.    |
| **`symfony-doctrine-orm`**           | Entity modeling, migrations & fixtures via Symfony's `bin/console`.      | `make:entity`, `doctrine:migrations:*`, schema validation, cascade/index discipline.   |
| **`symfony-testing-quality`**        | PHPUnit test suites and static analysis tuned for Symfony.               | Web/Kernel TestCase, test env config, `phpstan-symfony`/`phpstan-doctrine` extensions. |
| **`symfony-asset-mapper`**           | JS & Sass assets via AssetMapper/SassBundle — no Node build step.        | `importmap:*`, Stimulus controllers, `sass:build`, `asset-map:compile`.                |
| **`easyadmin-crud-controllers`**     | Admin backends built with EasyAdminBundle.                               | Dashboard/CrudController structure, field config, filters, actions, permissions.       |

---

## 💡 Skill Format Standard

All skills follow the standard AI Agent skill specification:
```markdown
---
name: skill-identifier
description: Clear, concise description of the skill scope and when the agent should trigger it.
---

# Skill Title
... actionable guidelines, code snippets, and terminal commands ...
```
