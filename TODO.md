# TODO

- Add support to load any Terracotta config value from file just using `<field>Path` convention, e.g. `descriptionPath`
- Add [Hangar provider-specific link settings](./plans/2026-07-add-hangar-provider-link-settings-plan.md)

- Add [Override Pattern](./designs/25-07-10-add-config-override-pattern.md)
- Add [Fluent Gradle Plugin Config DSL](./plans/2026-07-12-fluent-gradle-plugin-config-dsl-plan.md)
- Add [Import Task](./designs/26-07-12-add-task-import.md)

- Rename `environment` to `gameEnvironment`

- Finish [External YAML Configuration](./designs/26-07-10-add-config-external-yaml.md)

- Add "create" task

- Try migrate ClockTime to this project
- Ask for friend feedback on the project

---

- Add support for multiple Terracotta files
    - Import mode
    - `platform/` convention

- Add [Terracotta Cloud](./designs/26-07-11-add-cloud-terracotta.md)  in a separate private repo
    - Add website with auth, token management, billing (Stripe), etc
- [gha] Add [Terracotta GHA Module](./designs/26-07-12-add-module-gha.md)

- Add standalone CLI

- Add Provider feature support table to integration docs

- Add [Cloud Preview Service](./designs/25-07-12-add-cloud-preview-service.md) (paid Cloud plan, web UI + CLI)
- Add [Terracotta Registry Gateway](./designs/26-07-11-add-cloud-registry-gateway.md)

---

- Add [Config Validation](./designs/25-07-12-add-config-validation.md)

- Add [Authentication Workflows](./designs/25-07-10-add-auth-workflows.md)

- Add [Staging Environments](./designs/25-07-12-add-cloud-staging-environments.md) like Modrinth Staging

- Add [YAML Schema for IDEs](./designs/26-07-11-add-ide-yaml-schema.md)

- Add [CurseForge Provider](./designs/25-07-10-add-provider-curseforge.md)
---
- Add [Provider Toposort](./designs/25-07-12-add-provider-toposort.md)
- Add [Git Provider](./designs/25-07-12-add-provider-git.md) (host-agnostic Git CLI for tags and file commits)
- Add [GitHub Provider](./designs/25-07-12-add-provider-github.md) (GitHub control-plane metadata: description, topics, homepage, license, lifecycle)
- Add [GitHub Release Provider](./designs/25-07-12-add-provider-github-release.md) (GitHub Releases distribution, depends on Git Provider for tags)
- Add to GitHub modules docs to clarify: `terracotta-github` (Pulumi infra), `terracotta-provider-github` (control provider), `terracotta-provider-github-release` (release provider), and `terracotta-provider-git` (host-agnostic Git CLI)

---

- Integrate `test_release_smoke.py` into the CI/CD release pipeline so it runs automatically after `release.yml` completes successfully, validating the release end-to-end instead of relying on a manual post-release step.

- Add automated release notifications (e.g. Discord, Slack, or email), so release status is communicated proactively rather than requiring developers to monitor the GitHub Actions page.
    - Add proactive notification mechanism for deployments and releases
- Add contact point

