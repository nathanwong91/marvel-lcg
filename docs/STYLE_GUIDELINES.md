# Style Guidelines

Conventions for this fork. Follow them when adding or changing UI, TypeScript, or Python.

---

## 1. UI (CSS / HTML)

### Theme tokens

All colours, radii, shadows and fonts come from `public/css/theme.css` (`:root` tokens). Do not hard-code
colours in component CSS or inline styles. If a value is missing, add a token there.

| Token group    | Examples                                         |
| -------------- | ------------------------------------------------ |
| Surfaces       | `--bg`, `--surface-1`, `--surface-2`, `--border` |
| Text           | `--text`, `--text-muted`                         |
| Accents        | `--accent` (Marvel red), `--accent-2`, `--success`, `--danger` |
| Shape / motion | `--radius-s/m/l`, `--shadow-s/m`, `--ease`, `--blur` |

`theme.css` loads **last** on every page (`main.html`, `marvel.html`). It overrides the legacy per-component
files in `public/css/marvel/`; it does not replace them. Prefer moving a rule into `theme.css` over editing
a legacy file, unless the legacy file owns layout or sizing.

### Rules

- Layout and sizing use the existing scale variables (`--font-size`, `--font-size-out`, `--card-width`).
  Do not mix in raw `px` for anything that scales with the 3D scene.
- z-index values are tokens in `public/css/marvel/marvel.css` (`--z-index-*`). Never write a bare number.
- Buttons: use `.button` (in-game) or `.menu-btn` (menus). Do not add a third button style.
- Every interactive element needs a `:hover` and a visible `:focus-visible` state.
- Respect `prefers-reduced-motion`: gate non-essential transitions and transforms behind it.
- Panels float on `--surface-1` with `backdrop-filter: var(--blur)`, `--border`, `--radius-m`.
- System font stack only (`--font-ui`); no web-font downloads.

### File organisation

- New in-game component CSS: one file per component in `public/css/marvel/`, linked in `marvel.html`.
- Element ids are kebab-case (`save-quit-btn`). Classes are kebab-case.
- Debug-only UI carries the `debug-only` class; release-only carries `release-only`.

---

## 2. TypeScript (`public/js`)

- Source is `.ts`; compiled `.js` / `.js.map` are git-ignored. Never edit or commit the `.js`.
- Compile with **TypeScript 5.x** (`tsc` in `public/js`). TS 6+ rejects the repo's `moduleResolution: node10`.
- Code is organised as classes with `static` members (`Button`, `Command`, `Game`...). Match that; do not
  introduce free-floating module state or a framework.
- 4-space indent, no trailing semicolons on statements unless the surrounding file uses them.
- Server calls go through `fetch` with relative URLs; user-visible feedback goes through `Notify`.
- A user action that can lose progress asks for confirmation (see `Button.doQuit`).

---

## 3. Python (`engine/`, `game/`)

- Classes and methods are `PascalCase` (`SaveScene`, `LoadReplay`); variables and parameters are `snake_case`.
- Full type annotations. Config values go through `ConfigVariables` and `launch.json`, not literals.
- Web routes are registered in the `server_*.py` `__init__` with `AddAwaitGetSecurity` / `AddPostSecurity`
  so auth and version checks apply. Never call `web_app.router.add_*` directly.
- Guard handlers against "no game running" (`session.scene` is `None` outside a game) and return a status
  code instead of letting an assert become a 500.
- Card scripts are Python and run unsandboxed. Never load third-party scripts without reading them.

---

## 4. Saves

- Saves are scene JSON written by `session.SaveScene` to the first folder in `replay_folders`
  (`./replays/`, git-ignored). They show up on the **Replay / Continue** page and resume from there.
- `/save_local` is the one endpoint for "save to disk". Do not add a second save path.
