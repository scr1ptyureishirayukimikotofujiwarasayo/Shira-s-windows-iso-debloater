# ISO Customization Toolkit

A menu-driven PowerShell toolkit for servicing Windows ISOs — Windows 10/11, 8/8.1, 7, Vista, and even XP/2003 (extract/build only). Inspired by MSMG Toolkit, KernelOS, and Windows X-Lite.

> **New to the toolkit?** Read **[USAGE.md](USAGE.md)** for step-by-step walkthroughs, common scenarios, cheat sheets, and troubleshooting.

## Quick Start

1. **Run PowerShell as Administrator**
2. Execute `.\isoToolkit.ps1`
3. Select **Source** (menu option 1) → pick your Windows ISO
4. Choose the edition index to work on
5. Use the menus to integrate drivers, remove bloat, customize visuals
6. **Save changes** (menu option 5) → **Build ISO** (menu option 6)

---

## Supported Windows Versions

| Version | WIM Mount | Integrate | Remove AppX | Customize | Health Check | Build ISO | Notes |
|---------|-----------|-----------|-------------|-----------|--------------|-----------|-------|
| **10 / 11** | Full | Full | Full | Full | Full | Full | All features |
| **8 / 8.1** | Full | Full | Full | Full | Full | Full | All features |
| **7** | Full | Full | N/A | Full | N/A | Full | No AppX or Cleanup-Image |
| **Vista** | Limited | Limited | N/A | Full | N/A | Full | DISM support varies by update level |
| **XP / 2003** | N/A | N/A | N/A | N/A | N/A | Full | i386-based, no WIM; extract/build only |

**XP/2003 note:** XP ISOs use the i386 folder structure, not WIM. The toolkit detects this automatically and shows:  
`[!] XP/2003 ISO detected (i386-based, not WIM). DISM servicing is unavailable. You can still build the ISO.`  
For XP customization, use **nLite** alongside this toolkit for extraction and ISO rebuilding.

---

## Main Menu

```
1. Source — Load ISO, auto-backup, extract, mount WIM
2. Integrate — Add updates, drivers, .NET, registry, start layout, features
3. Remove — Uninstall AppX packages (interactive or from list)   [Win 8+]
4. Customize — Visuals, wallpaper, OEM, look swap, autounattend
5. Save changes & unmount — Commit and unmount the WIM
6. Build ISO — Create bootable ISO with oscdimg
7. Tools — WIM manager, USB, health check & repair
8. Exit
```

---

## Integrate Menu

| # | Option | What it does |
|---|--------|-------------|
| 1 | Windows Updates | Browse a folder of .msu/.cab files and integrate them |
| 2 | Device Drivers (from folder) | Browse a folder of .inf drivers |
| 3 | Export current system drivers | Extracts all 3rd-party drivers from your running Windows using `dism /online /Export-Driver` and injects them into the ISO |
| 4 | .NET Framework 3.5 | Enables .NET 3.5 from the ISO's SxS store |
| 5 | Registry file (.reg) | Imports a .reg file into the mounted image's registry |
| 6 | Start Menu layout (.xml) | Imports a LayoutModification.xml |
| 7 | Enable Windows features | Comma-separated feature names (e.g. NetFx3, IIS-WebServer) |
| 8 | Disable Windows features | Same format, removes features |

---

## Remove Menu

| # | Option | What it does |
|---|--------|-------------|
| 1 | Interactive AppX removal | Arrow keys to navigate, spacebar to toggle, Enter to confirm — full checkbox-style selector (Windows 8+ only) |
| 2 | Remove with preset list | Load a .txt file with one package name per line (Windows 8+ only) |

---

## Customize Menu

