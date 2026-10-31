+++
id = "PL-002"
title = "Add Hangar Provider-Specific Link Settings"
status = "open"
priority = "medium"
+++

---
description: Add provider-specific Hangar link configuration so users can customize Top labels and add Sidebar sections.
---

# Add Hangar Provider-Specific Link Settings

This plan follows `project/methodology/module-development-workflow.md` for a **new public API / major feature** that
touches `terracotta-core`, `terracotta-provider-hangar`, `terracotta-gradle-plugin`, and `docs`.

## Source of truth

- TODO item: `Add Hangar provider-specific link settings` (`project/TODO.md`)
- Design proposal: `project/designs/26-07-12-add-hangar-provider-link-settings.md` (to be written)

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

- [ ] Decide whether to skip this phase; the design direction was already discussed.
- [ ] If open questions remain, open a brief `project/brainstorm/26-07-12-add-hangar-provider-link-settings.md` note.
- [ ] Capture trade-offs between generalizing sections in the canonical model vs. keeping them Hangar-specific.

## Phase 2: System design

- [ ] Read `project/methodology/module-system-design-workflow.md`.
- [ ] Investigate the Hangar API schema for link sections (Top and Sidebar) and confirm whether the flat `homepage`/`source`/`issues`/`wiki`/`discord` fields still work.
- [ ] Decide the canonical-to-Hangar mapping: default labels for known fields, plus provider-specific overrides.
- [ ] Define the provider-specific configuration shape for `terracotta.yml` and the Gradle DSL.
- [ ] Write `project/designs/26-07-12-add-hangar-provider-link-settings.md` with public API signatures and examples.
- [ ] Complete the design review checklist from `project/methodology/module-review-workflow.md`.

## Phase 3: Contract

- [ ] Create a feature branch from `main` after the design proposal is approved.
- [ ] Read `project/methodology/module-contract-workflow.md`.
- [ ] Add the Hangar-specific link-section model in `terracotta-provider-hangar` (e.g. `HangarLinkSection`, `HangarLink`, `HangarLinkSectionType`).
- [ ] Add the provider-specific configuration model (e.g. `HangarLinksConfig` / Gradle DSL block).
- [ ] Update `TerracottaProjectLinks` only if the design requires a canonical change; otherwise keep it unchanged.
- [ ] Add KDoc for every new public symbol.
- [ ] Run `:<module>:compileKotlin` for affected modules.

## Phase 4: Test-driven development

- [ ] Read `project/methodology/module-testing-workflow.md`.
- [ ] Add unit tests for the canonical-to-Hangar mapping, including default Top labels and custom overrides.
- [ ] Add tests for Sidebar section serialization and round-trip behavior.
- [ ] Add tests for the Gradle DSL and YAML config loading.
- [ ] Add tests verifying Modrinth is unaffected by the new Hangar-only config.
- [ ] Run tests and confirm they fail for the expected reason.

## Phase 5: Implementation

- [ ] Read `project/methodology/module-implementation-workflow.md`.
- [ ] Implement the Hangar link-section model and mapping from `TerracottaProjectLinks` to the native Hangar representation.
- [ ] Wire provider-specific link settings into `HangarClient.updateProject` and `HangarStateProvider.fetchProject`.
- [ ] Update the Gradle DSL with a `links { ... }` block under the Hangar provider extension.
- [ ] Update the YAML config loader to parse the new `providers.hangar.links` section.
- [ ] Run `:build :spotlessCheck`.

## Phase 6: Review

- [ ] Read `project/methodology/module-review-workflow.md`.
- [ ] Run the code review checklist.
- [ ] Confirm auto-review (tests, spotless, build, docs build) passes.
- [ ] Update `CHANGELOG.md` with the user-visible change.
- [ ] Escalate to human review if the public API shape or multi-module scope is contentious.

## Phase 7: Documentation

- [ ] Read `project/methodology/module-documentation-workflow.md`.
- [ ] Update `docs/content/modules/provider-hangar/tutorials/using-hangar.md` with the new link settings.
- [ ] Update `docs/content/modules/core/reference/config-schema.md` with the `providers.hangar.links` shape.
- [ ] Update `docs/content/modules/gradle-plugin/how-to-guides/kotlin-dsl-configuration.md` if the DSL changes.
- [ ] Document the distinction between canonical `links` (portable semantics) and `providers.hangar.links` (Hangar-only presentation).
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
