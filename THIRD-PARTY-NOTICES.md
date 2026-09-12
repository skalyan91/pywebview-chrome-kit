# Third-party notices

This repository's own code and CSS are MIT-licensed (see `LICENSE`). The chrome kits redraw or
embed icon assets from third-party sources, listed below. This section is carried over verbatim
(paths adjusted) from `THIRD-PARTY-NOTICES.md` in [SUD Workbench](https://github.com/skalyan91/sud-workbench),
the app this kit was extracted from — see that repository's own notices file for the full context
of its other, unrelated third-party assets (data, fonts, backends).

## Chrome kits — `macos-kit/`, `win11-kit/`

An app embedding this kit ships (at most) two of the three real chrome kits and loads exactly one,
chosen at page load from `<html data-platform>`. Their icon sets are unrelated in provenance.

| Component | Where | Upstream | Licence |
|---|---|---|---|
| Fluent UI System Icons | `win11-kit/fluent-tokens.css` — 38 of 40 `--sf-*` masks | [microsoft/fluentui-system-icons](https://github.com/microsoft/fluentui-system-icons) @ `a9e7f2d7bd8a` | MIT |
| WinUI 3 theme resources | `win11-kit/*.css` — colours, radii, metrics, timings | [microsoft/microsoft-ui-xaml](https://github.com/microsoft/microsoft-ui-xaml) | MIT |
| Lucide | `macos-kit/mac-tokens.css` and `win11-kit/fluent-tokens.css` — the hand-drawn `--sf-*` masks | [lucide-icons/lucide](https://github.com/lucide-icons/lucide) | ISC |
| SF Symbols | `macos-kit/mac-tokens.css` — 12 `--sf-*` masks, base64 PNG | Apple | see below |

- Fluent UI System Icons and the WinUI theme resources are both Copyright (c) Microsoft
  Corporation, MIT. From `microsoft-ui-xaml` **no code is copied — values only**, read out of
  `Common_themeresources_any.xaml`, `CornerRadius_themeresources.xaml`,
  `MenuFlyout_themeresources.xaml`, `ScrollBar_themeresources.xaml`,
  `TextBlock_themeresources.xaml`, `TitleBar/TitleBar_themeresources.xaml` and
  `Materials/Acrylic/AcrylicBrush.{h,_themeresources.xaml}`.
- Lucide glyphs are inlined as SVG path data, each named in a trailing comment at its own token.
  `--sf-narcs` and `--sf-nbrackets` are Lucide in **both** kits: they draw a notation, not an OS
  affordance, so Fluent has no counterpart to swap in.

**SF Symbols are Apple's, and are macOS-only on purpose.** Eight masks in `mac-tokens.css`
(`--sf-undo/-redo/-zoomin/-zoomout/-actualsize/-help/-grid/-open`) are real SF Symbols. Nothing here
bakes a rendered copy into the repository: `mac-tokens.css` only `@import`s a git-ignored,
packaging-time-generated stylesheet, meant to be produced by a script in the **consuming app**
(SUD Workbench's is `app/mac/sf_symbols.py`, run by `packaging/render_sf_symbols.py`) that calls
`NSImage.imageWithSystemSymbolName_accessibilityDescription_` on the user's own machine and never
redistributes a rendered copy.

Apple licenses SF Symbols for use in apps **on Apple platforms**; reproducing the artwork inside a
Windows or Linux build is not covered. Any packaging pipeline that assembles a Windows or Linux
build from a tree including this kit **must strip `macos-kit/` from that payload** — see SUD
Workbench's `packaging/windows/make_win_app.py` and `packaging/linux/make_deb.sh`/`make_rpm.sh` for
a reference implementation that fails the build if it survives. The Fluent kit supplies all 41 mask
names from MIT-licensed sources for Windows, so nothing is lost there. A Linux kit needing the same
41 mask names without depending on `macos-kit/` should build on `chrome-shared/` instead (see
`adwaita-kit/` and `chrome-shared/README.md`), which carries everything `macos-kit/`'s stylesheets
declare except the eight real SF Symbols, replaced with the same MIT-licensed Fluent equivalents.
