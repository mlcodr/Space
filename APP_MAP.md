# Space App Map

This document is a maintainer-facing map of the current `Space` app as verified from the codebase and the deployed site on July 9, 2026.

## What Space Is

`Space` is a static journal web app hosted on GitHub Pages and designed to behave like a mobile-friendly PWA. It has no custom backend. The browser talks directly to the GitHub Contents API, stores encrypted data locally for offline use, and syncs `thoughts.enc.json` back to the repo.

Primary files:

- `index.html`: markup and nearly all app logic
- `styles.css`: full visual system and responsive behavior
- `manifest.json`: PWA manifest
- `sw.js`: service worker and offline shell caching
- `thoughts.enc.json`: encrypted journal payload

## Live State Snapshot

Verified against the deployed app at `https://mlcodr.github.io/Space/`:

- Date shown in editor header: `Jul 9, 2026`
- Visible saved thoughts: `2`
- Visible folders: `0`
- Search placeholder: `search your thoughts (2)`
- Visible entries:
  - `July 5`, `10:56pm`, tag `fdfs`, excerpt `How is the first person died?`
  - `July 5`, `4:30pm`, tag `sadfgdd`, excerpt `He was a very lustful person`

## Screen Map

### 1. Setup Screen

Purpose:

- Connect the app to a GitHub repo before the journal can be unlocked.

Visible controls:

- `username`
- `repo name`
- `Personal Access Token`
- `Save & Continue`
- inline validation error message

Behavior:

- Validates repo access by requesting the repo metadata from the GitHub API.
- Stores repo credentials in browser `localStorage`.
- On success, hides setup and shows the PIN screen.

Code:

- Markup: `index.html:18-38`
- Submit logic: `index.html:490-520`
- Visual styling: `styles.css:944-1069`

### 2. PIN Screen

Purpose:

- Unlock the encrypted journal using a PIN-derived AES key.

Visible controls:

- PIN input
- `Enter`
- inline error message
- warning text that there is no recovery path

Behavior:

- First tries to fetch `thoughts.enc.json` from GitHub.
- If that fails, falls back to the locally cached encrypted blob.
- If neither exists, creates a new empty journal.
- On success, reveals the main journal UI.

Code:

- Markup: `index.html:40-49`
- Unlock flow: `index.html:544-644`
- Startup routing: `index.html:651-659`
- Visual styling: `styles.css:1071-1201`

### 3. Main Journal Screen

Purpose:

- Write, browse, search, read, organize, and manage thoughts.

Major regions:

1. Editor card
2. Folder panel
3. Search bar
4. Entries list

Code:

- Main markup: `index.html:51-129`
- Visual shell: `styles.css:117-383`

### 4. Read-Only Thought View

Purpose:

- Show a thought in a cleaner reading layout inside the top card.

Visible controls:

- rendered thought body
- copy button
- word count

Behavior:

- Hides the write editor and tag input
- Hides save buttons while reading
- Closes when the read-only area is clicked

Code:

- Markup: `index.html:76-87`
- Enter/exit logic: `index.html:704-736`
- Copy behavior: `index.html:1623-1642`
- Styling: `styles.css:196-292`

## Main UI Map

### Editor Card

Visible elements:

- current date
- theme toggle
- optional tag field
- large thought textarea
- `Save thought`
- `Cancel` when editing
- editor word count
- save status
- sync status

Behavior:

- Textarea auto-grows with content
- Word count updates live
- Save creates a new thought or updates the current one
- Save is local-first and syncs in the background

Code:

- Header and fields: `index.html:54-96`
- Theme toggle logic: `index.html:135-144`
- Auto-grow and word count: `index.html:788-862`
- Save/update logic: `index.html:1546-1598`
- Cancel edit: `index.html:1600-1607`
- Save/sync state logic: `index.html:298-462`
- Editor styling: `styles.css:146-195`, `styles.css:1203-1246`

