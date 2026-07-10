# Roadmap

This roadmap reflects the current state of the project as of July 9, 2026.

It is based on:

- the current source code
- the live deployed app
- the git history

It is intentionally lightweight and should be updated as priorities shift.

## Current State

Space is already functional as a private encrypted journal with:

- setup via GitHub repo credentials
- PIN-based unlock
- encrypted local and remote storage
- local-first editing
- background GitHub sync
- tags
- folders
- search
- read-only thought view
- responsive desktop and mobile layouts
- PWA shell caching

## Recently Completed Themes

The most recent development work has centered on:

- mobile responsiveness
- folder UX
- touch gestures and drag/drop
- sync reliability
- read-only mode polish
- installability/PWA experiments

## Near-Term Priorities

### 1. Documentation And Maintainability

Why:

- the project has been moving fast
- most logic lives in a single inline script
- maintainability currently depends on clear repo docs

Good next work:

- keep docs current as behavior changes
- tighten naming and comments in fragile logic areas
- reduce hidden coupling between UI and sync state

### 2. Mobile Interaction Polish

Why:

- the product is effectively mobile-first
- touch gestures are central to the experience
- this is one of the easiest places for regressions

Good next work:

- verify triple-tap and press-and-hold feel reliable
- refine touch drag/drop ergonomics
- keep folder interactions understandable on small screens

### 3. Sync And Offline Confidence

Why:

- journal trust depends on reliable saving
- local-first and background sync are core product promises

Good next work:

- improve visibility of pending-sync state
- tighten edge-case recovery when remote fetch fails
- verify resilience around reloads and reconnects

### 4. Security Hardening

Why:

- the app stores repo credentials locally
- journal content is sensitive by nature

Good next work:

- audit token handling assumptions
- continue reducing XSS exposure risk
- document safe operational practices more clearly

## Medium-Term Opportunities

### Code Structure Cleanup

Potential direction:

- gradually split the inline script into smaller modules
- keep the app static and simple while improving maintainability

### Search Improvements

Potential direction:

- richer filtering
- folder-aware search UX
- better empty-state messaging

### Thought Presentation

Potential direction:

- richer markdown support
- better long-form reading experience
- better handling of larger thought collections

## Explicit Non-Goals For Now

These are not natural next steps unless the project direction changes:

- adding a large framework just for structure
- building a custom backend without a clear product reason
- expanding features faster than documentation and maintenance can keep up

## Open Questions

These are the main product/engineering questions still hanging over the project:

- should GitHub remain the long-term sync layer?
- should token handling be redesigned?
- should recovery remain impossible by design?
- should the app stay single-file simple, or begin controlled modularization?
- how much richer should the editor become before complexity outweighs the benefit?

## Update Rule

If priorities shift in a meaningful way, update this file alongside:

- [README.md](README.md)
- [APP_MAP.md](APP_MAP.md) when visible flows change
- [ARCHITECTURE.md](ARCHITECTURE.md) when system behavior changes
