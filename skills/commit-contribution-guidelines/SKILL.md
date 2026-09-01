---
name: commit-contribution-guidelines
description: Enforcing strict commit message formatting, git workflow conventions, and project contribution standards.
---

# Commit & Contribution Guidelines Skill

Use this skill when composing git commit messages, creating branches, or preparing pull requests and project contributions.

## Commit Message Rules

- Avoid overly verbose descriptions or redundant text.
- Limit the subject line to **50 characters**.
- Capitalize the first letter of the subject line.
- Do **not** end the subject line with a period.
- The subject line must be in **past tense** (e.g. `Fixed ...`, `Added ...`, `Updated ...`, `Refactored ...`, `Removed ...`).
- Separate the subject line from the body with a **blank line**.
- Use the body to explain **what and why** was changed rather than how the change was implemented.
- Format the body using clean **bullet points**.
- Wrap body lines at ~72 characters where practical.

## Commit Message Format

```text
<Past-tense summary in 50 characters or less>

- <Bullet point explaining what was changed and why>
- <Bullet point detailing secondary changes or edge-case handling>
- <Bullet point noting any deprecations or migrations if applicable>
```

## Example Commit Messages

### Feature Addition:
```text
Added Mergado XML feed export for products

- Implemented MergadoFeedGeneratorService under SyncModule
- Configured cron CLI command for periodic background feed generation
- Added test assertion for valid XML output structure
```

### Bug Fix:
```text
Fixed product filter pagination offset calculation

- Corrected limit/offset handling in ProductFacade when filtering by category
- Added regression test to verify page count calculations
```

### Refactoring:
```text
Refactored checkout cart calculation logic

- Extracted cart total calculations from controller into CartCalculationService
- Replaced redundant database queries with eager-loaded entity associations
```
