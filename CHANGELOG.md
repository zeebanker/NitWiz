# NitWiz Changelog

All notable changes to NitWiz are documented here.  
Versioning follows [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`

- **MAJOR** — breaking changes or complete rewrites
- **MINOR** — new features or meaningful capability additions
- **PATCH** — bug fixes, polish, and small tweaks

---
## v1.1.1 — 2026-03-01

### Fixed
- **FAR (folds after raise) logic fixed** — previously didn't reset raise flag between streets, so a villain raise in a prior street and hero folding in a later street was counted as FAR.

## v1.1.0 — 2026-02-26

### Added
- **Firebase multi-layer sync** — data now pushes to Firebase on every change (debounced 1s), on tab hide/focus loss, on page unload, and on a 3-minute heartbeat timer. Eliminates data loss from cache clears or tab switches.
- **Sync Now button** — manual Firebase push on the home screen below the logout button. Animates to green "Synced ✓" for 1.5s on tap, then restores to light blue. Hover brightens; active nudges down matching app button conventions.
- **Last Synced timestamp** — displayed below Sync Now button. Updates on every successful Firebase write. Format: `Last sync'd mm/dd/yy hh:mm:ss`.
- **Hand number pulse animation** — when hand number advances after tapping New, it pulses white 3× over 1 second then settles back to orange.
- **Hand number frozen until New tapped** — number no longer advances immediately on hand completion; stays on current hand until user taps New.
- **Action summary and notes preserved after hand completion** — UI stays populated showing the completed hand's data. Only clears when New is tapped.
- **APP_VERSION JS constant** — single source of truth for version. Splash screen reads it dynamically; update one line to bump version everywhere.
- **CHANGELOG.md** — version control document introduced.

### Changed
- `qoStreetEndActions` emptied — nothing auto-advances except IP Check/Call/Cold Call. All other actions (Limp, Complete, OOP Call, OOP Check) now correctly show → Next Street button, preventing premature street advancement for OOP positions.
- UTG preflop actions changed from Cold Call to Call — UTG is first to act preflop and cannot cold call an open raise.
- Drum re-initializes on expand — correct player snaps to center with orange selected state when drum is un-collapsed.

### Fixed
- **Action summary and notes clearing prematurely** — root cause traced to saveDatabase() → updateAllViews() → qoDrumPopulate() → qoOnPlayerSelect() → qoResetHand() chain firing on every save. Fixed by guarding qoOnPlayerSelect() in qoDrumPopulate when a hand is in progress or complete.
- **Hand number advancing twice** — qoUpdateHandNumber now returns early when qoHandDone is true, preventing the display from jumping to the next number before New is tapped.
- **Sync Now button turning dark after click** — restore after green flash now explicitly sets original rgba(141,196,240) values instead of clearing inline styles to empty.
- **Drum showing wrong selected item on un-collapse** — drum was re-shown via display:block without re-rendering, causing a stale non-centered item to appear orange. Fixed by calling qoDrumInit + qoDrumRenderItems after expand.

---

## v1.0.0 — 2026-02-26

Initial versioned release. Establishes the stable baseline from the Feb 24 build with all QO module improvements applied in this session.

### Core App (Pre-existing at baseline)
- Single-file HTML/JS application (~15,000 lines)
- Firebase Firestore cloud sync with localStorage fallback
- 78-player database with full profile management
- Player archetypes / brain training module
- Live Session Notes with Hand History tracking
- Dashboard with global search
- Base64 watermark at 6% opacity (Chrome file:// compatible)
- Save guards and null safety throughout
- Mouse wheel support on all drum interfaces
- Orange NitWiz brain logo branding

### Quick Observations Module — New in v1.0.0
- **Cold Call chip** added to preflop actions for SB, MP, LP, D, X positions
  - UTG uses "Call" instead (first to act, no open raise to cold call)
- **Fold PF (no data)** button — one-tap to record a preflop fold and advance hand counter, fixing VPIP denominator accuracy
- **Accurate VPIP calculation** — denominator now includes all hands dealt, not just hands where player entered the pot
- **IP/OOP-aware street completion logic**
  - IP: Check, Call, Cold Call auto-advance to next street
  - OOP: all non-terminal actions show Next Street button for manual advance
  - Fold / All-in end the hand for any position
- **River-specific End Hand button** — replaces Next Street on the river; orange color to distinguish from blue Next Street
- **Auto-save on hand completion** — stats update immediately when hand ends, without waiting for New
- **Notes preserved after hand completion** — action summary and notes stay visible until user taps New; notes entered post-completion are captured on New tap
- **New Hand button** — replaced "+ New" text with "New" plus minimal line-art palm icon
- **Player drum collapses after selection** — player name displayed in orange with chevron; tap name to re-expand drum
- **Version label on splash screen** — displays current app version below copyright, driven by APP_VERSION JS constant

### Bug Fixes in v1.0.0
- Fixed VPIP denominator: pure preflop folds were excluded from hand count
- Fixed Call bypassing IP/OOP logic via qoStreetEndActions
- Fixed Limp, Complete, Cold Call incorrectly auto-advancing for OOP positions
- Removed overly aggressive save guard that would have blocked notes from being captured on New tap
- Undo logic updated to use IP-aware _deriveAwaiting helper
