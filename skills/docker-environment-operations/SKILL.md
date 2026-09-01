---
name: docker-environment-operations
description: Interacting with containerized services (app, database, cache) using Docker Compose, managing Composer/NPM dependencies, and executing CLI workflows.
---

# Docker & Environment Operations Skill

Use this skill when running container commands, installing/updating dependencies, building assets, or executing project workflows inside containerized environments.

## Environment Architecture

- Primary Web/App service: `app`
- Database service: `mariadb` / `mysql` / `postgres`
- Caching / In-memory service: `valkey` / `redis`
- Typical working directory inside container: `/srv/` (mapped to host project root)
- Operating system in container: Linux (Debian / Alpine)

## Essential Container Commands

### 1. Interactive Shell Access
```bash
docker compose exec app bash
# or (if bash is unavailable in Alpine):
docker compose exec app sh
```

### 2. Cache Clearing & Maintenance
```bash
# Framework console command, e.g.:
docker compose exec app php bin/console cache:clear

# Or manual temp/cache removal
docker compose exec app rm -rf temp/cache log/*
```

### 3. Composer Operations
```bash
# Install dependencies
docker compose exec app composer install

# Update dependencies
docker compose exec app composer update

# Require a new package
docker compose exec app composer require vendor/package

# Dump optimized autoload
docker compose exec app composer dump-autoload -o
```

### 4. NPM / Node Operations
```bash
# Install packages
docker compose exec app npm install

# Build assets for production
docker compose exec app npm run build

# Run asset dev watcher
docker compose exec app npm run dev

# Run arbitrary npx tools
docker compose exec app npx <tool-name> <args>
```

### 5. Console & CLI Commands

Use the entrypoint the project's framework actually provides — check for `bin/console`, `www/index.php`, or `artisan` before guessing:
```bash
# Symfony
docker compose exec app php bin/console <command>

# Nette (front controller doubles as CLI entrypoint)
docker compose exec app php www/index.php <command> <args>

# Laravel
docker compose exec app php artisan <command>
```

### 6. Framework & ORM Commands

Run these directly rather than through a wrapper script — see `doctrine-database-migration`/`symfony-doctrine-orm` for the full set of ORM/migration commands and `testing-static-analysis`/`symfony-testing-quality` for test/analysis commands, for example:
- `docker compose exec app php bin/console doctrine:migrations:migrate` — Execute pending database migrations
- `docker compose exec app php bin/console doctrine:migrations:status` — List status of all migrations
- `docker compose exec app php bin/console doctrine:schema:validate` — Validate ORM mapping and schema
- `docker compose exec app vendor/bin/phpstan analyse && docker compose exec app vendor/bin/phpunit` — Run full test suite and static analysis
