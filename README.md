# Windows ISO Toolkit (All-in-One)

Three tools in one repo for debloating, customizing, and repairing Windows ISOs — before AND after installation.

---

## Toolkit Ecosystem

| Tool | Type | What It Does | When to Use |
|------|------|-------------|-------------|
| **isoDebloaterScript.ps1** | ISO debloater | Strips bloat from a Windows ISO before installation. Removes AppX packages, features, Edge, AI, OneDrive, services, telemetry, and builds a debloated bootable ISO. | **Before installing Windows** — create a clean ISO to install from scratch. |
| **rintechtoolkit\\isoToolkit.ps1** | ISO modding / repair toolkit | Menu-driven toolkit for integrating updates, drivers, .NET, registry tweaks, OEM branding, wallpapers, autounattend.xml, WIM operations, and repairing/restoring features and WinRE from a donor ISO. | **After debloating** — fine-tune the ISO with customizations, restore removed components, or repair corrupted images. |
| **BakuretsuClean\\** | Live OS debloater | Debloats an already-installed Windows system. Removes AppX packages, disables services, blocks telemetry, applies privacy tweaks. Includes revert support. | **After installing Windows** — debloat a live system without touching the ISO. |

### Recommended Workflow

```
1. isoDebloaterScript.ps1  →  Strip bloat from stock ISO, build debloated ISO
2. rintechtoolkit\          →  Customize/repair the debloated ISO (drivers, wallpaper, WinRE recovery, etc.)
3. Install the debloated ISO →  Test in VM or bare metal
4. BakuretsuClean\          →  (Optional) Apply additional live debloat after installation
5. Cleanup.bat              →  Clear all temp files, logs, backups, and output ISOs for a fresh start
```

---

## Quick Start

**ISO Debloater:**
1. **Run PowerShell as Administrator**
2. Execute: `.\isoDebloaterScript.ps1`
3. Select your Windows ISO when prompted
4. Answer the debloat options (press Enter for defaults)
5. Wait for the debloated ISO to be created

**ISO Toolkit (modding / repair):**
1. Run: `.\rintechtoolkit\isoToolkit.ps1`
2. Select `1. Source` → load an ISO
3. Use `Integrate`, `Customize`, `Tools` menus to modify or repair the image
4. `5. Save changes & unmount` → `6. Build ISO`

**Live OS Debloater:**
1. Run: `BakuretsuClean\RunDebloater.bat` as Administrator

**Cleanup:** Run `Cleanup.bat` to wipe all work files, logs, temp dirs, backup ISOs, and output ISOs.

**Minimum requirements:** Windows 10/11 host, 20GB free disk space, Administrator privileges.

---

## Cleanup

After one or more debloat/modding sessions, leftover files accumulate:

