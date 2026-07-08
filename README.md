# Hikari-Cursor — Windows mouse cursors on macOS

**English** | [简体中文](README.zh-CN.md) | [日本語](README.ja.md)

Bring Windows `.cur` / `.ani` cursor themes to macOS. Animated cursors are supported, and the change is system-wide (every app, including browsers / Electron). Developed and verified on macOS 26 (Tahoe).

## Install (for users)

Download the latest `Hikari-Cursor.dmg` from [Releases](../../releases), open it, and drag the **Hikari-Cursor** icon into the **Applications** folder.

The first launch is blocked by macOS (the app is signed locally, not notarized by Apple). Allow it either way:

- **Recommended**: in Finder → Applications, **right-click Hikari-Cursor → Open**, then click "Open" in the dialog; or go to System Settings → Privacy & Security and click "Open Anyway" near the bottom.
- Or run once in Terminal: `xattr -dr com.apple.quarantine /Applications/Hikari-Cursor.app`

Requires macOS 15 or later (developed and verified on macOS 26).

## Build from source

Needs the Xcode / Swift 6 toolchain. Run `./build_app.sh`; artifacts land in `dist/`:

| Artifact | Description |
|---|---|
| `Hikari-Cursor.app` | GUI + menu-bar app: pick a theme folder → preview → assign mappings → apply / restore in one click |
| `mousecur` | CLI with the same features, plus auto-apply at login (the CLI keeps its original name, independent of the GUI brand) |

## Manual mapping

After picking a theme folder, every row has a dropdown on the right for choosing which macOS cursor that file should replace (each option shows a macOS cursor preview icon, grouped into "Common / Resize / Window edges·corners / Other"). By default the mapping is auto-detected from the filename (shown as "Auto · xxx"), but you can also:

- Reassign to any macOS cursor — even files auto-detected as "no match" (handwriting, person select, etc.) can be forced onto a slot;
- Choose "Don't map" to skip a file.

Helpers: files with the same name (e.g. packs that ship one `busy.ani` per color variant) are labeled with their subfolder to tell them apart; when several checked files map to the same macOS cursor, an orange warning appears on those rows (only the last one applied actually takes effect). Manual mappings are saved with "Apply" and restored on next launch.

## Menu-bar resident

`Hikari-Cursor.app` is a pure menu-bar app (`LSUIElement`, no Dock icon). Closing the main window does not quit it — there is always an arrow icon in the top-right menu bar. Clicking it lets you:

- Open Hikari-Cursor… (bring the main window back)
- Reapply last theme / Restore system default (no need to open the window)
- Auto-apply at login (checkbox, reflects whether the LaunchAgent is installed)
- Quit Hikari-Cursor (terminates the process; the menu-bar icon disappears)

## CLI quick start

```bash
# Apply a whole theme (roles auto-detected from filenames and mapped to macOS slots)
./dist/mousecur apply <theme-folder>

# Restore the macOS default cursors
./dist/mousecur reset

# Cursor size (pt, default 32)
./dist/mousecur apply <theme-folder> --size 40

# Apply a single file to a specific slot
./dist/mousecur apply arrow.cur --slot arrow

# Cursor changes are lost after logout; install auto-reapply at login
./dist/mousecur agent install

# Remove auto-reapply at login
./dist/mousecur agent uninstall

# Others
./dist/mousecur info <file>       # frame count / size / hotspot
./dist/mousecur preview <file>    # export frames as PNG
./dist/mousecur slots             # list replaceable system cursor slots
./dist/mousecur doctor            # check availability of private system APIs
```

## Supported file formats

- Static `.cur` (BMP 1/4/8/24/32-bit + AND mask, or embedded PNG)
- Animated `.ani` (RIFF/ACON, with `rate` / `seq` chunks and 8-bit palette frames)
- **ANI disguised as `.cur`** (some packs name RIFF animations `.cur`; detected by content)

## Windows role → macOS slot mapping

| Windows file | macOS cursor |
|---|---|
| Normal | Arrow (incl. Tahoe's ArrowS, browser cursor.0) |
| Text | I-beam (incl. IBeamS, cursor.1) |
| Link | Pointing hand (cursor.13 / PointingHand) |
| Busy | Wait spinner (Wait) |
| Working | Busy-but-clickable (cursor.4) |
| Unavailable | Forbidden (cursor.3 / NotAllowed) |
| Precision | Crosshair |
| Help | Help |
| Move | Move |
| Vertical / Horizontal / Diagonal | Directional resize + window edges/corners |
| Handwriting, Person, Pin, Alternate, etc. | No macOS equivalent, skipped |

## Notes

- Cursor registration only lasts for the current login session and reverts after logout/restart; use `mousecur agent install` to make it persist.
- If a few cursors are not restored immediately after "Restore default", just log out and back in (registration never crossed sessions to begin with).
- Uses private system APIs. If it breaks after a major macOS upgrade, run `mousecur doctor` first.
- The binary is ad-hoc signed for personal use; redistribution requires re-signing.

## License

[MIT](LICENSE) © 2026 zhy072
