# ISO Customization Toolkit — Usage Guide

A step-by-step walkthrough for every feature. If you're new, start with [Quick Start](#quick-start).

---

## Quick Start (Your First ISO Mod)

```
1. Run PowerShell as Administrator
2. .\isoToolkit.ps1
3. Select "1. Source" -> pick your Windows ISO -> choose an edition
4. Make changes (Integrate / Remove / Customize)
5. "5. Save changes & unmount"
6. "6. Build ISO" -> name it -> done
```

The toolkit auto-backs up your original ISO to `ISOBackup\original_backup.iso`. You can always revert.

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
                ┌─────────────────┐
                │ 5. SAVE CHANGES │  Commit to WIM + unmount
                └────────┬────────┘
                         ▼
                ┌─────────────────┐
                │  6. BUILD ISO   │  Create bootable .iso
                └─────────────────┘

  At any point: 7. Tools -> WIM operations, health check, repair, WinRE recovery
```

**Key rules:**
- **Source must be loaded first** (option 1) before anything else
- **Integrate / Remove / Customize** can be done in any order
- **Save** writes changes and unmounts the WIM
- **Build** creates the final ISO (auto-saves if not already done)
- **Tools** works on loaded WIM files or the mounted image depending on the tool

---

## Detailed Walkthroughs

### Scenario A: Create a Debloated ISO with Custom Wallpaper

```
1. Source -> load your Windows 11 ISO -> pick "Windows 11 Pro"
2. Remove -> "2. Remove with preset list" -> point to a .txt file of package names
3. Customize -> "1. Desktop wallpaper" -> pick your .jpg
4. Customize -> "6. OEM branding" -> pick logo + enter org name
5. Save changes & unmount
6. Build ISO -> name it "Win11Custom" -> done
```

### Scenario B: Integrate Drivers Into an ISO

```
1. Source -> load ISO -> pick edition
2. Integrate -> "2. Device Drivers" -> browse folder with .inf drivers
   OR
   Integrate -> "3. Export current system drivers" -> auto-exports from your PC
3. Save changes & unmount
4. Build ISO
```

### Scenario C: Repair a Corrupted/Stripped ISO Using a Donor

```
1. Source -> load the damaged ISO
2. Tools -> "9. Repair with donor ISO" -> browse clean donor ISO
   This runs DISM RestoreHealth using the donor as a repair source
3. Tools -> "8. Health check & repair" -> verify it's fixed
4. Save changes & unmount
5. Build ISO
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
3. Save changes & unmount
4. Build ISO
```

### Scenario E: Recover WinRE After Aggressive Debloating

```
1. Source -> load the stripped ISO
2. Tools -> "11. Recover WinRE from donor ISO" -> browse donor ISO
   (Must be same Windows version/edition)
   This copies Winre.wim, ReAgent.xml, and re-enables registry keys.
3. Save changes & unmount
4. Build ISO
```

### Scenario F: Import Browser Data Into the ISO

```
1. Source -> load your ISO -> pick edition
2. Customize -> "11. Import browser data"
   Detects installed browsers (Chrome, Edge, Brave, Firefox, Opera, Vivaldi)
   Select which browser -> cookies, bookmarks, logins, history copied into ISO
   New users will find their browser data pre-loaded after install
3. Save changes & unmount
4. Build ISO
```

### Scenario G: Swap Visuals From One ISO to Another

```
1. Source -> load your target ISO
2. Customize -> "7. Look swapper" -> browse donor ISO
   Copies wallpapers, themes, cursors, sounds, user pics from donor
   Optional: DLL patching (icons, login visuals) — ONLY if same Windows version
3. Save changes & unmount
4. Build ISO
```

---

## All Menus Reference

### Source (Option 1)
Loads the ISO, extracts it, detects Windows version, and mounts the WIM for editing. Also creates a backup at `ISOBackup\original_backup.iso`. If a WIM was previously saved/unmounted, selecting Source again will remount it.

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

### Save (Option 5)
Commits all changes to the WIM and unmounts. Must be done before building. If you skip this and go straight to Build, it auto-saves first.

**If save fails:** A mounted image is left behind. Run this to clean up:
```
dism /unmount-image /mountdir:"C:\ISOToolkit\mount" /discard
```

### Build (Option 6)
Creates the final bootable ISO using oscdimg.exe. You'll be asked for an output filename (no extension). The ISO is saved next to the script. If oscdimg.exe isn't found, it auto-downloads from Microsoft's ADK.

### Tools (Option 7)

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
| Saving before building on non-WIM ISO (XP) | Tool says "Nothing to save" | Non-WIM ISOs don't use DISM — just build directly |
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

The toolkit uses `C:\ISOToolkit\` as its work directory. This folder can grow to 15-30 GB depending on the ISO size. To clean up:

```
# After you're done with all toolkit work:
Run Cleanup.bat from the project root folder

# Or manually:
rd /s /q C:\ISOToolkit
rd /s /q C:\WIDTemp
```

The tool auto-creates a `.toolkit_marker` file to track its own temp directories — on exit, it asks whether to clean up.

---

## Quick Reference Cheat Sheet

```
# Load ISO and jump straight to debloating
1 -> pick ISO -> pick edition -> 3 -> 1 -> Spacebar to select -> Enter

# Add your PC's drivers to an ISO
1 -> pick ISO -> pick edition -> 2 -> 3 -> 5 -> 6 -> name -> done

# Brand an ISO with your company logo
1 -> pick ISO -> pick edition -> 4 -> 6 -> pick logo -> type name -> 5 -> 6

# Make a Windows 11 ISO look like Windows 10
1 -> pick Win11 ISO -> pick edition -> 4 -> 7 -> pick Win10 donor ISO -> skip DLLs -> 5 -> 6

# Restore WSL2 and .NET 3.5 to a stripped Micro ISO
1 -> pick stripped ISO -> pick edition -> 7 -> 10 -> pick donor ISO -> toggle features -> Enter -> 5 -> 6

# Create a fully unattended install ISO
1 -> pick ISO -> pick edition -> 4 -> 10 -> enter account name -> 5 -> 6
```
