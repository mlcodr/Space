# Space

Space is a static, encrypted journal app hosted on GitHub Pages and optimized for mobile-first PWA use.

The app has no custom backend. It runs entirely in the browser, encrypts journal data client-side with a PIN-derived key, stores an offline copy locally, and syncs the encrypted payload to `thoughts.enc.json` in a GitHub repo.

## What It Does

- Connects to a GitHub repo using a Personal Access Token
- Unlocks journal data with a PIN
- Encrypts thoughts and folders in the browser
- Supports writing, editing, deleting, tagging, searching, and foldering thoughts
- Works on both desktop and phone-sized layouts
- Caches the app shell for offline/PWA use

## Current Stack

- `index.html`: app markup and almost all app logic
- `styles.css`: visual system and responsive behavior
- `manifest.json`: PWA metadata
- `sw.js`: offline caching and shell behavior
- `thoughts.enc.json`: encrypted data payload

There is no framework, bundler, or build pipeline right now.

## Running The App

### Production

The app is designed to be deployed on GitHub Pages.

Live URL:

- [Space](https://mlcodr.github.io/Space/)

### Local

Because the app uses a service worker, it behaves best when served over local HTTP rather than opened directly as a `file://` page.

Good local options:

- any simple static file server
- GitHub Pages preview flow
- a lightweight local web server

The app can still be inspected directly from the source files, but PWA behavior will be limited outside normal HTTP hosting.

## Repo Guide

- [APP_MAP.md](APP_MAP.md): full screen-by-screen and feature-by-feature app map
- [ARCHITECTURE.md](ARCHITECTURE.md): data flow, state flow, and sync model
- [CONTRIBUTING.md](CONTRIBUTING.md): how to work on the project safely
- [SECURITY.md](SECURITY.md): security model and important caveats
- [ROADMAP.md](ROADMAP.md): current priorities and likely next steps

## Product Summary

Space is best thought of as:

1. a private journal
2. a local-first encrypted browser app
3. a GitHub-backed sync client
4. a mobile-first PWA with a desktop web fallback

## Known Reality

The project is compact and easy to ship, but most logic currently lives in one inline script inside `index.html`. That makes iteration fast, but it also means maintenance depends heavily on good documentation and careful changes.