| # | Option | What it does |
|---|--------|-------------|
| 1 | Desktop wallpaper | Browse an image → replaces default wallpaper |
| 2 | Lockscreen wallpaper | Browse an image → sets lockscreen background |
| 3 | User account picture | Browse an image → replaces all default user pictures |
| 4 | Sound scheme | Browse a folder of .wav files → merges into Windows\Media |
| 5 | Custom theme | Browse a .theme file |
| 6 | OEM branding | Browse a logo image + enter OEM name |
| 7 | Look swapper | Browse a donor ISO → copies wallpapers, themes, sounds, cursors, branding, user pics, lock screen images. Optionally patches system DLL resources (icons, UI frames, login visuals). Works with ESD and WIM donor ISOs, any Windows version |
| 8 | Inject files/scripts | Browse files → choose destination folder inside the mounted ISO |
| 9 | Custom files merge | Puts everything from a `Custom` folder next to the script into the mounted image |
| 10 | Create autounattend.xml | Generate unattended answer file with local account |

---

## Tools Menu

| # | Option | What it does |
|---|--------|-------------|
| 1 | WIM info | Lists all editions/indexes |
| 2 | Export edition | Exports selected index to a new WIM |
| 3 | Rename index | Change edition display name |
| 4 | Delete index | Remove an edition from multi-edition WIM |
| 5 | Optimize WIM | Max-compression export with size comparison |
| 6 | Split WIM | Split into .swm parts for FAT32 USB |
| 7 | ISO to USB | Copy built ISO to USB drive |
| 8 | Health check & repair | DISM CheckHealth → RestoreHealth with backup ISO source (Windows 8+ only) |
| 9 | Repair with donor ISO | Browse a clean donor ISO → mount its WIM → use as repair source (Windows 8+ only) |
| 10 | Restore features from donor | Browse a donor ISO → lists all enabled features with descriptions → pick which ones to add back (ideal for fixing stripped ISOs like Micro 10/11 or Tiny10/11) |

---

## Key Features

- **Multi-version support** — Windows 10/11, 8/8.1, 7, Vista, XP/2003. Detected automatically on source load
- **Auto-backup** — Original ISO copied to `ISOBackup\original_backup.iso` before any modifications
- **ESD handling** — Look swapper and source loading auto-convert ESD to WIM
- **Browse dialogs** — All file/folder selections use native Windows file pickers, no manual path typing
- **Press S to skip** during long operations
- **Error logging** in `ErrorLogs\` folder
- **No dependencies** beyond DISM (built into Windows) — oscdimg.exe auto-downloads if needed
- **Progress indicators** — Every operation displays what it's doing so you know the toolkit isn't frozen

---

## Version-Specific Notes

| Version | Limitations |
|---------|-------------|
| **10/11** | None — all features available |
| **8/8.1** | None — all features available |
| **7** | No AppX package management. No `/Cleanup-Image` (health check). Some features from DISM may require the Windows ADK |
| **Vista** | DISM commands may fail depending on service pack level. Best used for ISO extraction, simple registry imports, and ISO rebuilding. For deeper customization, use the WAIK tools |
| **XP/2003** | i386-based — no WIM, no DISM. Extraction and ISO rebuilding work. Use **nLite** or **XP2ESD** for actual customization |

---

## Look Swapper: How It Works

The look swapper extracts a donor ISO, mounts its WIM read-only, then copies visual assets into your target image:

1. **Wallpapers, themes, cursors** — from `Windows\Web\`, `Windows\Resources\Themes\`, `Windows\Cursors\`
2. **WAV sounds** — from `Windows\Media\`
3. **User account pictures** — from `ProgramData\Microsoft\User Account Pictures\`
4. **DLL patching** (optional) — copies `imageres.dll`, `shell32.dll`, `explorerframe.dll`, `authui.dll`, `basebrd.dll` from donor to target. **Warning:** only do this if donor and target are the same Windows major version, or the system won't boot

The look swapper handles both `.wim` and `.esd` donor ISOs and gracefully skips any folders that don't exist.

---

## Tips

- Load your ISO first (Source menu) before doing anything else
- The status bar shows detected Windows version after source load
- Mount happens automatically when you load a WIM-based source
- You can Integrate → Remove → Customize in any order
- Save before building, or build will auto-save
- Check `ErrorLogs\` if anything goes wrong
- The `Custom` folder next to the script lets you drop in any files to merge into the image
- For XP ISOs, the toolkit acts as an extractor/rebuilder — pair it with nLite for actual modifications
- When swapping looks between different Windows versions, skip DLL patching to avoid boot failures
