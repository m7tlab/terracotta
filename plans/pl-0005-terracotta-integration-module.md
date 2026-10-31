+++
id = "PL-005"
title = "Terracotta Integration Module"
status = "open"
priority = "medium"
+++

---
description: Implementation plan for adding a non-published terracotta-integration module and relocating cross-module integration tests.
---

# Terracotta Integration Module

This plan follows `project/methodology/module-development-workflow.md` for a **new module** that touches
`terracotta-integration` (new), `terracotta-gradle-plugin`, and `docs`.

## Source of truth

- TODO item: None; the design is the source of truth.
- Design proposal: `project/designs/26-07-12-add-module-integration.md`

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

- [ ] Open a fresh `project/brainstorm/<datetime>-<title>.md` file.
- [ ] Spend a short, time-boxed session exploring alternatives, creative angles, and out-of-the-box ideas.
- [ ] Capture the best ideas, trade-offs, and open questions in the brainstorm file.
- [ ] If the brainstorm produces a better direction, update the design proposal and this plan before continuing.
- [ ] Keep the note succinct; the goal is sharper ideas, not a design document.

## Phase 2: System design

- [ ] Read `project/methodology/module-system-design-workflow.md`.
- [ ] Read `project/designs/26-07-12-add-module-integration.md`.
- [ ] Confirm the module is non-published and has no `src/main/kotlin` source set.
- [ ] Confirm the initial test scope is the two Gradle TestKit suites in `terracotta-gradle-plugin`.
- [ ] Classify existing tests in `terracotta-gradle-plugin`, `terracotta-provider-modrinth`, `terracotta-provider-hangar`, and `terracotta-core` as unit vs integration.
- [ ] Confirm only cross-module or runtime-fixture tests move; keep mocked, fast unit tests in their home modules.
- [ ] Decide whether the relocated tests keep their current packages or move to a new `integration.gradle` package.
- [ ] Decide whether `terracotta-integration` proactively depends on all providers or only on the ones the current tests exercise.
- [ ] Complete design review checklist from `project/methodology/module-review-workflow.md`.
- [ ] Update this plan with decisions that affect later phases.

## Phase 3: Contract

- [ ] Create a feature branch from `main` after the design proposal is approved.
- [ ] Read `project/methodology/module-contract-workflow.md`.
- [ ] Verify the new module exposes no public API; there is no production source set.
- [ ] Confirm test dependencies in `terracotta-integration/build.gradle.kts` cover the Gradle plugin, core, state filesystem, and any providers needed.
- [ ] Confirm `terracotta-gradle-plugin` retains the `java-gradle-plugin` plugin and its marker publication.
- [ ] Add KDoc only if any test helper becomes reusable across modules.
- [ ] Run `:terracotta-integration:compileTestKotlin` to verify the test contract compiles.

## Phase 4: Test-driven development

- [ ] Read `project/methodology/module-testing-workflow.md`.
- [ ] Identify behavior and edge cases:
  - Tests exercise the plugin end-to-end through Gradle TestKit.
  - Tests verify task outcomes and project configuration parsing.
  - TestKit classpath helpers still resolve the plugin under test after relocation.
- [ ] Move `TerracottaPluginIntegrationTest` and `TerracottaPluginTaskIntegrationTest` into `terracotta-integration`.
- [ ] Move any shared helpers that are only used by these tests.
- [ ] Run the relocated tests and confirm they fail for the expected reason before wiring dependencies.

## Phase 5: Implementation

- [ ] Read `project/methodology/module-implementation-workflow.md`.
- [ ] Create `modules/terracotta-integration/build.gradle.kts` with no publishing plugins.
- [ ] Add `modules/terracotta-integration/` to `settings.gradle.kts`.
- [ ] Wire test dependencies so the integration module can exercise the Gradle plugin and other modules.
- [ ] Update the relocated tests to build the plugin classpath from the new module context.
- [ ] Remove the integration tests and their test-only helpers from `terracotta-gradle-plugin`.
- [ ] Run `:terracotta-integration:test` and confirm all relocated tests pass.
- [ ] Run `:build :spotlessCheck` for the whole project.
- [ ] Refactor and add KDoc for any new shared test helpers.

## Phase 6: Review

- [ ] Read `project/methodology/module-review-workflow.md`.
- [ ] Run code review checklist.
- [ ] By default, confirm auto-review (tests, spotless, build, docs build) passes.
- [ ] Update `CHANGELOG.md` if users need to know about the change.
- [ ] Optional: escalate to human review only when blocked on taste, direction, or high-stakes decisions.

## Phase 7: Documentation

- [ ] Read `project/methodology/module-documentation-workflow.md`.
- [ ] Add `docs/content/modules/terracotta-integration/README.md` explaining the module's purpose.
- [ ] Update `docs/content/modules/overview.md` to list `terracotta-integration` as an internal/non-published module.
- [ ] Cross-link KDoc with `@see` where appropriate.
- [ ] Final verification: `./gradlew build :spotlessCheck` and `mkdocs build --strict`.

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

<!-- Decisions, blockers, and discoveries go here as work progresses. -->
