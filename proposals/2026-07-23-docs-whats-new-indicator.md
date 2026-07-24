---
description: Add a "What's New" indicator on the docs homepage that highlights new deployment entries since the visitor's last visit, using localStorage and a createdAt diff.
---

# Proposal: "What's New" Indicator on Docs Homepage

## Problem

Returning visitors to the Terracotta docs homepage have no way to know whether new deployments or releases have shipped since their last visit. The "Last Changes" page exists but requires a deliberate navigation click, and nothing on the homepage signals freshness.

## Goal

Show a lightweight, dismissible "What's New" banner or badge on the docs homepage (`docs/index.md`) when new `deployments.json` entries have been added since the visitor's last visit. The indicator should:

- Appear only when there are genuinely new entries (not on every visit).
- Summarize what changed (count, titles, module badges).
- Link to the full "Last Changes" page for details.
- Be dismissible and stay dismissed until newer entries arrive.
- Require zero backend infrastructure — purely client-side via `localStorage`.

## Current architecture

| Component | File | Role |
|-----------|------|------|
| Deployment manifest | `deployments.json` | Source of truth: array of entries with `version`, `createdAt`, `title`, `summary`, `modules`, `isRelease` |
| Macro renderer | `docs/macros.py` | `deployments()` macro loads `deployments.json`, renders via Jinja2 template |
| Last Changes template | `docs/templates/deployments.j2` | Alpine.js component with filtering, search, module badges |
| Homepage | `docs/index.md` | Static MkDocs Material page with feature cards, flowchart, get-started links |
| Extra CSS | `docs/stylesheets/deployments.css` | Styles for the deployments UI |
| MkDocs config | `mkdocs.yml` | Theme config, macros plugin, extra CSS, hooks |

## Proposed design

### 1. Inject deployment metadata into the homepage

Add a new macro `whats_new_data()` in `docs/macros.py` that emits a `<script>` tag with a JSON blob containing the latest N deployment entries (sorted by `createdAt` descending). This avoids an extra HTTP fetch and keeps the data in sync with the build-time `deployments.json`.

```python
@env.macro
def whats_new_data(limit: int = 5):
    """Inject latest deployment entries as a JSON script tag for the What's New indicator."""
    entries = _load_deployments()
    entries.sort(key=lambda e: e.get("createdAt", ""), reverse=True)
    latest = entries[:limit]
    latest_created = entries[0]["createdAt"] if entries else ""
    payload = {
        "latestCreatedAt": latest_created,
        "entries": latest,
    }
    return f'<script id="whats-new-data" type="application/json">{json.dumps(payload, ensure_ascii=False)}</script>'
```

Call this macro in `docs/index.md` (e.g., right after the front matter / before the first heading):

```markdown
{{ whats_new_data() }}
```

### 2. Client-side diff logic

Add a self-contained `<script>` block (either inline in `index.md` or as a separate JS file in `docs/javascripts/whats-new.js` loaded via `extra_javascript` in `mkdocs.yml`) that:

