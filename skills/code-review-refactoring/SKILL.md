---
name: code-review-refactoring
description: Applying SOLID principles, architectural refactoring, legacy code modernization, and database query/performance optimization.
---

# Code Review & Refactoring Skill

Use this skill when conducting code reviews, refactoring complex methods, reducing cyclomatic complexity, or improving database/application performance.

## 1. Clean Code & SOLID Checklist

- **Single Responsibility**: Each class or service should focus on one responsibility. Avoid "God classes" or oversized controllers/presenters.
- **Open / Closed**: Design extension points using interfaces and composition rather than modifying core base classes.
- **Liskov Substitution**: Derived classes or interface implementations must honor their contract without throwing unexpected unsupported exceptions.
- **Interface Segregation**: Keep interfaces small and focused.
- **Dependency Inversion**: High-level modules should depend on abstractions/interfaces rather than concrete low-level implementations.

## 2. Refactoring Tactics

1. **Extract Method / Extract Service**:
   - When a method exceeds ~30 lines or handles multiple tasks (e.g. validating input + calculating prices + sending email), extract dedicated helper services.
2. **Replace Array Bags with Value Objects (DTOs)**:
   - Instead of passing associative arrays with untyped keys (`$data['email']`), introduce explicit typed Data Transfer Objects (DTOs) with immutable properties.
3. **Eliminate Duplication**:
   - Extract common business logic into shared Facades/Domain Services. Avoid copy-pasting logic across multiple controllers or presenters.

## 3. Performance & Query Optimization

- **N+1 Query Problem**:
  - Always check ORM queries in loops. Use DQL/QueryBuilder joins (`LEFT JOIN FETCH`) or batch-loading repository methods instead of lazy-loading associations in template loops.
- **Indexing**:
  - Ensure columns used in `WHERE`, `JOIN`, and `ORDER BY` clauses are properly indexed.
- **Caching**:
  - Cache heavy computations or static catalog trees using the project's cache layer (Redis/Valkey, Symfony Cache, Nette Cache, etc.) rather than recomputing on every request.
