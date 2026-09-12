# pywebview-chrome-kit

Native-feeling window chrome for a [pywebview](https://pywebview.flowmark.dev/) app — a unified
title bar with traffic lights or caption buttons, popup/context menus, a status bar, dialog sheets,
and an in-window menu bar for Windows — in **plain CSS custom properties plus two classic
`<script>` files**. No build step, no bundler, no `import`/`export`; drop the files into a page and
reference them from `index.html`.

Extracted from [SUD Workbench](https://github.com/skalyan91/sud-workbench), a native-feeling
desktop app for linguistic annotation, where this chrome first shipped. That project remains the
reference integration — its `app/mac/`, `app/win/`, `app/linux/` shells and `app/menu_spec.py` are
the Python-side counterpart this kit's comments point to throughout.

## What's in the box

| Directory / file | What it provides |
|---|---|
| `macos-kit/` | The macOS 26 "Tahoe" Liquid-Glass skin: traffic lights, unified title bar, popup menus, SF-Symbol icon masks. See `macos-kit/README.md`. |
| `win11-kit/` | The Windows 11 Fluent skin: caption buttons, menu bar, command bar, Acrylic surfaces, Fluent UI System Icons. See `win11-kit/README.md`. |
| `adwaita-kit/` | A placeholder GNOME/Adwaita-esque skin for Linux, built on `chrome-shared/` rather than sourced with the same fidelity as the other two. See `adwaita-kit/README.md`. |
| `chrome-shared/` | The token/chrome base `adwaita-kit/` (and, at equal specificity, `macos-kit/`) build on, so the ~1,200 lines of shared rules exist once. See `chrome-shared/README.md`. |
| `js/platform.js` | Reads which kit is loaded off `<html data-platform>`, and turns macOS's glyph-run shortcut notation (`⇧⌘Z`) into Windows/Linux spelling (`Ctrl+Shift+Z`) via `accel()`, plus `cmdKey`/`cmdAltKey`/`cmdOptKey` for handler-side modifier tests. |
| `js/menubar.js` | The in-window menu bar Windows needs (macOS gets a real `NSMenu` for free). Data-driven from a menu-spec table your backend serves — see "Wiring it up" below — never a second copy of your commands. |

All three (real) kits dress the **same DOM** and declare the **same token names** — an app's own
stylesheet needs no platform branching, just `<html data-platform="mac|win|linux">` set before
first paint and exactly one kit's stylesheets loaded off that.

## Using it

```html
<!doctype html>
<html data-platform="mac">   <!-- stamp this from an inline <head> script, before first paint -->
<head>
  <!-- pick ONE pair, matching data-platform -->
  <link rel="stylesheet" href="macos-kit/mac-tokens.css">
  <link rel="stylesheet" href="macos-kit/mac-chrome.css">
  <link rel="stylesheet" href="app.css">   <!-- your own stylesheet, loaded after the kit -->
</head>
<body>
  ...
  <script src="js/platform.js"></script>   <!-- first: everything else reads window.PLATFORM/accel -->
  ...
  <script src="js/menubar.js"></script>    <!-- last of the pair: needs menuState()/syncMenu, defined by your app -->
</body>
</html>
```

`js/menubar.js` is inert on macOS (every entry point returns immediately when the platform isn't
Windows) and needs three things from your app to come alive on Windows:

- a bridge call returning a menu-spec table (`{menus: [{title, items: [{title, js, vis, check,
  accel, sep, submenu}, …]}, …]}`) — SUD Workbench's `Api.menu_spec()` is the reference shape;
- a `menuState()` function returning the flags `vis`/`check` rules key off, and a `syncMenu()`
  function this module wraps to hear every state change;
- `window.pywebview.api.recent_files()` / `.new_window()` / `.caption()`, if your app uses the
  matching menu rows (Open Recent, New Window, the caption buttons).

`macos-kit/mac-tokens.css` renders eight of its icon masks from real SF Symbols at packaging time —
see `macos-kit/README.md` and this repo's `THIRD-PARTY-NOTICES.md` before shipping a build that
includes `macos-kit/` on a non-Apple platform.

## Licence

MIT (see `LICENSE`) for the original code and CSS. Several icon masks are redrawn from third-party
sources under their own licences — see `THIRD-PARTY-NOTICES.md`, in particular the note on SF
Symbols and Apple-platform-only distribution.
