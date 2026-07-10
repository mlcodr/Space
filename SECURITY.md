# Security

Space is a private journal app, so security expectations need to be explicit.

This document describes the current security model, what it protects well, and what it does not protect well.

## What Space Protects

The journal content itself is encrypted in the browser before it is written to the repository.

Current protection model:

- PIN-based key derivation with `PBKDF2`
- encrypted payload with `AES-GCM`
- encrypted remote storage in `thoughts.enc.json`
- encrypted local offline cache in browser storage

This means the thought content is not stored in plaintext in the GitHub repo.

## What Space Does Not Fully Protect

### GitHub Token Storage

The GitHub Personal Access Token is stored in browser `localStorage` so the app can sync directly from the client.

Implications:

- the token is not encrypted by the app
- anyone with access to that browser profile can potentially retrieve it
- any serious XSS bug would be especially dangerous because it could expose repo credentials

This is one of the most important security realities in the current design.

### PIN Recovery

There is no recovery system.

If the PIN is lost:

- encrypted journal contents cannot be decrypted
- the app does not provide recovery questions or backup recovery flow

This is intentional in the current implementation.

### Browser Trust Boundary

Space trusts the local browser environment.

If the browser or device is compromised, the app cannot protect against:

- local storage inspection
- malicious extensions
- active browser-level malware
- someone using an already unlocked session

## Threat Model Summary

Space is strongest against:

- casual inspection of the GitHub repo contents
- accidental plaintext leakage into the repo
- offline reading of `thoughts.enc.json` without the PIN

Space is weaker against:

- compromised browser profiles
- token exposure from local storage
- client-side injection issues
- device-level compromise

## Current Security-Sensitive Areas In Code

High-risk code sections:

- crypto helpers and encryption functions in `index.html`
- GitHub credential storage and fetch/save flow
- render paths that use HTML output
- any future code that touches clipboard, token handling, or remote sync

## Safe Change Guidelines

When changing security-sensitive code:

- do not reduce key-derivation cost casually
- do not change encrypted payload fields without documenting migration behavior
- do not add HTML rendering paths without checking for XSS risk
- do not add new local storage secrets without documenting why
- do not add recovery flows casually; they change the security model materially

## Rendering Safety Notes

The app already contains some defensive rendering behavior:

- inline text rendering escapes HTML before applying lightweight markdown formatting
- truncation logic is written carefully to avoid malformed excerpt output

Still, any change around:

- `innerHTML`
- markdown rendering
- tags
- excerpt rendering

should be treated as security-sensitive.

## Operational Recommendations

For users or maintainers who care about stronger safety:

- use a GitHub token scoped as narrowly as possible
- avoid reusing that token elsewhere
- do not use the app in an untrusted browser profile
- treat unlocked browser sessions as sensitive
- keep backups of the encrypted repo data if the journal matters
- keep the PIN somewhere safe, because recovery does not exist

## If The Security Model Changes

Update all of these:

- [README.md](README.md)
- [ARCHITECTURE.md](ARCHITECTURE.md)
- [APP_MAP.md](APP_MAP.md) if visible flows change

Examples of model-changing work:

- replacing local token storage
- adding a server
- changing encryption algorithms
- adding PIN recovery
- changing where decrypted data can exist
