## Purpose
This repo is a small static single-page celebration animation. The guidance below helps an AI coding agent make safe, useful edits quickly and follow project-specific conventions.

## Big picture
- Single-page static site: `index.html` is the app entry. It assembles scripts from `file/` and draws an animated heart/tree on a `<canvas>`.
- Core animation and data model are in `file/love.js` (exports globals: `Tree`, `Point`, `random`, `bezier`) and UI helpers in `file/functions.js` (e.g. `typewriter`, `timeElapse`).
- Runtime uses jQuery and Jscex (async transpiler) loaded from `file/` in index.html. No bundler or build step.

## Key files and patterns (examples)
- `index.html` — boots the app, defines `opts` (branch/bloom/footer config) and registers async flows using Jscex. Example pattern you will see: `Jscex.compile("async", function() { ... })` and `eval(...)` around compiled code.
- `file/love.js` — contains constructors: `Seed`, `Bloom`, `Branch`, `Tree`. `new Tree(canvas, width, height, opts)` is the main entry for the canvas animation.
- `file/functions.js` — small helpers: a `typewriter` jQuery plugin and `timeElapse(date)` that updates `#clock`.
- `aud.mp3`, `img2.png` — static assets referenced directly from `index.html`.

## Project-specific conventions and gotchas
- No module system: files attach constructors and helpers to `window`. When editing, preserve these globals (e.g., `window.Tree = Tree`) or keep the same global names to avoid breaking `index.html`'s imperative boot sequence.
- Script load order matters: `jquery.min.js` and the Jscex scripts must be loaded before `functions.js` and `love.js`. Don't reorder script tags in `index.html` unless you update code accordingly.
- Async flows rely on Jscex compile + eval. Keep the pattern `eval(Jscex.compile("async", function () { ... }))` if modifying those animations.
- Visual configuration is data-driven: the `opts` object in `index.html` contains numeric arrays for branches and bloom counts — edit carefully and verify visually.
- Canvas responsiveness: on resize the page triggers a full reload (see `functions.js`). Expect whole-page reloads on width/height change.

## How to run locally (quick checks)
1. Static open: double-click or open `index.html` in a modern browser (Chrome or Firefox). Some browsers may block audio autoplay; clicking the canvas triggers `playAudio()`.
2. Run from a local HTTP server (recommended to avoid file:// restrictions):

   python3 -m http.server 8000

   then open: http://localhost:8000

3. Smoke checks: verify the canvas draws the tree and the text animates. If nothing shows, open DevTools console — common errors are missing script order or syntax errors in edited files.

## Editing guidance
- To extend the animation: add functions or tweak `opts` in `index.html`. Keep changes small and visually test in the browser.
- When refactoring `love.js`, preserve the global API surface (e.g., `Tree`, `Point`) or update `index.html` accordingly.
- If you add third-party libs, drop them into `file/` and add a `<script>` tag in `index.html` in the correct load order.

## No automated tests/build
- There is no build, test harness, or package manifest. Keep edits minimal and verify manually in the browser. If you want CI or linting, add a lightweight config (ESLint, package.json) and we can wire it in.

## Integration points and external deps
- External integrations: none at runtime. The page references Chrome/Firefox download links but does not call external APIs.
- Runtime libraries are local copies in `file/`: `jquery.min.js`, `jscex*.js` — do not replace with a different major version without validating the Jscex-compile usage.

## If you are an AI agent: checklist for a safe PR
- Preserve global names (`Tree`, `Point`, `seed`, etc.) or update `index.html` to match your refactor.
- Keep script load order intact. Test visually after any change.
- When changing numeric `opts` arrays, run the site and take a screenshot to ensure the tree still fits the canvas.
- Commit messages: short, present-tense, reference the area changed (e.g., `fix: branch coords in index.html`).

---

If you want, I can: create this file in the repo and (with your permission) make a git commit and attempt to push to the remote; tell me whether to proceed with a default commit message `chore: add copilot instructions` and whether you'd like me to push.