| Leftover | Source |
|----------|--------|
| `C:\WIDTemp\` | isoDebloaterScript.ps1 work directory |
| `C:\ISOToolkit\` | isoToolkit.ps1 work directory |
| `ISOBackup\` | Original ISO backups (~6 GB each) |
| `ErrorLogs\` | Timestamped error logs from all tools |
| `ADKDownload\` | Cached oscdimg / ADK downloads |
| `*.iso` (output ISOs) | Debloated/custom output ISOs |
| `*.log`, `*.txt` | Script/transcript logs |
| `%TEMP%\toolkit_*.reg` | Temp registry import files |

Run **`Cleanup.bat`** (as Administrator) from the toolkit folder to clear everything. It prompts before removing each ISO so you can keep ones you want. After cleanup, the toolkit folder returns to its initial state — ready for a fresh run.

---

## What Happens During Execution

| Step | Description | Time |
|------|-------------|------|
| ISO selection & backup | Copies original ISO to `ISOBackup\` folder | ~1-2 min |
| Mount & extract | Copies ISO contents to temp working directory | ~2-5 min |
| Package removal | Removes 85+ AppX packages via DISM | ~3-8 min |
| Edge removal | Strips Edge browser, WebView, EdgeUpdate | ~1-3 min |
| AI removal | Removes Copilot, Recall, AI components | ~1-3 min |
| Registry tweaks | Privacy, performance, telemetry policies | ~1-2 min |
| Service disabling | 45+ unnecessary services disabled | <1 min |
| Widgets removal | Strips Widgets, News, WebExperience | ~30 sec |
| Hyper-V removal (optional) | Removes virtualization platform | ~1-2 min |
| Defender disable (optional) | Registry-based Defender policies | ~30 sec |
| Scheduled tasks | Disables 45+ telemetry/bloat tasks | ~1 min |
| WinSxS cleanup | Conservative bloat component cleanup | ~2-5 min |
| Privacy tweaks | Cortana, location, activity, ads policies | <1 min |
| Image cleanup | DISM component cleanup & compression | ~10-20 min |
| Health check & repair | Checks & repairs component store | ~2-5 min |
| Export WIM | Compresses modified image | ~5-20 min |
| ISO creation | Builds bootable ISO with oscdimg | ~2-5 min |
| **Total (typical)** | | **~25-50 min** |
| **Total (with ESD compress)** | | **~40-80 min** |

---

## All Options Explained

### `Remove unnecessary packages?` (AppxRemove)
Removes 85+ provisioned AppX packages from the ISO including: Bing, Xbox, Skype, Teams, OneNote, Clipchamp, 3D Viewer, Maps, Solitaire, Mixed Reality, People, Camera, Photos, Paint3D, various codec extensions, and more. This is the bulk of bloat removal.

### `Remove unnecessary features?` (CapabilitiesRemove)
Removes 25+ optional Windows features: Internet Explorer, WordPad, PowerShell ISE, Media Player, Steps Recorder, Quick Assist, handwriting/OCR/speech recognition, Fax & Scan, Hello Face, XPS Viewer, Print 3D, OpenSSH Client, and more.

### `Remove OneDrive?` (OnedriveRemove)
Strips OneDrive from SysWOW64, removes shortcuts, cleans up WinSxS components, and removes OneDrive appdata folders. Registry policies prevent reinstallation.

### `Remove Microsoft Edge?` (EDGERemove)
Runs DISM Remove-Edge, removes Edge AppX packages (Stable, DevTools, WebView), cleans Edge registry keys (WOW6432Node, EdgeUpdate services), removes Edge Program Files, scheduled tasks, and WinSxS components. Registry policies block Edge reinstallation.

### `Remove AI Components?` (AIRemove)
Removes Copilot, Recall, AIX, CoreAI, GameAssist, WritingAssistant, and OfficeActions packages. Applies 30+ registry policies to disable AI in Search, Edge, Paint, Notepad, Office, and system-wide. Unhides and removes hidden CBS AI packages. Sets post-install RunOnce commands to disable the WSAIFabricSvc service.

### `Bypass TPM check?` (TPMBypass)
Sets LabConfig registry keys to bypass TPM 2.0, Secure Boot, CPU, RAM, Storage, and Disk checks. Modifies boot.wim for the same bypasses. Replaces appraiserres.dll with empty file. Hides unsupported hardware watermark.

### `Enable user folders?` (UserFoldersEnable)
Restores Desktop, Documents, Downloads, Music, Pictures, and Videos shortcuts in File Explorer on Windows 11.

### `Integrate Intel RST/VMD drivers?` (DriverIntegrate)
Downloads and integrates Intel RAID/VMD drivers into both install.wim and boot.wim. Useful for laptops with Intel VMD storage controllers.

### `Compress the ISO?` (ESDConvert)
Uses recovery (ESD) compression instead of standard WIM compression. Reduces ISO size but takes significantly longer (15-45 min extra).

### `Use Oscdimg for ISO creation?` (useOscdimg)
Uses Microsoft's oscdimg.exe for reliable bootable ISO creation. Automatically downloads if not found. Alternative is the experimental IMAPI2FS COM method.

### `Remove Windows Defender/Security?` (DefenderRemove)
**EXPERIMENTAL — safe mode only.** Disables Defender services (WinDefend, WdNisSvc, SecurityHealthService, etc.), applies registry policies to block real-time protection and SmartScreen, sets post-install RunOnce commands. Does NOT delete files from WinSxS (would corrupt installer). Disables SmartScreen and AppHost web content evaluation.

### `Remove Widgets completely?` (WidgetsRemove)
Removes Widgets and WebExperience AppX packages, disables News & Interests via policy, removes Widgets scheduled tasks, cleans WinSxS components.

### `Disable Windows Update?` (WinUpdateDisable)
Disables wuauserv, WaaSMedicSvc, and UsoSvc services. Sets group policies to block automatic updates, driver updates, and WU internet access.

### `Remove Hyper-V components?` (HyperVRemove)
Disables Microsoft-Hyper-V-All via DISM, disables all Hyper-V services (HvHost, vmic*), and removes Hyper-V Windows packages.

### `Enable Extreme Debloat mode?` (ExtremeDebloat)
Enables deeper service disabling including: Print Spooler, Bluetooth, LAN Server/Workstation, SSDP/UPnP, Smart Card, Fax, SMS Router. Use only if you know you won't need these services.

### `Disable unnecessary services?` (ServicesDisable)
Disables 45+ services: DiagTrack, WSearch, SysMain, Xbox Live, Wallet, Maps, Phone, OneSync, PrintNotify, WaaSMedicSvc, TabletInput, FontCache, RetailDemo, Windows Error Reporting, and more.

### `Apply performance tweaks?` (PerformanceTweaks)
Disables visual effects, animations, hibernation. Optimizes CPU scheduling, memory management, prefetch/Superfetch. Disables network throttling, ICMP redirects, Game Mode, GameDVR.

---

## Health Check & Repair

After all modifications but before ISO creation, the script runs a comprehensive health check:

1. **Component store scan** — `dism /CheckHealth` on the mounted image. Parses DISM output to detect corruption.
2. **Automatic repair** — If issues found, runs `dism /RestoreHealth` using:
   - **Backup ISO** as source (if available — the script creates a backup at `ISOBackup\original_backup.iso`)
   - Falls back to Windows Update / built-in repair if no backup
   - Shows explicit BEFORE/AFTER status: `[REPAIRED]` or `[STILL DAMAGED]`
3. **Critical file verification** — Checks 12 critical files exist (explorer.exe, registry hives, boot files, setup.exe)
4. **Registry hive validation** — Loads each hive to verify they're not corrupted
5. **Health summary** — Reports passed checks vs issues found

---

## Error Logging

Every run creates a timestamped folder in `ErrorLogs\`:
- `errors_YYYY-MM-DD_HH-mm-ss.log` — All errors, warnings, and skips
- `summary_YYYY-MM-DD_HH-mm-ss.txt` — Formatted summary with counts

Errors are categorized by source (DISM, AppX, Mount, Export, Health, etc.) for easy diagnosis.

---

## Command-Line Automation

```powershell
# Fully automated run
.\isoDebloaterScript.ps1 -noPrompt -isoPath "C:\Win11.iso" -winEdition "Windows 11 Pro" -outputISO "Win11Lite"

