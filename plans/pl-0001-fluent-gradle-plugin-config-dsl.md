+++
id = "PL-001"
title = "Fluent Gradle Plugin Config DSL"
status = "open"
priority = "medium"
+++

---
description: Add a fluent, method-style DSL for the Terracotta Gradle plugin configuration system, keeping the existing Property<T> API intact.
---

# Fluent Gradle Plugin Config DSL

This plan follows `project/methodology/module-development-workflow.md` for a **major user-facing API improvement** that
touches `terracotta-gradle-plugin`.

## Source of truth

- TODO item: `Add Fluent Gradle Plugin Config DSL` (`project/TODO.md`)
- Design proposal: `project/designs/26-07-12-fluent-gradle-plugin-config-dsl.md` (to be written during the System design phase)

## Progress summary

| Phase | Status | Notes |
|-------|--------|-------|
| Brainstorm | Not started | Optional |
| System design | Not started | |
| Contract | Not started | |
| TDD | Not started | |
| Implementation | Not started | |
| Review | Not started | |
| Documentation | Not started | |
| Push and merge | Not started | |
| Release report | Not started | |

## Phase 1: Brainstorm

- [ ] Open a fresh `project/brainstorm/<datetime>-fluent-gradle-plugin-config-dsl.md` file.
- [ ] Explore DSL styles: method setters, infix functions, nested builders, type-safe accessors.
- [ ] Decide how provider names are resolved (`modrinth { }` vs `create("modrinth") { }`).
- [ ] Decide how platform-specific blocks (e.g. `relations { required("...") }`) are modeled.
- [ ] Capture trade-offs and open questions in the brainstorm file.

## Phase 2: System design

- [ ] Read `project/methodology/module-system-design-workflow.md`.
- [ ] Write the design proposal.
- [ ] Define the fluent DSL surface:
  - Root-level canonical fields: `loaders(FABRIC, PAPER)`, `gameVersions("1.20.1", ...)`.
  - Provider blocks: `modrinth { projectId("...") }` or `create("modrinth") { ... }` with extra convenience.
  - Overrides block: `overrides { loaders(FABRIC); changelog("...") }`.
  - Platform-specific blocks: `relations { required("fabric-api") }`, `featured(true)`, `channel("Snapshot")`, etc.
- [ ] Decide on backward compatibility with the existing `Property<T>` API.
- [ ] Complete design review checklist from `module-review-workflow.md`.
- [ ] Update this plan with decisions that affect later phases.

## Phase 3: Contract

- [ ] Create a feature branch from `main` after the design proposal is approved.
- [ ] Read `project/methodology/module-contract-workflow.md`.
- [ ] Add public DSL interfaces and builders in `terracotta-gradle-plugin`.
- [ ] Add KDoc for every public DSL symbol intended for Dokka.
- [ ] Run `:terracotta-gradle-plugin:compileKotlin`.

## Phase 4: Test-driven development

- [ ] Read `project/methodology/module-testing-workflow.md`.
- [ ] Identify behavior and edge cases: missing values, mixed fluent and `set()` usage, provider name resolution, override precedence.
- [ ] Write failing tests against the contract.
- [ ] Run tests and confirm they fail for the expected reason.

## Phase 5: Implementation

- [ ] Read `project/methodology/module-implementation-workflow.md`.
- [ ] Implement the fluent DSL while preserving the existing `Property<T>` API.
- [ ] Refactor and add KDoc.
- [ ] Run `:build :spotlessCheck`.

## Phase 6: Review

- [ ] Read `project/methodology/module-review-workflow.md`.
- [ ] Run code review checklist.
- [ ] By default, confirm auto-review (tests, spotless, build, docs build) passes.
- [ ] Update `CHANGELOG.md` if users need to know about the change.
- [ ] Optional: escalate to human review only when blocked on taste, direction, or high-stakes decisions.

## Phase 7: Documentation

- [ ] Read `project/methodology/module-documentation-workflow.md`.
- [ ] Add or update Diátaxis docs for the Gradle plugin configuration DSL.
- [ ] Cross-link KDoc with `@see`.
- [ ] Final verification: `./gradlew build :spotlessCheck` and `uv run mkdocs build --strict`.

## Phase 8: Push and merge

- [ ] Push the branch and open a pull request to `main`.
- [ ] Review the PR; address feedback and re-run checks.
- [ ] Once CI is green, merge the pull request.
- [ ] Observe CD and confirm the deployment succeeds.

## Phase 9: Release report

- [ ] Archive project artifacts used for this work:
  - Move this plan to `project/plans/archived/<plan>.md`.
  - Move the design proposal to `project/designs/archived/<design>.md` if it is no longer needed.
  - Move the brainstorm note to `project/brainstorm/archived/<note>.md` if one was created.
- [ ] Copy `project/reports/TEMPLATE.md` to `project/reports/release/<datetime>-<title>.md` and fill it out.

## Notes

- The fluent DSL should be additive; existing `Property<T>.set(...)` usage must continue to work.
- Keep the scope focused on the Gradle plugin configuration block. Platform-specific runtime settings (e.g. upload-time options) are out of scope unless explicitly added.
- Consider how the DSL will interact with the external YAML config and the root-level canonical defaults described in `project/designs/25-07-10-add-config-override-pattern.md`.
