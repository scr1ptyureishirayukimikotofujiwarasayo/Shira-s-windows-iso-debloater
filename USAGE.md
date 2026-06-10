# ISO Customization Toolkit — Usage Guide

A step-by-step walkthrough for every feature. If you're new, start with [Quick Start](#quick-start).

---

## Quick Start (Your First ISO Mod)

```
1. Run PowerShell as Administrator
2. .\isoToolkit.ps1
3. Select "1. Source" -> pick your Windows ISO -> choose an edition (mounts automatically)
4. Make changes (Integrate / Remove / Customize)
5. "5. Build ISO" -> name it -> done
   (Build commits/unmounts the image for you first - there is no separate Save step)
```

The toolkit auto-backs up your original ISO to `ISOBackup\backup_<timestamp>.iso`. You can always revert.

> **Important:** verify your changes by **installing the ISO in a VM**, not by mounting the `.iso` file. Almost everything the toolkit changes lives *inside* `sources\install.wim` and only appears after Windows is installed.

---

## Understanding the Workflow

```
                    ┌─────────────┐
                    │ 1. SOURCE   │  Load ISO, pick edition, mount WIM
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                  ▼
  ┌──────────┐    ┌──────────────┐    ┌─────────────┐
  │ INTEGRATE│    │    REMOVE    │    │  CUSTOMIZE  │
  │ updates  │    │  AppX bloat  │    │ wallpaper   │
  │ drivers  │    │  (pick from  │    │ OEM brand   │
  │ .NET 3.5 │    │   checkbox)  │    │ autounattend│
  │ registry │    │              │    │ look swap   │
  │ features │    │              │    │ browser data│
  └────┬─────┘    └──────┬───────┘    └──────┬──────┘
       │                 │                   │
       └─────────────────┼───────────────────┘
                         ▼
                ┌─────────────────────────────┐
                │       5. BUILD ISO          │  Commits to WIM + unmounts,
                │  (auto-saves, then packages)│  then creates the bootable .iso
                └─────────────────────────────┘

  At any point: 6. Tools -> WIM operations, health check, repair, WinRE recovery
```

**Key rules:**
- **Source must be loaded first** (option 1) before anything else; it mounts the WIM automatically
- **Integrate / Remove / Customize** can be done in any order
- **Build ISO (option 5)** commits your changes to the WIM, unmounts, and packages the ISO — all in one step. There is no separate Save menu item.
- **Tools (option 6)** works on loaded WIM files or the mounted image depending on the tool
- **Exit (option 7)** warns you if an image is still mounted with unsaved changes

---

## Detailed Walkthroughs

### Scenario A: Create a Debloated ISO with Custom Wallpaper

```
1. Source -> load your Windows 11 ISO -> pick "Windows 11 Pro"
2. Remove -> "2. Remove with preset list" -> point to a .txt file of package names
3. Customize -> "1. Desktop wallpaper" -> pick your .jpg
4. Customize -> "6. OEM branding" -> pick logo + enter org name
5. Build ISO -> name it "Win11Custom" -> done   (commits + packages in one step)
```

### Scenario B: Integrate Drivers Into an ISO

```
1. Source -> load ISO -> pick edition
2. Integrate -> "2. Device Drivers" -> browse folder with .inf drivers
   OR
   Integrate -> "3. Export current system drivers" -> auto-exports from your PC
3. Build ISO   (auto-saves the mounted image, then packages it)
```

### Scenario C: Repair a Corrupted/Stripped ISO Using a Donor

```
1. Source -> load the damaged ISO
2. Tools -> "9. Repair with donor ISO" -> browse clean donor ISO
   This runs DISM RestoreHealth using the donor as a repair source
3. Tools -> "8. Health check & repair" -> verify it's fixed
4. Build ISO   (auto-saves the mounted image, then packages it)
```

### Scenario D: Restore Removed Features (e.g. .NET, WSL, Printing)

```
1. Source -> load the stripped ISO
2. Tools -> "10. Restore features from donor" -> browse donor ISO
   A checkbox menu shows all available features from the donor:
     - Arrow keys to navigate
     - Spacebar to toggle
     - Enter to confirm
   Pick the ones you want back.
3. Build ISO   (auto-saves the mounted image, then packages it)
```

### Scenario E: Recover WinRE After Aggressive Debloating

