# Windows ISO Debloater (Enhanced)

An advanced PowerShell script that strips Windows ISOs to a level comparable to Windows X-Lite / KernelOS builds. Removes bloatware, disables telemetry, optimizes performance, and verifies ISO health — all before installation.

---

## Which ISO Should You Use?

| ISO Edition | Debloat Safety | Recommendation |
|------------|---------------|---------------|
| **Windows LTSC (IoT/Enterprise LTSC)** | Minimal impact | LTSC is already heavily debloated by Microsoft. Most removal options target components that don't exist in LTSC. See the LTSC section below for specific guidance. |
| **Windows Enterprise** | Safe | Good for moderate debloating. Fewer consumer integrations than Pro. |
| **Windows Pro / Pro for Workstations** | Moderate | Use conservative options. Skip `DefenderRemove`, `WinSxSCleanup`, and `TaskCleanup`. |
| **Windows Home** | Risky | Home edition has tight consumer integrations. Only use `AppxRemove` and basic tweaks. Skip everything else. |

### For Home/Pro ISOs — Debloat Live Instead

If you want an aggressive debloat on a Home or Pro system, **install the stock ISO first**, then use **BakuretsuClean** to debloat the running OS. This avoids corrupting the installer and gives you a stable installation with the same result.

```
BakuretsuClean — live-OS debloater (included in the repo)
Run: BakuretsuClean\RunDebloater.bat as Administrator
Features: AppX removal, service disabling, telemetry blocking, privacy tweaks, revert support
```

---

## Recommended Settings by ISO Type

### LTSC (IoT/Enterprise LTSC)

**Important:** LTSC ISOs are already debloated by Microsoft — they ship without Store apps, Widgets, Copilot, AI components, Xbox, Bing, and most consumer features. Running aggressive removal options on LTSC will produce errors (e.g. "package not found", "feature not present"). This is **normal and expected** — the script is trying to remove components that don't exist in LTSC.

**Errors on LTSC do NOT mean something is wrong with your ISO or the script.** They are harmless "not found" skips because LTSC never had those components.

#### Recommended for LTSC — skip these options entirely:
```
-AppxRemove no              # LTSC has almost no AppX bloat
-CapabilitiesRemove no      # LTSC already lacks most consumer features
-OnedriveRemove no          # OneDrive is not pre-installed on LTSC
-EDGERemove no              # Edge is optional / not always present
-AIRemove no                # Copilot, Recall, AI components don't exist on LTSC
-WidgetsRemove no           # Widgets are not included in LTSC
-DefenderRemove no          # Skip — Defender is one of the few things actually present
-TaskCleanup no             # Minimal telemetry tasks already
-WinSxSCleanup no           # LTSC is already lean
-ExtremeDebloat no          # Services it targets are often absent or already disabled
```

#### Options still useful on LTSC:
```
-TPMBypass          yes     # Bypass hardware checks if needed
-UserFoldersEnable  yes     # Restore folder shortcuts on Windows 11
-DriverIntegrate    yes     # Integrate Intel VMD drivers if needed
-ESDConvert         yes     # Compress the ISO
-useOscdimg         yes     # Reliable ISO creation
-PerformanceTweaks  yes     # CPU/memory/network optimizations (always applicable)
-ServicesDisable    yes     # Some services may be present; errors are normal
-WinUpdateDisable   no      # Optional — LTSC only gets security updates; disabling may leave you vulnerable
```

### Pro (play it safe) — same as BakuretsuClean's non-aggressive approach
```
-SafeMode
```
This removes AppX bloat only. Keeps Store, Calculator, Photos, Edge, Cortana, and system services intact. Safe for any edition.

Or manually:
```
-AppxRemove yes -CapabilitiesRemove yes -OnedriveRemove yes -EDGERemove yes
-AIRemove yes -ServicesDisable yes -PerformanceTweaks yes
-DefenderRemove no -WidgetsRemove no -WinUpdateDisable no
-ExtremeDebloat no -TaskCleanup no -WinSxSCleanup no
```

### Home (bare minimum)
```
-AppxRemove yes
-CapabilitiesRemove no -OnedriveRemove no -EDGERemove no
-AIRemove no -DefenderRemove no -ServicesDisable no
-TaskCleanup no -WinSxSCleanup no
Then: run BakuretsuClean after installation
```

---

## Debloating LTSC ISOs — What to Expect

LTSC (Long-Term Servicing Channel) editions are Microsoft's own debloated Windows builds. They ship without:
- Microsoft Store (and most Store-dependent apps)
- Xbox, Gaming, Mixed Reality, and consumer entertainment apps
- Copilot, Recall, and AI/cloud assistant components
- Widgets, News, WebExperience, and consumer information feeds
- Bing, Weather, Maps, and other search-dependent apps
- Teams, Skype, and consumer communication integrations
- Most telemetry and data collection tasks present in Home/Pro

