---
name: web-security-hardening
description: Implementing web application security defenses, preventing OWASP Top 10 vulnerabilities, managing authentication, input sanitization, and security headers — framework-agnostic principles.
---

# Web Security Hardening Skill

Use this skill when developing authentication flows, handling sensitive data, sanitizing user inputs, or securing web APIs and frontend views. For the concrete mechanism a specific framework provides (CSRF token API, template auto-escaping, authorization attributes/voters), see that framework's engineering skill (`nette-php-engineering`, `symfony-php-engineering`) — the rules below are what to enforce, not how any one framework's API spells it.

## 1. Input Validation & Injection Prevention

- **SQL Injection**:
  - Always use prepared statements and parameter binding via the ORM's query builder or DQL/QueryBuilder equivalent.
  - Never concatenate raw user input into SQL/DQL strings.
  ```php
  // Good:
  $qb->where('p.id = :id')->setParameter('id', $productId);
  // Bad:
  $qb->where("p.id = $productId");
  ```
- **XSS (Cross-Site Scripting)**:
  - Trust the template engine's automatic contextual escaping (Latte, Twig, Blade all escape by default).
  - Avoid raw/unescaped output filters (Latte `|noescape`, Twig `|raw`) unless the data has been sanitized through a dedicated HTML purifier first.

## 2. CSRF & Form Security

- Ensure CSRF tokens are verified on all state-changing HTTP requests (`POST`, `PUT`, `DELETE`, `PATCH`).
- Use the framework's built-in CSRF protection rather than rolling a custom token scheme — every mainstream PHP framework ships one (form-level protection, a CSRF token manager service, etc.); check the relevant framework skill for the exact API.

## 3. Authentication & Authorization

- Use strong password hashing (`PASSWORD_BCRYPT` / `PASSWORD_ARGON2ID`), applied via the framework's password hasher service rather than calling `password_hash()` ad hoc.
- Implement role-based access control checks before performing any privileged operation, enforced at the framework's access-control layer (route/controller-level guards, an authorization voter, or an explicit permission check in the request-handling layer) — not only hidden in the UI.
- Guard against Insecure Direct Object References (IDOR): Always verify that the authenticated user owns or has rights to modify the requested entity ID, not just that they are authenticated.

## 4. HTTP Security Headers

Ensure appropriate security headers are emitted via `.htaccess`, Nginx config, or framework response headers/middleware:
- `X-Frame-Options: SAMEORIGIN`
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Content-Security-Policy` configured with valid sources.
