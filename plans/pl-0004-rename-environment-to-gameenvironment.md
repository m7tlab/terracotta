+++
id = "PL-004"
title = "Rename `environment` to `gameEnvironment`"
status = "open"
priority = "medium"
+++

---
description: Rename `environment` to `gameEnvironment` across the public API and documentation.
---

# Rename `environment` to `gameEnvironment`

This plan follows `project/methodology/guides/development.md` for a **refactoring** that
touches `terracotta-core`, `terracotta-gradle-plugin`, `terracotta-provider-modrinth`,
`terracotta-provider-hangar`, and `docs`.

## Source of truth

- TODO item: "Rename `environment` to `gameEnvironment`" (`project/TODO.md`)

## Progress summary

| Phase | Status | Notes |
|-------|--------|-------|
| TDD | Not started | |
| Implementation | Not started | |
| Review | Not started | |
| Documentation | Not started | |
| Push and merge | Not started | |
| Release report | Not started | |

## Phase 4: Test-driven development

- [ ] Read `project/methodology/guides/testing.md`.
- [ ] Identify every public symbol, DSL property, config field, and test that uses `environment`.
- [ ] Write failing tests that assert the renamed `gameEnvironment` contract.
- [ ] Run tests and confirm they fail for the expected reason.

## Phase 5: Implementation

- [ ] Read `project/methodology/guides/implementation.md`.
- [ ] Rename public symbols from `environment` to `gameEnvironment` with the smallest change.
- [ ] Update affected tests, Gradle plugin DSL, provider clients, and loader adapters.
- [ ] Refactor and add or update KDoc.
- [ ] Run `:build :spotlessCheck`.

## Phase 6: Review

- [ ] Read `project/methodology/guides/review.md`.
- [ ] Run code review checklist.
- [ ] By default, confirm auto-review (tests, spotless, build, docs build) passes.
- [ ] Update `CHANGELOG.md` under `[Unreleased]` with a direct summary (e.g., "Renames `environment` to `gameEnvironment`"). Do not add a meta-introduction such as "This release..." or "This unreleased set of changes..."; the release script promotes the `[Unreleased]` body verbatim into the new version section.
- [ ] Optional: escalate to human review only when blocked on taste, direction, or high-stakes decisions.

## Phase 7: Documentation

- [ ] Read `project/methodology/guides/documentation.md`.
- [ ] Update Diátaxis docs that reference `environment`.
- [ ] Cross-link KDoc with `@see` where the renamed symbol is documented.
- [ ] Final verification: `./gradlew build :spotlessCheck` and `mkdocs build --strict`.

## Phase 8: Push and merge

- [ ] Push the branch and open a pull request to `main`.
- [ ] Review the PR; address feedback and re-run checks.
- [ ] Once CI is green, merge the pull request.
- [ ] Observe CD and confirm the deployment succeeds.

## Phase 9: Release report

- [ ] Archive project artifacts used for this work:
  - Move this plan to `project/plans/archived/<plan>.md`.
  - Move any brainstorm note to `project/brainstorm/archived/<note>.md` if one was created.
- [ ] Copy `project/methodology/templates/report.md` to `reports/<datetime>-<title>.md` and fill it out.

## Notes

<!-- Decisions, blockers, and discoveries go here as work progresses. -->
