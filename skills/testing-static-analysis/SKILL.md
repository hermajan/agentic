---
name: testing-static-analysis
description: Executing PHPStan static analysis, PHP test suites, template linters, and full quality verification suites — framework-agnostic core commands and principles.
---

# Testing & Static Analysis Skill

Use this skill when running tests, executing static code analysis, adding regression test cases, or verifying complete code quality. This skill covers the framework-agnostic core; for template-linter and test-runner specifics of a particular framework, see its dedicated skill (`nette-php-engineering` for Nette Tester/Latte linting, a Symfony testing skill for PHPUnit/`bin/console` specifics) if one exists in this kit.

## 1. Static Analysis with PHPStan

- **Configuration**: Located at the project root (`phpstan.neon` / `phpstan.neon.dist`) or under a config directory, depending on the framework's conventions.
- **Baseline File**: `phpstan-baseline.neon` (used to ignore legacy technical debt).
- **Running PHPStan**:
  ```bash
  composer run phpstan
  # or:
  vendor/bin/phpstan analyse
  ```
- **Updating the Baseline**:
  ```bash
  vendor/bin/phpstan analyse --generate-baseline
  ```
  *(Rule: Never generate baseline entries to hide newly introduced bugs; fix issues at the root).*

## 2. Test Execution

Identify the test runner actually configured in the project (`composer.json` `scripts`, `phpunit.xml*`, or a framework-specific tester config) before assuming a command — don't guess.

- **PHPUnit** (most common across frameworks):
  ```bash
  vendor/bin/phpunit
  # single file:
  vendor/bin/phpunit tests/path/to/SampleTest.php
  ```
- **Nette Tester**: see `nette-php-engineering`.
- Any project-specific test command declared in `composer.json` (`composer run test`, `composer run tester`, etc.) takes precedence over the raw binary invocation above.

## 3. Template & Markup Linting

Lint whichever template engine the project uses after editing templates (Latte, Twig, Blade, etc.) — check for a project-provided linter script or a framework console command (e.g. `bin/console lint:twig`) rather than assuming one exists.

## 4. Code Standards & Fixers

- **PHP-CS-Fixer Check**:
  ```bash
  vendor/bin/php-cs-fixer fix --dry-run --diff
  ```
- **PHP-CS-Fixer Apply**:
  ```bash
  vendor/bin/php-cs-fixer fix
  ```
- **PHP Syntax Check (Parallel Lint)**:
  ```bash
  vendor/bin/parallel-lint src tests
  ```

## 5. Full Verification Pipeline

Some projects expose a single aggregate `composer run` script that runs static analysis, tests, and linting together — prefer running that over invoking each tool separately, so the check matches what CI runs. Check `composer.json`'s `scripts` section (e.g. `composer run check`) before falling back to running the tools individually as above.

If the project runs in Docker, prefix any of the above commands with `docker compose exec <service>` (see `docker-environment-operations`).
