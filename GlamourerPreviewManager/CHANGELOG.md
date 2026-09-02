# Changelog

All notable changes to this project will be documented in this file.

## [1.1.4.1] - 2026-09-02

### Fixed
- **Design Selection with Collapsed Design Details**: Fixed an issue where design previews became unresponsive or stuck on a previously selected outfit when the "Design Details" panel in Glamourer was collapsed.
- **Dalamud Plugin Wrapper Reflection**: Unwrapped Dalamud's internal `ExposedPlugin.<plugin>P` proxy field during reflection to access the underlying `Glamourer` instance and `DesignFileSystem.Selection` directly.
- **Tree Node Fallback Disambiguation**: Added automatic duplicate suffix removal (` (2)`) and multi-candidate resolution to fallback label matching, preventing UI freezes when duplicate design names or folder path prefixes are present.
- **Filename Matching**: Prioritized exact filename matches before applying duplicate-number stripping during preview file rediscovery.

## [1.1.4.0] - 2026-09-02

### Added
- **Preview Rediscovery & Review Window**: Added a dedicated Review Window (`/gpm review` or via Settings) to inspect and resolve unassigned or conflicting preview image files:
  - Image thumbnails with middle-click zoom.
  - Categorization of unassigned files (Name conflicts vs. Orphan files).
  - Target design selector prioritizing matching names and unassigned designs.
  - Actions to assign previews, delete image files (with confirmation), or dismiss entries.
  - Auto-assign button for non-conflicting name matches.
  - Search filter and rescan button.
- **Duplicate-Name Disambiguation**: When multiple designs share the exact same name, GPM now tags preview filenames with their short identifier (`"Design [d8f4e2a1].png"`). Uniquely named designs continue to use clean filenames (`"Design.png"`).
- **Retention & Deletion Policy**: Added an "Auto-delete preview images on design deletion" toggle under Settings (*Previews & Storage* tab, default *Off*). When disabled, deleting a design in Glamourer leaves the image file on disk for reassignment.

### Changed
- **Reflection-First Design Resolution**: Prioritized Glamourer's internal design selection reflection as the primary authority during ImGui selection hooks, preventing label string collisions across duplicate design names.
- **Explicit Screenshot Target Locking**: Locked target design GUIDs during screenshot capture to prevent UI focus shifts from saving screenshots to the wrong design.
- **Disk Rescan Synchronization**: Opening the Review Window or running Rediscover refreshes Glamourer's design files directly from disk without requiring a plugin restart.
- **Shared File Protection**: Overwriting or updating a preview image no longer deletes the existing file if another design is currently using it.

### Fixed
- **Duplicate Design Name Preview Cross-Assignment**: Fixed an issue where taking a screenshot, pasting from clipboard, or importing an image for a design that shared its name with another design caused the preview image to be applied to and shared by both designs.
- **Unintended Image Deletion during Scans**: Fixed an issue where unassigned image files in the preview folder could be deleted during folder scans or rediscovery.

---

## [1.1.3.1] - 2026-08-20

### Fixed
- **Incognito Mode Design & Preview Resolution**: Added support for Glamourer's 8-character hex Incognito labels (`Identifier.ToString()[..8]`) and `struct DesignIdentifier` reflection parsing, ensuring preview images update immediately in Incognito Mode.
- **Tab Isolation (Actors & NPCs Tab Protection)**: Restricted preview injection anchors strictly to `DesignPanel` controls (`Export to Dat`, `Apply Mod Associations`) and added active tab validation (`EphemeralConfig.SelectedMainTab == MainTabType.Designs`), preventing preview panels from bleeding into the Actors, NPCs, or Automation tabs.
- **Asynchronous Texture Loading Flash**: Replaced instant error alerts during async disk loading with a smooth loading state (`"Loading preview image..."`), eliminating temporary red error flashes when clicking between designs.

## [1.1.3.0] - 2026-08-13

### Added
- **Zero-Hook Glamourer Preview Window**: Added a dedicated Preview window (`/gpm preview` or `/gpmpreview`) that tracks Glamourer selection automatically via direct reflection. Displays design preview images, middle-click zoom, and one-click controls (**Paste Clipboard**, **Browse Image**, **Take Screenshot**, **Remove Preview**) with zero reliance on native `cimgui.dll` memory hooks.

### Fixed
- **Glamourer Refactor Compatibility**: Fixed design selection reflection to support Glamourer's `Luna.ServiceManager` architecture.
- **MinHook Address Space Resilience**: Wrapped native ImGui function hooks in isolated exception handlers to prevent memory buffer allocation failures in crowded virtual memory environments.

---

## [1.1.2.1] - 2026-07-31

### Added
- **Outfit Roulette Preview Toggle**: Added a toggle option to include outfits in the roulette pool even if they do not have a preview image attached (displays a fallback message in the UI).

---

## [1.1.2.0] - 2026-07-31