```
1. Source -> load the stripped ISO
2. Tools -> "11. Recover WinRE from donor ISO" -> browse donor ISO
   (Must be same Windows version/edition)
   This copies Winre.wim, ReAgent.xml, and re-enables registry keys.
3. Build ISO   (auto-saves the mounted image, then packages it)
```

### Scenario F: Import Browser Data Into the ISO

```
1. Source -> load your ISO -> pick edition
2. Customize -> "11. Import browser data"
   Detects installed browsers (Chrome, Edge, Brave, Firefox, Opera, Vivaldi)
   Select which browser -> cookies, bookmarks, logins, history copied into ISO
   New users will find their browser data pre-loaded after install
3. Build ISO   (auto-saves the mounted image, then packages it)
```

> Want the browser **installed** (not just its data)? Use Customize option 18 "Add browser" with a full offline installer — see Scenario H below.

### Scenario G: Swap Visuals From One ISO to Another

```
1. Source -> load your target ISO
2. Customize -> "7. Look swapper" -> browse donor ISO
   Copies wallpapers, themes, cursors, sounds, user pics from donor
   Optional: DLL patching (icons, login visuals) — ONLY if same Windows version
3. Build ISO   (auto-saves the mounted image, then packages it)
```

### Scenario H: Bundle a Browser So It's Installed on First Boot

```
1. Download the FULL OFFLINE browser installer first:
   - Firefox: https://www.mozilla.org/firefox/all/ -> "Firefox Setup x.x.x.exe" (~60 MB)
   - Chrome:  the "Standalone" / offline installer (ChromeStandaloneSetup64.exe)
   NOT the small "Firefox Installer.exe" / online stub (~500 KB) - it installs nothing.
2. Source -> load your ISO -> pick edition
3. Customize -> "18. Add browser" -> select the offline installer
   -> accept the suggested silent switches (Firefox: /S, MSI: /qn /norestart)
4. Build ISO   (auto-saves the mounted image, then packages it)
5. Install in a VM and finish first boot. The browser installs silently via
   SetupComplete.cmd. Check C:\ProgramData\Browsers\install.log on the PC if it
   doesn't appear (it records the exit code).
```

> **Why offline?** `SetupComplete.cmd` runs as `SYSTEM` before the first logon, often before networking is up. An online stub has nothing to download at that point, so it silently installs nothing. The full offline installer is self-contained and works headless. Option 18 warns you if you pick a tiny `.exe`.

---

## All Menus Reference

### Source (Option 1)
Loads the ISO, extracts it, detects Windows version, and mounts the WIM for editing. Also creates a backup at `ISOBackup\backup_<timestamp>.iso`. If a WIM was previously saved/unmounted, selecting Source again will remount it.

**What happens:**
1. You pick an ISO file
2. It's extracted to a work directory
3. You choose which edition index to work on
4. The WIM is mounted for editing
5. Windows version is auto-detected

### Integrate (Option 2) — requires mounted image

| # | Feature | How to use |
|---|---------|-----------|
| 1 | Windows Updates | Browse folder with .msu / .cab files |
| 2 | Device Drivers | Browse folder with .inf driver files |
| 3 | Export current system drivers | Auto-extracts your PC's 3rd-party drivers and injects them |
| 4 | .NET Framework 3.5 | Enabled from the ISO's own SxS store |
| 5 | Registry file (.reg) | Browse a .reg file — imports into mounted image's hives |
| 6 | Start Menu layout (.xml) | Browse a LayoutModification.xml |
| 7 | Enable Windows features | Type feature names comma-separated (e.g. `NetFx3, IIS-WebServer`) |
| 8 | Disable Windows features | Same format — permanently removes feature files from the ISO. **Requires donor ISO to restore.** |

### Remove (Option 3) — requires mounted image, Windows 8+

| # | Feature | How to use |
|---|---------|-----------|
| 1 | Interactive AppX removal | Checkbox-style picker. Arrow keys to move, Spacebar to toggle, Enter to apply |
| 2 | Remove with preset list | Browse a .txt file — one package name per line. Lines starting with `#` are ignored |

**How to create a removal list:** Run option 1 first to see what packages exist. Note down the ones you want to remove. Create a .txt file with one package name per line. Use wildcards like `Microsoft.Xbox*` to catch all Xbox packages.

### Customize (Option 4) — requires mounted image

