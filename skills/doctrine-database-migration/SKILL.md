---
name: doctrine-database-migration
description: Managing ORM entities, database schemas, and data migrations using Nettrine/Doctrine ORM and migrations via Docker commands.
---

# Doctrine & Database Migration Skill

Use this skill when defining or modifying database entities, running schema synchronizations, creating versioned database migrations, or executing test fixtures.

## Key Principles & Conventions

1. **ORM Implementation**: Nettrine ORM (`nettrine/orm`, `nettrine/migrations`) or Doctrine ORM.
2. **Entity Location**: Store all ORM entities in `App\Models\Entity\*` (or module entity namespaces).
3. **Execution Context**: Always execute database operations and migration commands inside the Docker container (`docker compose exec app ...`).
4. **Schema Migrations vs Updates**:
   - For production-ready workflows, always use versioned migration files rather than direct schema updates.
   - Test migrations both forwards (`migrate`) and backwards where rollbacks are supported.

## Database Management Commands

### 1. Schema Validation & Diff
```bash
# Validate mapping and database synchronization
docker compose exec app php www/index.php orm:validate-schema
```

### 2. Updating Schema Directly (Development Only)
```bash
# Update schema directly in development database
docker compose exec app php www/index.php orm:schema-tool:update --force
# (Note: May require interactive confirmation)
```

### 3. Creating & Running Versioned Migrations
```bash
# Generate a new migration class based on entity differences
docker compose exec app php www/index.php migrations:diff

# List all migrations and their execution status
docker compose exec app php www/index.php migrations:status

# Apply all pending migrations
docker compose exec app php www/index.php migrations:migrate
```

### 4. Entity Generation & Fixtures
```bash
# Generate entities from mapping definitions
docker compose exec app php www/index.php orm:generate-entities

# Load test data fixtures
docker compose exec app php www/index.php fixtures:load
```

## Best Practices for Entity Design

- Use explicit typed properties.
- Ensure proper index definitions on foreign keys and frequently filtered columns.
- Always configure cascade operations carefully (`cascade={"persist"}` vs `cascade={"all"}`) to prevent accidental data deletion or memory leaks.
- Avoid bi-directional relationships unless necessary; prefer unidirectional associations to reduce complexity.
