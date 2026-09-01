---
name: symfony-testing-quality
description: Running PHPUnit test suites (unit, functional/WebTestCase, KernelTestCase), the test environment, and Symfony-specific static analysis setup.
---

# Symfony Testing & Quality Skill

Use this skill when writing or running tests in a Symfony project, or configuring PHPStan for Symfony's service container and Doctrine metadata.

## 1. Test Types & Base Classes

- **Unit tests**: Plain PHPUnit `TestCase` for framework-agnostic classes (services with no container dependency, value objects, pure logic).
- **Kernel tests**: `KernelTestCase` — boots the DI container without an HTTP layer; use `self::getContainer()->get(...)` to fetch services under test.
- **Functional/HTTP tests**: `WebTestCase` — boots a test client (`static::createClient()`) that makes real requests through the kernel; use for controller/route-level assertions (`$client->request('GET', '/album/1')`, `$this->assertResponseIsSuccessful()`).
- Keep unit tests as the majority; reserve `WebTestCase` for behavior that genuinely depends on routing/middleware/HTTP semantics.

## 2. Test Environment & Configuration

- Config overrides for the `test` environment live in `config/packages/test/*.yaml` and `.env.test` (or `.env.test.local` for machine-local overrides, never committed).
- The test database should be separate from dev (`DATABASE_URL` override in `.env.test`) — never run tests against the dev/prod database.
- Reset schema/fixtures between runs with `doctrine/doctrine-fixtures-bundle` + `DAMA\DoctrineTestBundle` (wraps each test in a rolled-back transaction) if configured, rather than truncating tables manually.

## 3. Running Tests

```bash
# Run the full suite
vendor/bin/phpunit
# or, if configured via composer:
composer run test

# Run a single test class / file
vendor/bin/phpunit tests/Controller/AlbumControllerTest.php

# Run tests matching a name filter
vendor/bin/phpunit --filter testShowReturns404ForMissingAlbum

# Clear/warm the test-env cache if config changes aren't picked up
php bin/console cache:clear --env=test
```

## 4. Static Analysis

- Use `phpstan/phpstan-symfony` and `phpstan/phpstan-doctrine` extensions so PHPStan understands autowired services, the container, and entity metadata instead of flagging false positives.
- Run the same way as the generic `testing-static-analysis` skill describes (`vendor/bin/phpstan analyse`), just with these extensions enabled in `phpstan.neon`.

## 5. Fixtures for Tests

```bash
# Load fixtures into the test database (typically wrapped by a test bootstrap/helper, not run ad hoc against a shared DB)
php bin/console doctrine:fixtures:load --env=test --no-interaction
```