| # | Feature | What it changes |
|---|---------|----------------|
| 1 | Desktop wallpaper | Replaces `img0.jpg` in `Windows\Web\Wallpaper\Windows\` |
| 2 | Lockscreen wallpaper | Sets `img100.jpg` in `Windows\Web\Screen\` |
| 3 | User account picture | Replaces all default user/guest avatar images |
| 4 | Sound scheme (.wav) | Copies .wav files into `Windows\Media\` |
| 5 | Custom theme (.theme) | Copies .theme file into `Windows\Resources\Themes\` |
| 6 | OEM branding | Sets logo PNG + registry values for manufacturer name |
| 7 | **Look swapper** | Copies wallpapers, themes, cursors, sounds, user pics, branding from a donor ISO. Optionally patches system DLLs for icons/login visuals |
| 8 | Inject files/scripts | Browse a file, then browse a folder inside the mounted ISO to place it |
| 9 | Custom files merge | Drop anything into a `Custom` folder next to the script — everything gets copied into the image root |
| 10 | Create autounattend.xml | Generates an unattended answer file with a local admin account and OOBE settings |
| 11 | **Import browser data** | Copies cookies, saved logins, bookmarks, history, autofill data from your host browser into the ISO's Default user profile |
| 12 | System Tweaks | Applies privacy/performance/UI registry tweaks to the offline image |
| 13 | Services Configuration | Sets service startup types (e.g. disable telemetry services) |
| 14 | Scheduled Tasks | Disables telemetry / data-collection scheduled tasks |
| 15 | Post-Setup Scripts | Adds a script that runs after install (`SetupComplete.cmd` / `RunOnce`) |
| 16 | Visual Transformation | Applies a bundled theme-pack preset (XP / Vista / Win7 styles) |
| 17 | **Themed Edition Builder** | Cross-version theming done safely: loose assets applied offline now; theme + theming tools (SecureUxTheme, OpenShell) applied at first boot. No DLL swaps. |
| 18 | **Add browser** | Bundles a browser installer and installs it silently at first boot. Use the **full offline** installer, not the online stub. Logs to `C:\ProgramData\Browsers\install.log`. See Scenario H. |

### Build ISO (Option 5)
Commits the mounted image to the WIM, unmounts it, then creates the final bootable ISO using `oscdimg.exe` — all in one step (there is no separate Save menu item). You'll be asked for an output filename (no extension); the ISO is saved next to the script unless you passed an output path. If `oscdimg.exe` isn't found, it auto-downloads from Microsoft's ADK.

**If a build/save ever fails and leaves a mounted image behind,** clean it up with:
```
dism /unmount-image /mountdir:"C:\ISOToolkit\mount" /discard
dism /cleanup-mountpoints
```
(or just run `cleanup.bat`, which does this for you).

### Tools (Option 6)

| # | Feature | Use case |
|---|---------|----------|
| 1 | WIM info | List all editions/indexes in a WIM |
| 2 | Export edition | Extract one edition to a new, smaller WIM |
| 3 | Rename index | Change "Windows 11 Pro" to "Win11 Custom" |
| 4 | Delete index | Remove unwanted editions from multi-edition WIM |
| 5 | Optimize WIM | Recompress with max compression — reduces file size |
| 6 | Split WIM | Split into .swm parts for FAT32 USB drives |
| 7 | ISO to USB | Copy a built ISO to a USB drive |
| 8 | Health check & repair | DISM CheckHealth -> RestoreHealth using backup ISO as source |
| 9 | Repair with donor ISO | Use a clean ISO as repair source for a damaged image |
| 10 | Restore features from donor | Interactive checkbox of available features to restore from donor |
| 11 | Recover WinRE from donor | Restore Recovery Environment files from a donor ISO |

---

## Common Mistakes & Fixes

| Mistake | What happens | Fix |
|---------|-------------|-----|
| Trying to customize before loading source | Menus say "Mount an image first" | Always start with option 1 (Source) |
| Checking for changes by mounting the output `.iso` | Browser/wallpaper/tweaks appear "missing" | Those live inside `install.wim`. Verify by installing in a VM, or mount `install.wim` with DISM |
| Bundling an online "stub" browser installer (option 18) | Nothing installs at first boot | Use the full **offline** installer; the stub needs internet during a SYSTEM-context stage |
| Building a non-WIM ISO (XP) | "Nothing to save" is normal | Non-WIM ISOs don't use DISM — Build just repackages directly |
| Disabling a feature with /Remove | Feature files deleted permanently | Use Tools -> Restore features from donor ISO to get them back |
| DLL patching between different Windows versions | System won't boot | Only patch DLLs if donor and target are the same major version. Otherwise skip DLL patching in the look swapper. |
| Forgetting to unmount the donor ISO | "Drive already in use" errors | The toolkit auto-cleans donor mounts. If stuck, check Disk Management for leftover mounted ISOs. |
| Save fails with "file in use" | Another program has the mount dir open | Close Explorer windows browsing the mount dir, run the cleanup command above, then try again |

---

## Tips & Best Practices

- **Always test in a VM first** — install your custom ISO in Hyper-V or VMware before deploying to real hardware
- **Source ISO on a fast SSD** — DISM operations are heavily disk-bound; an NVMe SSD cuts processing time in half
- **Use the Standard tier first** — start with `-SafeMode` debloating, then use the toolkit to further customize. This avoids over-stripping.
- **Keep your donor ISOs organized** — if you plan to restore features or WinRE, keep a clean copy of the same Windows version handy
- **Check ErrorLogs\** after any operation that reports failures — the log tells you exactly what went wrong
- **The Custom folder** (`.\rintechtoolkit\Custom\`) is a drag-and-drop shortcut. Anything placed there gets merged into the ISO root when you use Customize option 9
- **Browser passwords don't transfer** — Chromium browsers encrypt passwords with DPAPI (machine-specific). Use a password manager or export CSV for portable password transfer
- **oscdimg.exe auto-downloads** — you don't need to install the Windows ADK. The first build will download the necessary tools automatically
- **Press S to skip** — any long-running loop operation can be skipped by pressing S

---

## Work Directory & Cleanup

The toolkit uses a `Work\` folder next to the script if present, otherwise `C:\ISOToolkit\`, as its work directory. This can grow to 15-30 GB depending on the ISO size. The toolkit auto-removes its work directory on normal exit (tracked via a `.toolkit_marker` file), and discards any still-mounted image first.

For a full reset — useful before committing to git or after a crash — run **`cleanup.bat`** (in this folder). It self-elevates and:

- discards any leftover DISM/WIM mounts and runs `dism /cleanup-mountpoints`
- unloads orphaned offline registry hives (`zT_*`, `zWR_*`)
- deletes `toolkit_log.txt`, `ErrorLogs\`, `ISOBackup\`, `Work\`, `C:\ISOToolkit\`, and the downloaded `Oscdimg\` cache
- optionally removes built output ISOs in this folder (it asks first)

```
# Full reset:
cleanup.bat

