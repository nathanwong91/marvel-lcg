# Marvel LCG (personal fork) — Agent Guide

Fork of `irefrixs/marvel-lcg`. Python server (aiohttp) + TypeScript/vanilla-CSS browser client. No JS framework.

## Run it

```sh
python3 -m pip install --user --break-system-packages -r requirements.txt   # no venv on this box
cd public/js && tsc            # TypeScript 5.x only; outputs .js next to .ts
cd ../.. && python3 main.py    # http://127.0.0.1:2345
```

- `assets/` is not in git. It is a symlink to `/mnt/d/Marvel LCG/assets` (from the itch.io release).
- The itch `.exe` in `D:\Marvel LCG` is broken (`No module named 'game_play'`); run from source.
- After a server restart wait for the port: it can sit in TIME_WAIT ~1 min and `main.py` asserts
  `IsPortAvailable`. Kill by PID (`ss -ltnp | grep 2345`), not `pkill -f` (it matches your own shell).
- zsh `node`/`npm` are lazy nvm wrappers that recurse; call `~/.nvm/versions/node/<v>/bin/node` directly.

## Before changing UI or code

Read `docs/STYLE_GUIDELINES.md`. Colours come from `public/css/theme.css` tokens, nothing hard-coded.

## Navigation map

| Want to change      | Look at                                                        |
| ------------------- | -------------------------------------------------------------- |
| Main menu           | `public/main.html`                                             |
| In-game page        | `public/marvel.html`, `public/js/marvel/buttons.ts` (buttons)  |
| Theme / look        | `public/css/theme.css` (loaded last), legacy `public/css/marvel/` |
| HTTP routes         | `engine/device/web/server/server_*.py`                         |
| Save / load         | `game/game_run/game_session.py`, `engine/device/web/server/server_new_game.py` |
| Card scripts        | `docs/card_scripting_guide.md`, `game/ability/`                |

## Testing

Every feature leaves a check behind. Engine tests: `unit_test/`. For UI, load the page in headless Chrome
and read the screenshot, for example:

```sh
chrome.exe --headless=new --user-data-dir='C:\Users\Public\cdp' --virtual-time-budget=4000 \
  --screenshot='C:\Users\Public\x.png' http://127.0.0.1:2345/
```

## Workflow rules

- Never commit `replays/`, `assets`, compiled `.js`, `statistics.json`.
- Do not commit or push unless asked.
- Shareable fixes (bugs, docs) are candidates for PRs upstream to `irefrixs/marvel-lcg`; UI restyle stays in the fork.