### Added
- **Outfit Roulette**: Added a new Outfit Roulette window (`/gpm roulette` / `/gpmroulette`) to randomly select and apply outfits from your active design pool. Features include:
  - **Confirm Before Apply**: Toggle option to preview the selected design before equipping it.
  - **Folder Filters**: Customize your active roulette pool by selecting/deselecting specific category folders.
  - **Friend's Roll Support**: Input a raw number (e.g. from `/random`) to select a design via modulo division (`index = Roll % PoolCount`).
- **Chat Roll Integration**: Added a history-button next to the Friend's Roll input field to instantly insert the last seen `/random` or `/dice` roll from chat. Supports English, German, and French clients with a high-performance string pre-filter to prevent frame-rate overhead.

---

## [1.1.1.1] - 2026-07-23

### Added
- **Sub-Command Routing**: Added support for launching the preview gallery directly using the `/gpm gallery` (or `/glampreview gallery`) command arguments.

---

## [1.1.1.0] - 2026-07-23

### Fixed
- **Missing Preview Image UI Recovery**: Added a fallback path if a design's preview image file goes missing or fails to load, preventing the UI from entering a button-less locked state. Users can now easily re-import, paste, or remove the preview mapping directly.
- **Linux/Steam Deck & Sibling Path Resolution**: Resolved a permanent hang on "Loading design info..." in custom and Linux environments (Wine/Proton prefixes) by dynamically checking for Glamourer's designs folder relative to the active GPM configuration folder parent path.
- **Debounced Logging**: Added defensive log warning limits to prevent frame-by-frame log spam on unresolved design lookups.

---

## [1.1.0.1] - 2026-06-27

### Added
- **Support & Community Links**: Added an in-game "Support on Ko-fi" button directly inside the settings Information tab alongside the Support Discord button.

---

## [1.1.0.0] - 2026-06-14

### Added
- **Glamourer Preview Gallery**: A visual browser window (`/gpmgallery` or `/glampreviewgallery`) displaying Glamourer designs in a collapsible category grid. Double-click a card to instantly apply the design.
- **Gallery Image Fit Modes**: Added a toggle option to fit full images inside the card without cropping (Contain) or scale them to fill the card bounds (Cover).
- **Middle-Click Zoom**: Support for holding the middle mouse button on gallery cards to display a full-screen zoomed preview overlay (hiding tooltips during active zoom).
- **Feature Promo Popup**: A one-time onboarding popup window introducing users to the new gallery on startup.

### Changed
- **Tabbed Settings Restructuring**: Organized settings options into distinct tabs (*Previews & Storage*, *Display & UI*, *Screenshot Capture*, and *Information*) for a much cleaner layout.
- **Decoupled Aspect Ratios**: Separated the Gallery card aspect ratio configuration from the screenshot crop overlay ratio, preventing crop setting changes from affecting existing gallery layouts.
- **External Screenshot Watcher Optimization**: Rewrote the directory file watcher to lazily run only when actively in GPM screenshot capture mode, completely avoiding background file system overhead. Added a toggle to automatically delete original screenshots after import.
- **Text Wrapping in Tips**: Refactored the settings information tips to support automatic word-wrapping.

---

## [1.0.1.1] - 2026-06-12

### Changed
- **Design Resolution Cache**: Implemented O(1) Guid and case-insensitive Name lookup dictionaries (`DesignsById`/`DesignsByName`) to eliminate linear sweeps through the designs list on the UI thread.
- **Window Stack Check Optimization**: Cached Glamourer's window state checks, re-evaluating only on window boundary changes to completely avoid linear stack checks inside hooked ImGui button, tree node, and selectable detours.
- **Busted Texture Path Resolver**: Added temp-based cache-busting image copy caching to force Dalamud's texture wrapper to reload updated screenshot and clipboard preview images instantly.

---

## [1.0.1.0] - 2026-06-10

### Fixed
- **Disabled Button Scope Leak**: Resolved a bug where GPM's injected preview buttons (*Paste Clipboard*, *Browse File*, and *Screenshot*) were grayed out and unusable for NPC designs or newly created designs because they were drawn inside Glamourer's `"Export to Dat"` `BeginDisabled` scope.
- **Layout & Position Preservation**: Detoured native `igTableNextColumn` calls to draw the GPM UI immediately after the button row, placing the UI in the correct position but outside of the disabled scope, keeping GPM buttons fully interactive.
- **Hover & Tooltip Flickering**: Implemented a native ImGui window stack (`windowStack`) to track active window names and child window nesting. Added a tooltip/popup guard (`IsTooltipOrPopup`) in `CheckAndDrawDeferredUI()` to prevent tooltip windows from prematurely consuming the deferred UI flag and drawing GPM elements inside tooltip popups, resolving the issue where the GPM UI would briefly hide or flicker when the mouse hovered over the "Export to Dat" button.
- **Icon URL Configuration**: Corrected the incorrect branch path (`/refs/heads/main` to `main`) in the plugin master list and local manifest files to ensure the plugin icon displays properly in the Dalamud plugin installer.

---

## [1.0.0.0] - 2026-06-05

### Added
- **Core Release**: Initial release of Glamourer Preview Manager. Enables attaching preview/cover images to Glamourer designs, pasting from clipboard, browsing files, and taking cropped screenshots directly in-game.