# Or manually, just the work dir / a stuck mount:
dism /unmount-image /mountdir:"C:\ISOToolkit\mount" /discard
dism /cleanup-mountpoints
rd /s /q C:\ISOToolkit
```

Manually placed **source** ISOs are never removed.

---

## Quick Reference Cheat Sheet

(Main menu: 1 Source, 2 Integrate, 3 Remove, 4 Customize, 5 Build ISO, 6 Tools, 7 Exit.
 Build (5) auto-saves; there is no separate Save step.)

```
# Load ISO and jump straight to debloating
1 -> pick ISO -> pick edition -> 3 -> 1 -> Spacebar to select -> Enter

# Add your PC's drivers to an ISO
1 -> pick ISO -> pick edition -> 2 -> 3 -> back -> 5 -> name -> done

# Brand an ISO with your company logo
1 -> pick ISO -> pick edition -> 4 -> 6 -> pick logo -> type name -> back -> 5

# Make a Windows 11 ISO look like Windows 10 (first-boot themed edition)
1 -> pick Win11 ISO -> pick edition -> 4 -> 17 -> pick/point to theme pack -> back -> 5

# Bundle a browser to install on first boot
1 -> pick ISO -> pick edition -> 4 -> 18 -> pick OFFLINE installer -> /S -> back -> 5

# Restore WSL2 and .NET 3.5 to a stripped Micro ISO
1 -> pick stripped ISO -> pick edition -> 6 -> 10 -> pick donor ISO -> toggle features -> Enter -> 5

# Create a fully unattended install ISO
1 -> pick ISO -> pick edition -> 4 -> 10 -> enter account name -> back -> 5
```
