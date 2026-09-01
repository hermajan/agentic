---
name: symfony-doctrine-orm
description: Managing Doctrine ORM entities, database schema, versioned migrations, and fixtures in a Symfony application via bin/console and DoctrineMigrationsBundle.
---

# Symfony Doctrine ORM & Migrations Skill

Use this skill when defining or modifying Doctrine entities, generating/running database migrations, or loading fixtures in a Symfony project (`doctrine/orm`, `doctrine/doctrine-bundle`, `doctrine/doctrine-migrations-bundle`).

## Key Principles & Conventions

1. **Entity Location**: `src/Entity/*`, one class per table, mapped via PHP 8 attributes (`#[ORM\Entity]`, `#[ORM\Column]`) — avoid annotations or XML/YAML mapping in new code.
2. **Repository Location**: `src/Repository/*Repository.php`, extending `ServiceEntityRepository`, injected wherever entity queries are needed (never query via `EntityManager::createQuery()` directly from a controller).
3. **Never hand-edit generated migrations** after they've been applied anywhere outside local dev — write a new migration instead.
4. **Schema changes always go through versioned migrations** in real environments; `doctrine:schema:update` is for local scratch iteration only, never for staging/production.

## Entity & Migration Workflow

```bash
# Generate/update a Doctrine entity interactively
php bin/console make:entity

# Validate mapping metadata + compare mapping vs actual DB schema
php bin/console doctrine:schema:validate

# (Dev only) Force-sync schema directly from entities, skipping migrations
php bin/console doctrine:schema:update --force
# Preview the SQL first without applying it:
php bin/console doctrine:schema:update --dump-sql

# Generate a migration from the diff between mapping and current DB schema
php bin/console make:migration
# or:
php bin/console doctrine:migrations:diff

# Inspect pending/applied migrations
php bin/console doctrine:migrations:status
php bin/console doctrine:migrations:list

# Apply all pending migrations
php bin/console doctrine:migrations:migrate

# Roll back the most recent migration
php bin/console doctrine:migrations:migrate prev
```

## Fixtures (if `doctrine/doctrine-fixtures-bundle` is installed)

```bash
# Load fixtures (purges the database first)
php bin/console doctrine:fixtures:load

# Append without purging
php bin/console doctrine:fixtures:load --append
```

## Best Practices for Entity Design

- Use explicit typed properties and PHP property types matching the Doctrine column type (`?int`, `string`, `\DateTimeImmutable`).
- Prefer `\DateTimeImmutable` over `\DateTime` for date/time columns.
- Add explicit indexes (`#[ORM\Index]`) on foreign keys and any column used in frequent `WHERE`/`ORDER BY` clauses.
- Set `cascade` and `orphanRemoval` deliberately per relation — never default to `cascade={"all"}` without checking the deletion/detach implications.
- Prefer unidirectional associations; only add the inverse side of a relation when the owning side's collection is genuinely needed for querying/iteration.
- Every migration must be reviewed for destructive operations (`DROP COLUMN`, `DROP TABLE`, type-narrowing `ALTER COLUMN`) before being applied to a shared environment — add a data-backfill step first when narrowing or renaming.