### Search Bar

Visible elements:

- magnifying glass icon
- search input

Behavior:

- Filters thoughts by body text or tag
- If a folder is currently selected, searching resets the view back to all thoughts first
- Placeholder shows the total thought count

Code:

- Markup: `index.html:117-124`
- Placeholder update: `index.html:922-923`
- Filter logic: `index.html:1480-1514`
- Input handler: `index.html:1609-1618`

### Folder Panel

Desktop behavior:

- Always visible in the left column
- Currently shows only `+ New folder` when there are no folders

Mobile behavior:

- Collapses into a `Folders` toggle bar
- Opens a stacked dropdown-style panel when tapped

Actions:

- Create folder
- Select folder to filter thoughts
- Toggle back to all thoughts by tapping the active folder again
- Rename folder
- Delete folder
- Drag thoughts into folders

Code:

- Markup: `index.html:101-115`
- Render logic: `index.html:917-1045`
- Select folder: `index.html:1047-1055`
- Folder menu and inline rename/delete: `index.html:1057-1182`
- Folder CRUD: `index.html:1184-1204`
- Folder move target handling: `index.html:1206-1225`
- Folder toggle on mobile: `index.html:1644-1653`
- Desktop styling: `styles.css:384-555`
- Mobile collapsible styling: `styles.css:1460-1617`

### Entries List

Each entry card shows:

- full month/day
- short time
- optional folder badge
- optional tag badge
- excerpt of the thought body

Behavior:

- Sorted newest first
- Empty states for no thoughts, no thoughts in a folder, and no search matches
- Supports editing and deletion through hidden actions

Code:

- List rendering: `index.html:1242-1275`
- Entry card creation: `index.html:1277-1478`
- Empty states: `index.html:1251-1264`, `index.html:1503-1512`
- Entry styling: `styles.css:556-940`

## Buttons, Controls, and What They Do

### Top-Level Buttons

- `Save & Continue`: validates GitHub repo/token and stores repo credentials
- `Enter`: decrypts or initializes the journal
- theme toggle: switches `data-theme` between `dark` and `light`
- `Save thought`: writes a new thought or saves edits
- `Cancel`: exits edit mode and clears current editor content
- read-only copy button: copies the full thought body to clipboard
- mobile `Folders` toggle: expands or collapses the folder list

### Entry-Level Buttons

- `Edit`: loads a thought into the editor
- `Delete`: reveals a yes/no confirmation row
- `yes` / `no`: confirm or cancel deletion

### Folder-Level Buttons

- `+ New folder`: swaps into an inline input
- `Rename`: replaces the folder label with an inline input
- `Delete`: swaps row into inline folder deletion confirmation

## Gesture Map

### Verified Live

- Tap/click a thought row: opens read-only mode
- Tap the read-only area: closes read-only mode
- Tap mobile `Folders`: opens and closes the mobile folder tray

### Source-Defined Behavior

These are explicit in code and are the intended interaction model:

- Triple tap a thought row: reveals `Edit` and `Delete`
  - `index.html:1412-1462`
- Press and hold a folder row for about 650ms: opens folder `Rename` / `Delete`
  - `index.html:948-997`
- Drag a thought onto a folder row on desktop
  - `index.html:998-1005`, `index.html:1283-1294`
- Touch-drag a thought on mobile
  - opens folders automatically if needed
  - creates a floating ghost card
  - supports edge-scrolling while dragging
  - drops into folder rows
  - `index.html:1666-1802`

### Global Cleanup Behaviors

- Clicking outside entries hides visible entry action bars
  - `index.html:1234-1240`
- Clicking outside a folder dropdown closes it
  - `index.html:1105-1112`

## Responsive Map

### Desktop / GitHub Pages Web App

Layout:

- Large paper-like editor card at the top
- Divider below editor
- Split lower section:
  - left: folders
  - top-middle: search controls
  - right/main: thought entries

Code:

