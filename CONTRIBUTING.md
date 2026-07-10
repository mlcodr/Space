# Contributing

This project is small, but it has a few sharp edges. The fastest way to break Space is to make a harmless-looking UI change that accidentally affects encryption, sync, or touch interactions.

This guide exists to keep changes disciplined.

## Ground Rules

- Prefer small, focused changes.
- Read the relevant section of [APP_MAP.md](APP_MAP.md) before editing.
- Treat setup, unlock, encryption, local cache, and GitHub sync as high-risk areas.
- Test both desktop and phone-sized layouts for any meaningful UI change.
- Do not casually change data shape or local storage keys without documenting it.

## Before You Change Anything

Figure out which area your change belongs to:

- onboarding/setup
- unlock/PIN flow
- editor behavior
- read-only mode
- search
- folders
- entry list/card layout
- touch interactions
- sync and offline behavior
- PWA/installability

Then trace it to the correct code sections:

- [APP_MAP.md](APP_MAP.md)
- [ARCHITECTURE.md](ARCHITECTURE.md)

## Change Strategy

### UI Changes

For UI changes:

- inspect both desktop and mobile states
- verify spacing, hidden states, and interaction targets
- watch for regressions in read-only mode and folder collapse behavior

### Behavior Changes

For logic changes:

- preserve local-first behavior
- preserve background sync semantics
- preserve encrypted payload compatibility unless intentionally migrating data

### Security-Sensitive Changes

Be especially careful when touching:

- `deriveKey`
- `encryptPayload`
- `decryptPayload`
- GitHub credential handling
- `thoughts.enc.json` format
- local storage key names

If one of those changes, update:

- [SECURITY.md](SECURITY.md)
- [ARCHITECTURE.md](ARCHITECTURE.md)
- [APP_MAP.md](APP_MAP.md) if the user-facing flow changes

## Manual Verification Checklist

For most changes, verify these flows:

1. setup screen still validates correctly
2. PIN unlock still succeeds with existing data
3. save a new thought
4. edit an existing thought
5. open a thought in read-only mode
6. search by body text or tag
7. create a folder
8. move a thought into a folder
9. reload and confirm state persists
10. check mobile layout at phone width

If the change touches gestures, also verify:

- folder press-and-hold
- thought read-mode tap behavior
- mobile folder toggle
- mobile touch-drag behavior

## Documentation Expectations

If your change affects:

- a visible screen
- a button
- a gesture
- a data shape
- local storage keys
- sync behavior
- security expectations

then update the relevant markdown file in the repo.

At minimum:

- user-visible interaction changes: update `APP_MAP.md`
- architecture or state-flow changes: update `ARCHITECTURE.md`
- security model changes: update `SECURITY.md`
- near-term planning changes: update `ROADMAP.md`

## Current Technical Reality

Space currently has:

- no build step
- no framework
- no automated test suite
- no module split for application logic

That means contributors need to compensate with:

- careful reading
- focused edits
- strong manual verification
- up-to-date docs

## Good First Cleanup Targets

Safe-ish improvements that are often worth doing:

- tightening docs
- clarifying code comments around non-obvious gesture logic
- improving naming in non-crypto helper areas
- reducing UI inconsistency between desktop and mobile
- splitting large logic blocks only when done carefully and incrementally
