# Reviewer Migration Branch

This branch treats the Svelte reviewer as the default desktop reviewer.

The current migration boundary is:

- Qt remains responsible for windowing, menus, dialogs, app lifecycle, and bridge hosting.
- The reviewer page owns the review UI, answer flow, bottom bar controls, and local review state.
- Card content is rendered inside `reviewer-inner` in a sandboxed iframe so card HTML does not share the same execution context as the trusted reviewer chrome.

## Host contract

- `qt/aqt/reviewer.py` selects `SvelteReviewer` for the review screen.
- `qt/aqt/mediasrv.py` exposes the reviewer-specific endpoints used by the Svelte page and the sandboxed card frame.
- `ts/routes/reviewer/+page.svelte` is the trusted reviewer shell.
- `ts/routes/reviewer-inner/index.ts` renders card content inside the sandboxed iframe.

## Compatibility expectations

The branch aims to preserve reviewer behavior where practical, but it does not treat full add-on compatibility as a hard blocker.

Expected to keep working or remain close to current behavior:

- scheduling and backend review operations
- reviewer toolbar and bottom bar actions
- existing reviewer hooks that are still triggered from the desktop side
- card HTML, media, MathJax, and typed answer rendering through the isolated card frame

Expected follow-up areas:

- add-ons that assume direct access to the legacy reviewer DOM
- integrations that depend on the old dual-webview structure
- remaining reviewer features still tracked in the original reviewer migration work

## Next migration steps

- finish any remaining reviewer parity gaps on this branch
- narrow the trusted bridge surface exposed to the reviewer shell
- reuse the same host and isolation model for future editor and preview migrations