- Desktop split layout styling: `styles.css:381-395`

### Mobile / PWA-First Layout

Layout changes at `max-width: 640px`:

- The lower split layout becomes a vertical stack
- Folder panel becomes a single toggle row
- Folder list becomes a collapsible panel
- Search stays full width
- Entries stack underneath
- Editor spacing tightens
- Save/footer spacing is compressed for phone screens

Additional breakpoints:

- `640px`: main mobile layout change
- `480px`: tighter page padding and smaller controls
- `375px`: smallest-phone tweaks

Code:

- Main responsive rules: `styles.css:1259-1458`
- Mobile collapsible folder overrides: `styles.css:1551-1617`

## Data and Persistence Map

### In-Memory Data Shape

Thought shape:

- `id`
- `createdAt`
- `updatedAt`
- `tag`
- `body`
- `folderId`

Folder shape:

- `id`
- `name`
- `createdAt`

Code:

- Save path that constructs thoughts: `index.html:1575-1583`
- Folder creation: `index.html:1185-1187`

### Encryption

- PIN is turned into a key with `PBKDF2`
- App encrypts JSON payloads with `AES-GCM`
- Payload format stores `version`, `updatedAt`, `salt`, `iv`, and `ciphertext`

Code:

- Crypto helpers and key derivation: `index.html:147-232`
- Encrypted payload file path: `index.html:234-236`
- Current encrypted file format in repo: `thoughts.enc.json`

### Local Storage

Browser local storage keys used by the app:

- `space-theme`
- `space-github-creds`
- `space-local-encrypted`
- `space-pending-sync`

Code:

- Theme storage: `index.html:137-144`
- GitHub creds storage: `index.html:238-247`
- Local encrypted blob: `index.html:328-354`
- Pending sync flag: `index.html:356-395`

### GitHub Sync

Behavior:

- Fetches encrypted journal from repo
- Saves updates as commits to `thoughts.enc.json`
- Retries on SHA conflicts
- Tracks whether local changes are still pending

Code:

- GitHub fetch/save: `index.html:250-296`
- Background sync loop: `index.html:397-462`

## PWA / Installability Map

### Manifest

Defines:

- app name and short name
- standalone display mode
- theme and background colors
- icon set

Code:

- `manifest.json`

### Service Worker

Behavior:

- Caches the app shell
- Avoids caching GitHub API calls and Google Fonts API responses
- Falls back to `index.html` for offline navigation

Code:

- `sw.js`
- Registration in app: `index.html:1816-1823`

## Working Change Map

If a future change request comes in, use this as the fastest entry point:

- Change onboarding or repo connection flow: `index.html:18-49`, `index.html:490-520`, `styles.css:944-1201`
- Change unlock/PIN behavior: `index.html:544-644`
- Change encryption or sync: `index.html:147-296`, `index.html:328-462`, `sw.js`
- Change editor UX: `index.html:54-96`, `index.html:788-862`, `index.html:1546-1607`
- Change read-only mode: `index.html:704-736`, `index.html:1620-1642`, `styles.css:196-292`
- Change folder UX: `index.html:917-1225`, `styles.css:384-555`, `styles.css:1460-1617`
- Change thought cards/list: `index.html:1242-1514`, `styles.css:556-940`
- Change mobile behavior: `styles.css:1259-1617`, `index.html:1644-1802`
- Change app install/offline behavior: `manifest.json`, `sw.js`, `index.html:1816-1823`

## Known Maintenance Reality

The app is easy to ship because it is compact, but most logic lives in one large inline script inside `index.html`. That means:

- feature work is fast
- debugging can be fast
- but the app has limited modular separation

For now, the most important mental model is:

1. setup connects the repo
2. PIN unlocks the encrypted payload
3. editor and list are local-first
4. GitHub sync runs in the background
5. mobile behavior is not separate code, but layered on top through CSS breakpoints and touch gesture handlers