# Customize what to remove
.\isoDebloaterScript.ps1 -AppxRemove yes -CapabilitiesRemove yes -OnedriveRemove yes -EDGERemove yes -AIRemove yes

# Extreme debloat with TPM bypass and ESD compression
.\isoDebloaterScript.ps1 -ExtremeDebloat yes -TPMBypass yes -ESDConvert yes -WinUpdateDisable yes

# Safe mode - remove only apps, keep everything else
.\isoDebloaterScript.ps1 -AppxRemove yes -CapabilitiesRemove no -OnedriveRemove no -EDGERemove no -AIRemove no -DefenderRemove no

# LTSC ISO - skip removals, apply only universal optimizations
.\isoDebloaterScript.ps1 -AppxRemove no -CapabilitiesRemove no -OnedriveRemove no -EDGERemove no -AIRemove no -WidgetsRemove no -DefenderRemove no -ServicesDisable yes -PerformanceTweaks yes -TPMBypass yes -ESDConvert yes
```

### All Parameters

| Parameter | Values | Default |
|-----------|--------|---------|
| `-noPrompt` | (switch) | off |
| `-isoPath` | path string | prompted |
| `-winEdition` | edition name | prompted |
| `-outputISO` | filename | prompted |
| `-useDISM` | yes/no | yes |
| `-AppxRemove` | yes/no | yes |
| `-CapabilitiesRemove` | yes/no | yes |
| `-OnedriveRemove` | yes/no | yes |
| `-EDGERemove` | yes/no | yes |
| `-AIRemove` | yes/no | yes |
| `-TPMBypass` | yes/no | no |
| `-UserFoldersEnable` | yes/no | yes |
| `-DriverIntegrate` | yes/no | no |
| `-ESDConvert` | yes/no | no |
| `-useOscdimg` | yes/no | yes |
| `-DefenderRemove` | yes/no | no |
| `-WidgetsRemove` | yes/no | yes |
| `-WinUpdateDisable` | yes/no | no |
| `-HyperVRemove` | yes/no | no |
| `-ExtremeDebloat` | yes/no | no |
| `-MicroMode` | yes/no | no |
| `-ServicesDisable` | yes/no | yes |
| `-PerformanceTweaks` | yes/no | yes |
| `-TaskCleanup` | yes/no | no |
| `-WinSxSCleanup` | yes/no | no |
| `-UseAutounattend` | yes/no | no |

---

## Skip / Stuck Recovery

- **Press `S`** during any looping operation (package removal, service disabling, scheduled tasks, WinSxS cleanup) to skip the remaining items in that section.
- For DISM operations, the script has a 2-retry timeout mechanism. If a DISM command hangs, it auto-skips after the timeout.
- If the script crashes mid-run leaving a mounted WIM: run `dism /unmount-image /mountdir:"C:\WIDTemp\mountdir\installWIM" /discard` before re-running.

---

## Tips

- **LTSC ISOs are already debloated** — errors during debloating are normal and harmless. Skip most removal options (AppX, features, Edge, AI, Widgets, OneDrive, tasks, WinSxS) and only enable TPM bypass, driver integration, folder shortcuts, compression, and performance tweaks.
- **For Home/Pro systems**, install the stock ISO and use **BakuretsuClean** (included) to debloat the live OS instead.
- **Use a fast SSD** for the working directory — DISM operations are disk-intensive.
- **Disable real-time AV** temporarily — some AV products interfere with DISM mounting operations.
- **Don't use Defender removal** unless you're comfortable with potential security implications and accept it's experimental.
- **The backup ISO** at `ISOBackup\original_backup.iso` can be deleted after confirming the debloated ISO works.
- **Check the error logs** at `ErrorLogs\` if the debloated ISO has installation issues.
- **If installation fails with a boot loop**, try re-running without `DefenderRemove` and `ExtremeDebloat`.

---

## Tested Versions

- **Windows 10**: 22H2 (Build 19045)
- **Windows 11**: 24H2 (Build 26100)

---

## Credits

- Original script by [itsNileshHere](https://github.com/itsNileshHere/Windows-ISO-Debloater)
- Enhanced by shirayukimikoto
- Inspired by KernelOS and Windows X-Lite stripped builds
- [tiny11builder](https://github.com/ntdevlabs/tiny11builder) for inspiration
- [Winaero](https://winaero.com/) for registry optimization techniques
- [RemoveWindowsAI](https://github.com/zoicware/RemoveWindowsAI) for AI removal approach

## License

GPL-3.0 — Use at your own risk. Always test debloated ISOs in a VM before deploying.
