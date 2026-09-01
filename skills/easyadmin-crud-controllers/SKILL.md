---
name: easyadmin-crud-controllers
description: Building and maintaining admin backends with EasyAdminBundle — Dashboard, CrudController, field configuration, filters, and actions.
---

# EasyAdmin CRUD Controllers Skill

Use this skill when adding or modifying admin CRUD screens with `easycorp/easyadmin-bundle` (`AbstractDashboardController`, `AbstractCrudController`, EasyAdmin fields, filters, and actions).

## 1. Structure

- **Dashboard**: One `DashboardController extends AbstractDashboardController`, registers menu items and links each to its `CrudController::class` — keep menu ordering and grouping in this single place, not scattered across controllers.
- **CRUD Controllers**: One per entity, `SomethingCrudController extends AbstractCrudController`, named `<Entity>CrudController`. Must implement `getEntityFqcn(): string` returning the mapped entity class.
- **Location**: Typically `src/Controller/Admin/*CrudController.php` (or the project's equivalent Admin namespace), separate from public-facing controllers.

## 2. Field Configuration

- Define fields in `configureFields(string $pageName): iterable`, branching on `$pageName` (`Crud::PAGE_INDEX`, `PAGE_NEW`, `PAGE_EDIT`, `PAGE_DETAIL`) when a field's visibility/type should differ per page rather than duplicating whole `configureFields` methods.
- Use the field type that matches the underlying Doctrine type (`TextField`, `AssociationField`, `DateTimeField`, `BooleanField`, `ImageField`/`FileField` for uploads) instead of leaving everything as the default guessed field.
- For `AssociationField`, set `setQueryBuilder()` or `autocomplete()` explicitly once the target table grows past a few hundred rows — the default full-collection dropdown does not scale.
- Keep expensive computed/virtual fields (`TextField::new('computedProp')`) off `PAGE_INDEX` if they trigger N+1 queries; add `setQueryBuilder()` with explicit `addSelect()`/joins on the CRUD controller's `createIndexQueryBuilder()` instead.

## 3. Actions, Filters & Permissions

- Customize actions via `configureActions(Actions $actions)` — add/remove/reorder using `Action::new()`, `Crud::PAGE_INDEX` etc.; guard destructive actions (`Action::DELETE`) with `->setPermission()` tied to a Symfony Security voter/role rather than hiding them client-side only.
- Add list filters via `configureFilters(Filters $filters)` matching indexed/frequently-filtered columns (see `symfony-doctrine-orm` skill on adding the underlying DB index).
- Enforce access at the controller level with `#[IsGranted(...)]` or a `configureCrud()`/security check — EasyAdmin does not implicitly restrict by role beyond what you configure.

## 4. Common Pitfalls

- Don't put business logic (e.g. slug generation, file processing) inline in the CRUD controller's lifecycle hooks (`persistEntity`, `updateEntity`) beyond a thin call into a domain service — keep the same service reachable from non-admin code paths.
- When overriding `persistEntity()`/`updateEntity()`, always call the parent method (or explicitly persist+flush) — forgetting it silently no-ops the save.
- Uploaded files: use `VichUploaderBundle` or an explicit `UploadedField`/event listener pattern consistently across all CRUD controllers that handle file uploads, not a bespoke approach per controller.

## 5. Useful Commands

```bash
# List routes registered by the dashboard/EasyAdmin
php bin/console debug:router | grep admin

# Clear cache after changing field/action configuration (config is compiled)
php bin/console cache:clear
```
