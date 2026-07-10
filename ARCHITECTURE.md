# Architecture

This document explains how Space works internally at a system level.

## High-Level Model

Space is a single-page browser app with four main responsibilities:

1. connect to a GitHub repo
2. unlock encrypted journal data with a PIN
3. let the user edit journal state locally
4. sync the encrypted state back to GitHub

There is no application server. GitHub is used as the remote storage layer.

## Main Components

### UI Shell

The full UI is defined in `index.html` and styled in `styles.css`.

Main screen regions:

- setup screen
- PIN screen
- editor card
- folder panel
- search bar
- entries list
- read-only mode inside the editor card

Reference:

- [APP_MAP.md](APP_MAP.md)

### State Model

Primary in-memory state:

- `thoughts`
- `folders`
- `currentView`
- `editingId`
- `readModeActive`
- `readModeThoughtId`
- `currentSha`
- `currentSalt`
- `cryptoKey`
- `dirtyVersion`
- `syncing`

The code is written in a local-first style:

- update local in-memory state
- write encrypted local cache
- re-render UI
- mark changes for background sync

## Data Model

### Thought

Expected fields:

- `id`
- `createdAt`
- `updatedAt`
- `tag`
- `body`
- `folderId`

### Folder

Expected fields:

- `id`
- `name`
- `createdAt`

### Stored Remote Payload

`thoughts.enc.json` stores:

- `version`
- `updatedAt`
- `salt`
- `iv`
- `ciphertext`

The decrypted payload currently contains:

- `thoughts`
- `folders`

Backward compatibility exists for an older payload shape where the decrypted value was just an array of thoughts.

## Startup Flow

### First Decision

On load, the app checks whether GitHub credentials already exist in `localStorage`.

- if missing: show setup screen
- if present: show PIN screen

### Setup Flow

The setup screen:

- collects GitHub username
- collects repo name
- collects Personal Access Token
- validates access by calling the GitHub repo endpoint
- stores credentials locally

### Unlock Flow

After PIN submission, the app tries:

1. fetch encrypted payload from GitHub
2. if that fails, load encrypted local cache
3. if neither exists, initialize a new empty journal

If a payload exists:

- derive the key from the PIN and stored salt
- decrypt the ciphertext
- hydrate `thoughts` and `folders`
- reveal the journal

## Encryption Model

### Key Derivation

The app derives a symmetric key from the PIN using:

- `PBKDF2`
- `SHA-256`
- `100000` iterations

### Encryption

The actual payload encryption uses:

- `AES-GCM`

Each encrypted save generates a fresh IV.

### Important Scope Boundary

The journal content is encrypted before upload, but the GitHub token itself is stored in browser local storage. This is an intentional product tradeoff in the current design and should be treated as a major security consideration.

See:

- [SECURITY.md](SECURITY.md)

## Local Persistence

Local storage keys currently used:

- `space-theme`
- `space-github-creds`
- `space-local-encrypted`
- `space-pending-sync`

The local encrypted cache exists to support:

- offline unlock
- offline editing
- later resync when connectivity returns

## Sync Model

### Remote Storage

Remote sync uses the GitHub Contents API against:

- `thoughts.enc.json`

### Save Strategy

The app is local-first:

1. update in-memory state
2. encrypt and write local cache
3. re-render immediately
4. mark sync as pending
5. push encrypted payload in the background

### Conflict Handling

The app tracks GitHub file SHA values and retries on conflict-style failures such as:

- `409`
- `422`

If a sync is already in flight and new edits arrive, `dirtyVersion` prevents the app from incorrectly clearing the pending-sync flag.

## Rendering Model

Rendering is hand-written DOM manipulation rather than framework-driven state reconciliation.

Key render functions:

- folder list rendering
- entries rendering
- read-only mode toggling
- search filtering

This makes targeted changes straightforward, but requires care to avoid stale DOM assumptions.

## Responsive Model

The same app serves both modes:

- desktop GitHub Pages web app
- mobile/PWA-first layout

The app does not have separate templates. Responsive behavior is handled through:

- CSS breakpoints
- touch gesture handlers
- mobile-specific folder collapse logic
- touch drag-and-drop polyfill

## Offline / PWA Model

`sw.js` caches the app shell:

- `index.html`
- `styles.css`
- icons
- manifest

It intentionally does not cache GitHub API traffic.

Offline behavior:

- cached shell can open
- local encrypted blob can unlock existing content
- pending changes can sync later when the browser comes back online

## Maintenance Hotspots

The most sensitive change areas are:

- unlock and setup flow
- encryption and sync logic
- read-only mode
- touch drag/drop behavior
- folder interactions
- responsive CSS around the `640px` breakpoint

For feature-level entry points, use:

- [APP_MAP.md](APP_MAP.md)
