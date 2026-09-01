---
name: symfony-php-engineering
description: Specialized guidance for developing, reviewing, and maintaining applications built on Symfony (5/6/7/8), covering Controllers, Services, DI autowiring, routing attributes, Twig templates, and YAML/PHP configuration.
---

# Symfony Framework & PHP Engineering Skill

Use this skill when writing, reviewing, or refactoring PHP code, Controllers, Services, Event Listeners/Subscribers, Forms, Twig templates, and Symfony configuration files.

## 1. Core Architecture & Conventions

- **Framework**: Symfony (`symfony/framework-bundle`), commonly pinned to a minor version constraint (e.g. `8.1.*`) — keep new code compatible with that pinned version, don't reach for APIs from a newer minor.
- **Directory Structure**:
  - `src/Controller/` (or project-specific subfolders, e.g. `src/Controllers/Admin/`) — HTTP entry points.
  - `src/Entity/` — Doctrine ORM entities.
  - `src/Repository/` — Doctrine repositories (one per entity, extends `ServiceEntityRepository`).
  - `src/Services/` (or `src/Service/`) — Business logic, framework-agnostic where possible.
  - `src/EventListener/` / `src/EventSubscriber/` — Kernel and domain event hooks.
  - `src/Form/` — `AbstractType` form classes.
  - `config/packages/*.yaml` — Bundle configuration, one file per bundle.
  - `config/routes.yaml` — Route imports (prefer PHP 8 attributes on controllers over YAML route definitions in new code).
  - `templates/` — Twig views.
  - `public/` — Web root; only `index.php` and compiled/mapped assets belong here.

## 2. Controller Conventions

1. **Thin Controllers**: Controllers should only extract request input, call a service/repository, and return a `Response`. Delegate business logic and multi-step mutations to services.
2. **Routing**: Prefer PHP attributes over YAML/XML for new routes:
   ```php
   #[Route('/album/{id}', name: 'album_show', methods: ['GET'])]
   public function show(Album $album): Response
   {
       return $this->render('album/show.html.twig', ['album' => $album]);
   }
   ```
3. **Parameter Conversion**: Use implicit param conversion (typed entity arguments resolved by Doctrine from route params) instead of manual `find()` calls where the entity manager already resolves it.
4. **Responses**: Return the correct `Response` subtype (`JsonResponse`, `RedirectResponse`, `BinaryFileResponse`) rather than manually setting headers on a generic `Response`.
5. **Validation**: Validate input via `symfony/validator` constraints on DTOs/entities or `symfony/form`, not manual `if` chains.

## 3. Services & Dependency Injection

- **Autowiring/Autoconfiguration**: Rely on `services.yaml` defaults (`autowire: true`, `autoconfigure: true`) — avoid manually wiring services unless a specific argument (e.g. a scalar, bound parameter, or interface with multiple implementations) requires it.
- **Constructor Injection Only**: Never use `Container::get()` / service locators in application code; inject collaborators via the constructor.
- **Interfaces over concretions**: Type-hint against an interface when a service may have alternate implementations (e.g. tagged services, strategy pattern).
- **Config precedence**: `config/services.yaml` for defaults and explicit bindings; `config/packages/*.yaml` for bundle-specific configuration; `config/packages/{env}/*.yaml` for environment overrides (`dev`, `test`, `prod`).

## 4. EventListener / EventSubscriber

- Prefer `#[AsEventListener]` attribute on a plain service method for a single event, or implement `EventSubscriberInterface` when a class handles several related events.
- Keep listener logic thin — delegate to a service if the handling requires non-trivial logic, to keep listeners testable in isolation.

## 5. Twig Template Conventions

- Auto-escaping is on by default — never use `|raw` on user-supplied content.
- Use `{% block %}` inheritance from a shared base layout; avoid duplicating `<head>`/nav markup across templates.
- Push presentation-only formatting into Twig filters/extensions (`src/Twig/`) rather than pre-formatting strings in the controller.
- Use `path()`/`url()` for all internal links — never hardcode route paths.

## 6. Security & CSRF (`symfony/security-bundle`)

- **Authorization**: Guard controllers/actions with `#[IsGranted('ROLE_...')]` or `#[IsGranted('EDIT', subject: 'album')]` for object-level checks; for non-trivial ownership/permission logic, implement a `Voter` (`voteOnAttribute()`) rather than inlining conditionals in the controller.
- **CSRF**: Forms built with `symfony/form` get CSRF protection automatically; for a non-form state-changing action (e.g. a plain POST button), generate/validate a token explicitly via `CsrfTokenManagerInterface`.
- **Password hashing**: Use the `PasswordHasherInterface` autowired for the `User` class (configured in `config/packages/security.yaml`) — never call `password_hash()` directly.
- **Access control**: Coarse-grained URL restrictions belong in `security.yaml`'s `access_control` section; fine-grained per-entity checks belong in a Voter, not scattered `if` statements.

## 7. Frontend Assets

Check `composer.json` before touching any JS/CSS: if it has `symfony/asset-mapper` (and, for Sass, `symfonycasts/sass-bundle`), use the `symfony-asset-mapper` skill — there is no Node/npm build step, everything is driven by `bin/console`. Only reach for the generic `frontend-assets-pipeline` skill's Node/Vite/Webpack workflow if the project also has a `package.json` build script (e.g. Encore, or a standalone SPA build) alongside or instead of AssetMapper.

## 8. Console & CLI

```bash
# List available commands
php bin/console list

# Clear cache (run after config changes)
php bin/console cache:clear

# Debug autowired services / routes
php bin/console debug:autowiring
php bin/console debug:router
php bin/console debug:container <service-id>

# Generate boilerplate (controller, entity, form, CRUD, subscriber)
php bin/console make:controller
php bin/console make:entity
```

If the project runs in Docker, prefix these with the project's `docker compose exec <service>` (see `docker-environment-operations` skill); otherwise run directly.
