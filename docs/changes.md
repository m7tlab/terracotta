# Changes

## [0.8.0] - 2026-07-13

Stabilizes gallery item identity using persisted state so images can be renamed or reordered without triggering a delete-and-reupload cycle.

### Added

**Core**

- Added optional `key` property to `TerracottaGalleryItem` for stable local identity.
- Added `GalleryIdentityReporter` interface so providers can report stable gallery identities after applying operations.
- Updated `DiffEngine.diff` with a persisted-gallery overload that matches local items to remote items by stable `localKey` even when titles change; falls back to title/ordering matching when no persisted identity exists.
- Added top-level `galleryLocalKey(item)` helper returning `item.key ?: item.imagePath`.

**Gradle Plugin**

- Added `key` property to `TerracottaGalleryExtension` and wired it through `TerracottaExtensionConfigurer` and `TerracottaTaskRegistrar`.
- Updated `TerracottaPlanTask` and `TerracottaApplyTask` to load persisted gallery state from the configured `StateSource`, pass it to `DiffEngine.diff`, and detect duplicate local keys with a warning and fallback to title/ordering matching.
- Updated `TerracottaApplyTask` to merge reported gallery identities from `GalleryIdentityReporter` providers into provider state and save the updated `TerracottaState` after a successful apply.

**Modrinth**

- `ModrinthRegistryProvider` implements `GalleryIdentityReporter`: uploads return the new remote URL, updates keep the existing URL, and deletes omit the identity.
- `ModrinthClient.uploadGalleryItem` returns the created gallery image URL.