### Why You'll See Errors

When you run the debloater on an LTSC ISO, many removal steps will report **"not found"** or **"failed"** because the script attempts to remove components that LTSC never included in the first place. These errors are:

- **Harmless** — the script continues past them
- **Normal** — expected behavior on already-lean ISOs
- **Logged** — check `ErrorLogs\` for details if you want to confirm

The final ISO will still be built successfully with whatever removals were possible.

### Best Practice for LTSC

Use the `-SafeMode` flag to only attempt benign removals, or run the script interactively and answer **"no"** to AppX removal, features removal, Edge, AI, Widgets, OneDrive, Defender, tasks, and WinSxS cleanup. Enable only the options that apply universally (TPM bypass, folder shortcuts, driver integration, compression, performance tweaks).

---

## Quick Start

1. **Run PowerShell as Administrator**
2. Execute: `.\isoDebloaterScript.ps1`
3. Select your Windows ISO when prompted
4. Answer the debloat options (press Enter for defaults)
5. Wait for the debloated ISO to be created

**Minimum requirements:** Windows 10/11 host, 20GB free disk space, Administrator privileges.

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
| `-ServicesDisable` | yes/no | yes |
| `-PerformanceTweaks` | yes/no | yes |

---

## Skip / Stuck Recovery

- **Press `S`** during any looping operation (package removal, service disabling, scheduled tasks, WinSxS cleanup) to skip the remaining items in that section.
- For DISM operations, the script has a 2-retry timeout mechanism. If a DISM command hangs, it auto-skips after the timeout.
- If the script crashes mid-run leaving a mounted WIM: run `dism /unmount-image /mountdir:"C:\WIDTemp\mountdir\installWIM" /discard` before re-running.

---

## Virtual Machine Testing & Troubleshooting Guide

> **IMPORTANT:** The issues described below are caused by **improper VM configuration** and the **natural side effects of aggressive component stripping** — they are **NOT defects in the script or your debloated ISO.** Your ISO is almost certainly valid. Before opening an issue, verify you are not hitting one of these known scenarios.

---

### 1. The Endless WinPE Pre-Install Loop (Hyper-V Generation 1)

**Symptom:** After the installation progress bar reaches 100% and the VM reboots, it drops back into the initial *"Install Now"* setup screen endlessly.

**Root Cause:** Hyper-V **Generation 1** virtual machines rely on IDE Controller emulation and blindly boot from the attached virtual CD/DVD ISO drive on every restart — ignoring the virtual hard disk where the OS was just installed. This is a Hyper-V Gen 1 design behavior, not an ISO corruption issue.

**The Fix:**
1. **Completely power off** the VM (do not just restart).
2. Open **VM Settings → DVD Drive** → change the **Media** type to **"None"** (or unmount the ISO entirely).
3. Apply changes and **start the VM cold**.
4. **Alternatively:** Move the **Virtual Hard Disk** to the top of the boot priority list in the VM's BIOS/firmware settings, OR simply do **not** press any key when the *"Press any key to boot from CD..."* prompt flashes during reboot.

---

### 2. The "Just a Moment" / OOBEREGION Loading Loop

**Symptom:** After a successful installation, the system hangs indefinitely on the blue *"Just a moment..."* screen, or throws a fatal **OOBEREGION** setup interface error.

**Root Cause:** This is actually a sign the debloater **worked perfectly.** Aggressive component stripping cleanly removes Microsoft's background consumer telemetry, web account integration, and regional tracking frameworks. The interactive OOBE wizard layer panics when its online service hooks are absent — it has **nothing to latch onto** for the default first-run experience. **This is not image corruption.**

**The Fix:**
1. Press **`Shift + F10`** (or **`Fn + Shift + F10`** on laptops) to open an administrative Command Prompt.
2. Force-create a local user account:
   ```
   net user Admin01 /add
   net localgroup Administrators Admin01 /add
   ```
3. Rewrite the setup boot flags to bypass the stuck OOBE wizard layer entirely:
   ```
   reg add "HKLM\SYSTEM\Setup" /v "OOBEInProgress" /t REG_DWORD /d 0 /f
   reg add "HKLM\SYSTEM\Setup" /v "SetupType" /t REG_DWORD /d 0 /f
   ```
4. Issue a hard restart from the toolbar or type:
   ```
   shutdown /r /t 0
   ```
5. Upon reboot, Windows will **skip the OOBE entirely** and log directly into the ultra-lean desktop shell as **`Admin01`** with full administrator privileges.

---

> **If you encountered either of these scenarios:** your debloated ISO is fine. These are known VM configuration quirks and expected outcomes of aggressive stripping — **please do not file a bug report for them.**

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