1. **Reads the JSON blob** from `#whats-new-data`.
2. **Reads `localStorage["terracotta:lastVisitCreatedAt"]`** — the `createdAt` of the most recent entry the user has already seen.
3. **Computes the diff**: filter entries where `entry.createdAt > lastVisitCreatedAt`. If no stored value exists (first visit), suppress the indicator (don't overwhelm first-time visitors).
4. **Renders the indicator** if the diff is non-empty.
5. **On dismiss or navigation to Last Changes**: updates `localStorage["terracotta:lastVisitCreatedAt"]` to `latestCreatedAt`.

```
┌─────────────────────────────────────────────┐
│  localStorage.lastVisitCreatedAt = "2026-07-12T05:39:25Z"  │
│  latestCreatedAt             = "2026-07-13T02:48:13Z"      │
│                                                             │
│  diff = entries.filter(e => e.createdAt > lastVisit)        │
│  // → [0.8.0 "Stable Gallery Identity", 0.7.1, 0.7.0]      │
│                                                             │
│  diff.length > 0  →  show indicator                         │
│  diff.length = 0   →  hide indicator                        │
└─────────────────────────────────────────────┘
```

### 3. UI placement and visual design

Two options, ranked by preference:

#### Option A: Inline banner below the hero (recommended)

Insert a dismissible admonition-style banner between the tagline and the feature cards grid:

```html
<div id="whats-new-banner" class="mdx-whats-new" style="display:none">
  <div class="mdx-whats-new__header">
    <span class="mdx-whats-new__icon">✨</span>
    <span class="mdx-whats-new__title">What's new</span>
    <button class="mdx-whats-new__close" aria-label="Dismiss" onclick="dismissWhatsNew()">×</button>
  </div>
  <div class="mdx-whats-new__body">
    <p id="whats-new-summary">3 new deployments since your last visit.</p>
    <ul id="whats-new-list">
      <!-- Dynamically populated: title, version badge, module badges -->
    </ul>
    <a href="last-changes.md" class="mdx-whats-new__link">View all changes →</a>
  </div>
</div>
```

- Uses the same Material Design color palette (amber accent for the "new" feel).
- Module badges reuse the same SVG icon system from `deployments.j2`.
- Collapsible via `<details>` if the list is long (more than 3 items).

#### Option B: Nav badge on "Last Changes" menu item

Add a small dot or count badge next to the "Last Changes" nav entry. This is subtler but requires overriding the `nav-item` partial in `docs/overrides/partials/`, which is more invasive and harder to keep in sync across Material theme updates.

### 4. Dismissal and state management

| Action | localStorage update | Effect |
|--------|---------------------|--------|
| First visit (no stored key) | N/A | No indicator shown |
| Return visit with new entries | N/A | Indicator shown |
| User clicks "Dismiss ×" | `lastVisitCreatedAt` = `latestCreatedAt` | Indicator hides, stays hidden until newer entries |
| User clicks "View all changes →" | `lastVisitCreatedAt` = `latestCreatedAt` | Navigates to Last Changes; indicator won't reappear next visit |
| User ignores indicator | No update | Indicator persists on next visit |

### 5. Edge cases

- **deployments.json is empty or missing**: suppress indicator, no JS errors.
- **Clock skew**: `createdAt` comparison is string-based ISO 8601, which is lexicographically correct. No client clock dependency.
- **Versioned docs (mike)**: each docs version has its own `deployments.json` snapshot at build time, so the indicator reflects what was new at that version's build. `localStorage` key should be version-scoped (e.g., `terracotta:{version}:lastVisitCreatedAt`) to avoid cross-version noise. The current mike version can be read from `__md_version` or the `data-md-version` attribute on `<html>`.
- **Privacy**: `localStorage` is first-party, no cookies, no external calls. No GDPR concern.

## Implementation plan

### Files to create

- `docs/javascripts/whats-new.js` — self-contained Alpine.js or vanilla JS component for the diff logic and rendering.

### Files to modify

| File | Change |
|------|--------|
| `docs/macros.py` | Add `whats_new_data()` macro |
| `docs/index.md` | Insert `{{ whats_new_data() }}` call and the banner placeholder HTML |
| `docs/stylesheets/deployments.css` (or a new `docs/stylesheets/whats-new.css`) | Add `.mdx-whats-new` styles |
| `mkdocs.yml` | Add `docs/javascripts/whats-new.js` to `extra_javascript` if using a separate file |

### Testing

- **Manual**: visit homepage fresh (no indicator), add a newer entry to `deployments.json`, rebuild, revisit (indicator appears), dismiss (indicator gone), rebuild with no new entries (indicator stays gone).
- **Automated**: add a Playwright or Cypress smoke test that loads the homepage, asserts the banner appears/disappears based on `localStorage` state.

## Alternatives considered

| Alternative | Why not |
|-------------|---------|
| Server-side "last updated" API | Adds infrastructure complexity; the project is a static site on GitHub Pages. |
| RSS/Atom feed with a feed reader widget | Not rejected — see "Medium-term: RSS/Atom feed" below. The localStorage indicator is the lightweight, zero-dependency first step; an RSS feed is a complementary medium-term addition. |
| Git log-based "recently changed pages" | Noisy — includes refactors and doc-only changes that don't matter to users. The `deployments.json` manifest is already curated. |
| SessionStorage instead of localStorage | Would show the indicator on every new tab/session, which is too aggressive. |

## Future extensions

- **Per-module filtering**: let users pick which modules they care about; only show indicators for those modules.
- **Email/notification opt-in**: if a backend is ever added, allow users to subscribe to release notifications.
- **Changelog diff view**: instead of just listing titles, show a collapsible diff of the actual changelog sections between the user's last-seen version and the latest.
- **"What's new in docs"**: extend the indicator to also cover `docs/CHANGELOG.md` entries, not just deployments.

## Medium-term: RSS/Atom feed

The localStorage-based indicator is a zero-infrastructure first step that works for casual returning visitors. As a complementary medium-term addition, we should add an **RSS/Atom feed** for Last Changes entries so that power users and integrators can subscribe via feed readers.

MkDocs Material supports this natively via the built-in [blog plugin](https://squidfunk.github.io/mkdocs-material/plugins/blog/) or the community [rss plugin](https://github.com/Guts/mkdocs-rss-plugin). The approach would be:

1. **Add the RSS plugin** to `mkdocs.yml` (`plugins` section). The `mkdocs-rss-plugin` generates a `feed.xml` at build time from page metadata.
2. **Map `deployments.json` entries to feed items**. Since the Last Changes page is macro-rendered (not individual pages), we have two options:
   - **Option A**: Generate individual markdown pages per deployment entry (e.g., `docs/changes/0.8.0.md`) during the build, so the RSS plugin can pick them up as discrete posts. This also gives each deployment a permalink for deep-linking.
   - **Option B**: Use the blog plugin's `meta`-driven approach with a custom hook that writes deployment entries as blog posts.
3. **Add a feed link** to the homepage "Links" section and the nav header so users can discover the feed URL.
4. **Add `<link rel="alternate">`** to the HTML head for auto-discovery by feed readers.

The RSS feed and the localStorage indicator are complementary:
- **localStorage indicator**: catches the attention of visitors who open the docs site directly.
- **RSS feed**: lets users subscribe and get notified without visiting the site, and can be consumed by CI pipelines, Discord webhooks, or other automation.

## Open questions

1. Should the indicator also fire for versionless entries (e.g., "Docs Site Overhaul") or only for versioned releases?
2. Should we cap the number of items shown in the banner (e.g., 3) with a "and N more" link?
3. Should the dismiss action be per-version (mike) or global?
