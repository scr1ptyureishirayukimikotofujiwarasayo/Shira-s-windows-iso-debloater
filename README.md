# Windows ISO Toolkit (All-in-One)

Three tools in one repo for debloating, customizing, and repairing Windows ISOs — before AND after installation.

---

## Toolkit Ecosystem

| Tool | Type | What It Does | When to Use |
|------|------|-------------|-------------|
| **isoDebloater.ps1** | ISO debloater | Strips bloat from a Windows ISO before installation. Removes AppX packages, features, Edge, AI, OneDrive, services, telemetry, and builds a debloated bootable ISO. | **Before installing Windows** — create a clean ISO to install from scratch. |
| **rintechtoolkit\\isoToolkit.ps1** | ISO modding / repair toolkit | Menu-driven toolkit for integrating updates, drivers, .NET, registry tweaks, OEM branding, wallpapers, autounattend.xml, browser data import, WIM operations, WinRE recovery, and repairing/restoring features from a donor ISO. | **After debloating** — fine-tune the ISO with customizations, restore removed components, or repair corrupted images. |
| **BakuretsuClean\\** | Live OS debloater | Debloats an already-installed Windows system. Removes AppX packages, disables services, blocks telemetry, applies privacy tweaks. Includes revert support. | **After installing Windows** — debloat a live system without touching the ISO. |

### Recommended Workflow

```
1. isoDebloater.ps1  →  Strip bloat from stock ISO, build debloated ISO
2. rintechtoolkit\          →  Customize/repair the debloated ISO (drivers, wallpaper, WinRE recovery, etc.)
3. Install the debloated ISO →  Test in VM or bare metal
4. BakuretsuClean\          →  (Optional) Apply additional live debloat after installation
5. Cleanup.bat              →  Clear all temp files, logs, backups, and output ISOs for a fresh start
```

---

## Quick Start

**ISO Debloater:**
1. **Run PowerShell as Administrator**
2. Execute: `.\isoDebloater.ps1` (or double-click `RunDebloaterSafe.bat`)
3. Select your Windows ISO when prompted
4. Choose a debloat mode from the menu:
   - `[1] Safe` — AppX removal only, safe for any edition
   - `[2] Aggressive` — all recommended options enabled
   - `[3] Ultra Micro` — maximum stripping + extreme disk savings
   - `[4] Manual` — pick each option individually with review screen
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

## Debloat Modes

When you run `isoDebloater.ps1`, you're greeted with a 4-option menu:

| Option | Mode | What it activates |
|--------|------|-------------------|
| **1. Safe Debloat** | AppX bloatware only | Removes packages, features, OneDrive, Edge, AI, Widgets. Disables services, applies privacy + performance tweaks. **Safe for any edition.** |
| **2. Aggressive Debloat** | Safe + more | Everything in Safe, plus aggressive package targets (Print/Fax/Scan, VBScript, 32-bit support, Notepad, Paint, MediaPlayer). |
| **3. Ultra Micro Mode** | Maximum stripping | Everything in Aggressive + WinRE removal, font stripping, Windows Update disable, Defender removal, Hyper-V removal, WinSxS deep cleanup, OOBE bypass. **Extreme disk savings.** |
| **4. Manual** | Pick each option | Choose from 22 individual options. Includes back button, review screen, and toggle-any-option functionality. |

The modes are presets — selecting option 1-3 auto-sets all 22 flags to their recommended values. Option 4 lets you pick each one.

### Tier Details

### Tier 1 — Safe Debloat (menu option 1 or `-SafeMode`)

The baseline.

| Category | What's Removed |
|----------|---------------|
| **AppX packages** (85+) | Bing (News, Weather, Search, Translator), Xbox (App, GameOverlay, GamingOverlay, SpeechToText, TCUI), Skype, Teams, OneNote, Clipchamp, 3D Viewer, Maps, Solitaire, Mixed Reality, People, Camera, Photos, Paint3D, Wallet, GamingApp, GetHelp, GetStarted, FeedbackHub, MicrosoftFamily, OfficeHub, Outlook, PowerAutomate, QuickAssist, StickyNotes, ToDos, Zune (Music, Video), Alarms, DevHome, CrossDevice, CommunicationsApps, PeopleExperienceHost, WebExperience, Calling, CapturePicker, NarratorQuickStart, ParentalControls, Print3D, SecureAssessmentBrowser, XGpuEjectDialog, Advertising, Journal, ECApp, AV1/HEVC/HEIF/VP9/MPEG2/Raw/Dolby codec extensions, ScreenSketch, WebMediaExtensions |
| **Capabilities** (15+) | Internet Explorer, WordPad, PowerShell ISE, Media Player, Steps Recorder, Quick Assist, Print 3D, Wireless Display, Print Management Console, handwriting/OCR/speech/text-to-speech for detected language |
| **Windows packages** (10+) | Internet Explorer, WordPad, MediaPlayer, TabletPCMath, StepsRecorder, QuickAssist, PowerShell ISE, printing PMCPPC/WFS, XPS Viewer, ADAM client |
| **OneDrive** | Setup.exe (SysWOW64 + System32), shortcuts, WinSxS components, appdata folders, registry run keys |
| **Edge** | DISM Remove-Edge, Stable + DevTools AppX, EdgeUpdate registry/service, Program Files, scheduled tasks, WinSxS components, WebView2 (Win10 only — kept on Win11 for OOBE) |
| **AI / Copilot** | Copilot, Recall, AIX, CoreAI, GameAssist, WritingAssistant, OfficeActionsServer AppX packages + 30+ registry policies blocking AI in Search, Edge, Paint, Notepad, Office, and system-wide. Hidden CBS packages unhidden and removed. |
| **Widgets** | WebExperience + Widgets AppX packages, News & Interests policies, scheduled tasks, WinSxS components |
| **Services disabled** (40+) | DiagTrack, dmwappushsvc, WSearch, SysMain, MapsBroker, lfsvc, PhoneSvc, MessagingService, PimIndexMaintenanceSvc, BcastDVRUserService, XblAuthManager, XblGameSave, XboxNetApiSvc, XboxGipSvc, WalletService, PrintNotify, LicenseManager, WbioSrvc, FrameServer, CaptureService, SensorService, SensorDataService, SensrSvc, TabletInputService, TrkWks, FontCache, DusmSvc, WpnService, StiSvc, wisvc, RetailDemo, tzautoupdate, W32Time, wcncsvc, WerSvc, WiaRpc |
| **Registry / Privacy** | Sponsored apps, telemetry (AllowTelemetry=0), mouse acceleration, Meet Now, ads & suggestions, BitLocker auto-encryption, GameDVR/GameBar, Cortana, consumer features, location tracking, Find My Device, Windows Tips, background apps, web search, error reporting, delivery optimization, advertising ID, clipboard history, activity feed, timeline, setting sync, Windows Ink, lock screen suggestions |
| **Performance** | Disable visual effects, animations, hibernation. Optimize CPU priority (Win32PrioritySeparation=38), large system cache, disable paging executive, network throttling, ICMP redirects, Game Mode |

**Approximate footprint:** ~18–22 GB free on a 126 GB disk (varies by edition).

---

### Tier 2 — Aggressive Debloat (menu option 2)

Everything in Tier 1, plus additional aggressive package targets. Adds removal of:

| Category | What's Added Beyond Tier 1 |
|----------|---------------------------|
| **Extreme services** | wlidsvc (MS Account), UnistoreSvc, UserDataSvc, OneSyncSvc, Spooler (printing), LanmanServer (file sharing), LanmanWorkstation, fdPHost, FDResPub, SSDP/UPnP, SCardSvr, ScDeviceEnum, SmsRouter, BTAGService (Bluetooth audio), bthserv (Bluetooth), Fax, hidserv (keyboard/mouse special keys) |
| **Additional AppX** | DesktopAppInstaller (winget), StorePurchaseApp, WebMediaExtensions, AssignedAccessLockApp, SecureAssessmentBrowser |
| **Additional capabilities** | Hello.Face, OneCoreUAP.OneSync, OpenSSH.Client, Xps.Viewer, Print.Fax.Scan, MathRecognizer, ALL language handwriting/OCR/speech/TTS for all languages |
| **Additional packages** | Hello-Face, Print-Fax-Scan-Feature, VBSCRIPT, Legacy WOW64 compatibility, Notepad (Win10), MSPaint (Win10), MediaPlayer |
| **Defender (aggressive)** | 11 services disabled (including WdBoot/WdFilter/WdDevFlt kernel drivers + Security Center), 40+ registry policy keys, files stripped from Program Files + ProgramData + System32 + drivers, 7 WinSxS patterns, scheduled tasks nuked, 8 post-install RunOnce cleanup commands |
| **WinRE** | Winre.wim deleted, Recovery directory removed, WinRE registry disabled |
| **Windows Update** | All WU services disabled, WU scheduled tasks deleted, WU/waasmedic/updateorchestrator WinSxS components removed, WU orchestrator registry nuked, DoNotConnectToWindowsUpdateInternetLocations policy set |
| **Fonts** | All non-essential fonts removed — keeps only Segoe UI, Arial, Times New Roman, Courier New, Marlett, Symbol, Wingdings, Tahoma, Verdana, CJK system fonts |
| **WinSxS (deep)** | 50 patterns: winre, recovery, bitlocker, security, defender, speech, hyperv, virtual, containers, sandbox, WSL, print, fax, scanner, xps, hello, biometrics, narrator, accessibility, magnifier, screen, camera, tablet, touch, pen, ink, 3D, holographic, terminal, powershell, netfx, IIS, workfolders, branchcache, directaccess, RAS, VPN, NDIS, mobile, telephony, ADAM, LDAP, MSMQ, multipoint, RDMA, storage replica, tiering, DFS, failover |
| **Micro registry** | Store blocking, Mail/Calendar disable, live tiles off, push notifications off, all background apps force-deny, lock screen off, task view off, people bar off, news/feeds off, widgets off, nearby sharing off, game DVR off, Cortana force-disable, find my device off, sync force-disable, ink workspace off, clipboard history/cloud off, activity feed off |

**Approximate footprint:** ~8–12 GB free on a 126 GB disk.

---

### Tier 3 — Ultra Micro Mode (menu option 3 or `-UltraMicroMode yes`)

Everything in Tier 2, plus extreme disk space recovery. Auto-enables MicroMode + OOBEBypass.

| Category | What's Added Beyond Tier 2 |
|----------|---------------------------|
| **Servicing stack backups** | Entire `WinSxS\Backup\` directory stripped (~1–3 GB) |
| **Servicing manifests** | 60+ bloat component manifests deleted from `WinSxS\Manifests\` — defender, edge, xbox, skype, hyperv, print, fax, scanner, and more (~500 MB–1 GB) |
| **Driver store** | Non-critical drivers stripped: printer, scanner, fax, modem, bluetooth, wifi, wwan, sensor, camera, biometric, smartcard, NFC, touch, pen, tablet, audio, media, DRM, display, battery, thermal (~500 MB–2 GB) |
| **NGEN cache** | Pre-compiled .NET assemblies removed from `assembly\NativeImages*` and `Microsoft.NET\assembly\GAC_MSIL`; .NET will JIT on first run (~300–500 MB) |
| **Language MUI files** | All `xx-XX` directories in `System32` and `SysWOW64` removed except the detected language (~300 MB–1 GB) |
| **Visual resources** | All wallpapers, themes, cursors, 4K wallpapers, screen images deleted (~100–200 MB) |
| **Migration data** | `System32\migration`, `migwiz`, `oobe\migrate`, `Sysprep` files stripped (~100–300 MB) |
| **Reserved storage** | All ReserveManager registry keys zeroed; RunOnce fires `compact /compactos:always` on first boot (~7 GB live savings) |
| **Ultra WinSxS** | 50 additional broad patterns: servicing, winsat, migration, upgrade, compatibility, driver, remotedesktop, DVD, bluray, media, location, wallet, parentalcontrols, family, easeofaccess, windowstogo, spelling, dictation, networking, SNMP, telnet, TFTP, RIP, LPD, LPR, IPC, RPC (~500 MB–2 GB) |
| **Ultra registry + RunOnce** | System restore disable, prefetch/superfetch disable, pagefile disable, dism /resetbase run-once, WinSxS Backup/ManifestCache strip on live system |
| **Additional AppX** | Cortana, Search, Apprep.ChxApp, ECApp |
| **Additional packages** | IE, StepsRecorder, QuickAssist, PowerShell ISE, printing PMCPPC/WFS, XPS Viewer, ADAM client |

**Approximate footprint:** ~2–4 GB used on a 126 GB disk (~122 GB free).

---

### Which Tier Should You Use?

| Tier | Command | Use Case |
|------|---------|----------|
| **Standard** | `-SafeMode` or pick options manually | Safe for any edition. No OOBE issues. |
| **Micro** | `-MicroMode yes` | Maximum stripping with working desktop. Combine with `-OOBEBypass yes` to skip the broken OOBE. |
| **Ultra Micro** | `-UltraMicroMode yes` | Closest to Micro 10/11 builds. Maximum free disk space. OOBEBypass auto-enabled. |

> **LTSC users:** Skip the debloater entirely. Your ISO is already stripped by Microsoft. Use the [rintechtoolkit](rintechtoolkit/) instead to add drivers, wallpapers, OEM branding, registry tweaks, and more to your LTSC ISO.

---

## Cleanup

After one or more debloat/modding sessions, leftover files accumulate:

| Leftover | Source |
|----------|--------|
| `C:\WIDTemp\` | isoDebloater.ps1 work directory |
| `C:\ISOToolkit\` | isoToolkit.ps1 work directory |
| `ISOBackup\` | Original ISO backups (~6 GB each) |
| `ErrorLogs\` | Timestamped error logs from all tools |
| `ADKDownload\` | Cached oscdimg / ADK downloads |
| `*.iso` (output ISOs) | Debloated/custom output ISOs |
| `*.log`, `*.txt` | Script/transcript logs |
| `%TEMP%\toolkit_*.reg` | Temp registry import files |

Run **`Cleanup.bat`** (as Administrator) from the toolkit folder to clear everything. It prompts before removing each ISO so you can keep ones you want. After cleanup, the toolkit folder returns to its initial state — ready for a fresh run.

---

## Virtual Machine Troubleshooting

> **IMPORTANT:** These issues are caused by **VM configuration quirks** and the **natural side effects of debloating** — they are **NOT defects in your debloated ISO.** Do not file a bug report for these.

### 1. Endless "Install Now" Loop (Hyper-V Gen 1)

**Symptom:** After installation reaches 100% and reboots, the VM drops back into the initial setup screen endlessly.

**Root Cause:** Hyper-V Generation 1 VMs boot from the attached virtual DVD drive before the virtual hard disk on every restart.

**Fix:** Power off the VM completely. Go to **VM Settings → DVD Drive** → set **Media** to **"None"** (unmount the ISO). Apply and start the VM cold. Alternatively, do not press any key when *"Press any key to boot from CD..."* appears.

### 2. OOBE Stuck at Region / "Just a Moment" Loop

**Symptom:** The system hangs on the blue "Just a moment..." screen or loops endlessly at the region/keyboard selection screen.

**Root Cause:** Debloating strips the consumer telemetry and online account frameworks that the OOBE wizard expects. The interactive OOBE panics when its online hooks are absent.

**Fix:**
1. At the stuck screen, press **Shift + F10** (or **Fn + Shift + F10** on laptops) to open Command Prompt.
2. Enable the built-in Administrator account:
   ```
   net user administrator /active:yes
   ```
3. Kill the stuck OOBE process and relaunch it properly:
   ```
   cd C:\Windows\System32\oobe
   msoobe
   ```
4. The OOBE wizard will restart. Go through the screens normally — it should now proceed past the region section.
5. **If testing in Hyper-V:** After completing the OOBE, you **must restart the virtual machine** (Action → Reset or Ctrl+Alt+End → restart). Hyper-V does not properly transition from the OOBE environment to the desktop without a cold restart.
6. On first login, you can set up your own user account and disable the built-in Administrator if desired.

### 3. Automatic OOBE Bypass (Built Into the Debloater)

Instead of fixing OOBE loops manually, you can have the debloater **inject an automatic bypass** directly into the ISO. When enabled (`-OOBEBypass yes` or answer Yes at the prompt), the debloater does two things:

1. **Injects `SetupComplete.cmd`** into the ISO (`\Windows\Setup\Scripts\`). This script runs at the very end of Windows Setup and:
   - Creates a local Administrator account (`Admin`, no password)
   - Configures auto-logon so the system boots straight to desktop
   - Force-sets all OOBE bypass registry keys (`OOBEInProgress=0`, `SetupType=0`, `BypassNRO=1`, etc.)
   - Disables the OOBE scheduled tasks that would otherwise fire

2. **Pre-seeds offline registry keys** in the mounted image so the bypass is baked in before the first boot even happens.

**Result:** After installation completes, the system reboots once and lands directly on the desktop as `Admin` — no OOBE screens, no region selection, no account creation, no "Just a moment" loop. Zero user interaction needed after install.

**How to use:**
```
.\isoDebloater.ps1 -OOBEBypass yes
```
Or choose "Yes" at the interactive prompt: *"Automatically bypass OOBE after install?"*

**Security note:** The auto-created `Admin` account has no password. Set one on first login or disable the account and create your own.

---

## What Happens During Execution

| Step | Description | Time |
|------|-------------|------|
| ISO selection & backup | Copies original ISO to `ISOBackup\` folder | ~1-2 min |
| Mount & extract | Copies ISO contents to temp working directory | ~2-5 min |
| Package removal | Removes AppX packages via DISM | ~3-8 min |
| Edge removal | Strips Edge browser, WebView, EdgeUpdate | ~1-3 min |
| AI removal | Removes Copilot, Recall, AI components | ~1-3 min |
| Registry tweaks | Privacy, performance, telemetry policies | ~1-2 min |
| Service disabling | Unnecessary services disabled | <1 min |
| Widgets removal | Strips Widgets, News, WebExperience | ~30 sec |
| Hyper-V removal (optional) | Removes virtualization platform | ~1-2 min |
| Defender removal (optional) | Aggressive Defender strip | ~1-2 min |
| Scheduled tasks (optional) | Disables telemetry/bloat tasks | ~1 min |
| WinSxS cleanup (optional) | Conservative bloat component cleanup | ~2-5 min |
| Micro Mode stripping (optional) | WinRE, fonts, WU, deep WinSxS, Micro registry | ~5-10 min |
| Ultra Micro stripping (optional) | Servicing backups, driver store, NGEN, MUI, manifests, CompactOS | ~10-20 min |
| OOBE Bypass injection (optional) | SetupComplete.cmd + offline reg keys | <1 min |
| Privacy tweaks | Cortana, location, activity, ads policies | <1 min |
| Image cleanup | DISM component cleanup & compression | ~10-20 min |
| Health check & repair | Checks & repairs component store | ~2-5 min |
| Export WIM | Compresses modified image | ~5-20 min |
| ISO creation | Builds bootable ISO with oscdimg | ~2-5 min |
| **Total (Standard)** | | **~25-50 min** |
| **Total (Micro)** | | **~35-65 min** |
| **Total (Ultra Micro)** | | **~50-90 min** |

---

## Command-Line Automation

```powershell
# Fully automated Standard debloat
.\isoDebloater.ps1 -noPrompt -isoPath "C:\Win11.iso" -winEdition "Windows 11 Pro" -outputISO "Win11Lite"

# Micro mode — maximum stripping, OOBE bypass auto-injected
.\isoDebloater.ps1 -MicroMode yes -OOBEBypass yes -isoPath "C:\Win11.iso" -winEdition "Windows 11 Pro" -outputISO "Win11Micro"

# Ultra Micro mode — extreme disk savings, everything auto-enabled
.\isoDebloater.ps1 -UltraMicroMode yes -isoPath "C:\Win11.iso" -winEdition "Windows 11 Pro" -outputISO "Win11UltraMicro"

# Safe mode — remove only apps, keep everything else
.\isoDebloater.ps1 -SafeMode

# Custom selection
.\isoDebloater.ps1 -AppxRemove yes -CapabilitiesRemove yes -OnedriveRemove yes -EDGERemove yes -AIRemove yes -DefenderRemove yes -OOBEBypass yes

# LTSC ISO — service disabling and performance tweaks only (LTSC is already debloated)
.\isoDebloater.ps1 -ServicesDisable yes -PerformanceTweaks yes -TPMBypass yes -ESDConvert yes -isoPath "C:\LTSC.iso" -winEdition "Windows 10 Enterprise LTSC" -AppxRemove no -CapabilitiesRemove no -OnedriveRemove no -EDGERemove no -AIRemove no -WidgetsRemove no -DefenderRemove no
```

### All Parameters

| Parameter | Values | Default | Description |
|-----------|--------|---------|-------------|
| `-noPrompt` | (switch) | off | Fully automated — requires `-isoPath`, `-winEdition`, `-outputISO` |
| `-isoPath` | path string | prompted | Path to source Windows ISO |
| `-winEdition` | edition name | prompted | Edition name to mount (e.g. "Windows 11 Pro") |
| `-outputISO` | filename | prompted | Output ISO filename (no extension) |
| `-SafeMode` | (switch) | off | AppX removal only, safe for any edition |
| `-AppxRemove` | yes/no | yes | Remove 85+ bloatware AppX packages |
| `-CapabilitiesRemove` | yes/no | yes | Remove 15+ optional Windows features |
| `-OnedriveRemove` | yes/no | yes | Completely remove OneDrive |
| `-EDGERemove` | yes/no | yes | Remove Microsoft Edge |
| `-AIRemove` | yes/no | yes | Remove Copilot, Recall, all AI components |
| `-TPMBypass` | yes/no | no | Bypass TPM 2.0, Secure Boot, CPU checks |
| `-UserFoldersEnable` | yes/no | yes | Restore Desktop/Documents folders on Win11 |
| `-DriverIntegrate` | yes/no | no | Integrate Intel RST/VMD drivers |
| `-ESDConvert` | yes/no | no | Compress ISO with recovery (ESD) compression |
| `-useOscdimg` | yes/no | yes | Use oscdimg.exe for reliable ISO build |
| `-useDISM` | yes/no | no | Force DISM CLI over PowerShell cmdlets |
| `-DefenderRemove` | yes/no | no | **Aggressive** — strips Defender completely (files, drivers, WinSxS, Security Center) |
| `-WidgetsRemove` | yes/no | yes | Remove Widgets and WebExperience |
| `-WinUpdateDisable` | yes/no | no | Disable Windows Update services + policies |
| `-HyperVRemove` | yes/no | no | Remove Hyper-V virtualization platform |
| `-ExtremeDebloat` | yes/no | no | Disable print spooler, Bluetooth, LAN sharing, etc. |
| `-**MicroMode**` | yes/no | no | **Tier 2** — WinRE, fonts, deep WinSxS, aggressive Defender, WU strip |
| `-**UltraMicroMode**` | yes/no | no | **Tier 3** — servicing backups, driver store, NGEN, MUI, manifests, CompactOS |
| `-ServicesDisable` | yes/no | yes | Disable 40+ telemetry/bloat services |
| `-PerformanceTweaks` | yes/no | yes | CPU, memory, network optimizations |
| `-TaskCleanup` | yes/no | no | Disable 45+ telemetry scheduled tasks |
| `-WinSxSCleanup` | yes/no | no | Conservative WinSxS bloat directory removal |
| `-UseAutounattend` | yes/no | no | Use autounattend.xml for unattended setup (CAUTION) |
| `-**OOBEBypass**` | yes/no | no | Inject SetupComplete.cmd — auto-creates Admin account, skips OOBE entirely |

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

## Skip / Stuck Recovery

- **Press `S`** during any looping operation (package removal, service disabling, scheduled tasks, WinSxS cleanup) to skip the remaining items in that section.
- For DISM operations, the script has a 2-retry timeout mechanism. If a DISM command hangs, it auto-skips after the timeout.
- If the script crashes mid-run leaving a mounted WIM: run `dism /unmount-image /mountdir:"C:\WIDTemp\mountdir\installWIM" /discard` before re-running.

---

## Which ISO Should You Use?

**Any Windows edition can be debloated safely.** Earlier assumptions that Home/Pro editions were "risky" were based on misdiagnosed VM issues — the actual root cause of installation loops was **incorrect Hyper-V settings and poor OOBE management** (see [VM Troubleshooting](#virtual-machine-troubleshooting) above), not ISO corruption or edition incompatibility.

### Best ISOs for Debloating

If your goal is a clean, well-stripped ISO with predictable results, **start with a full-featured edition** — you get more control over exactly what gets removed:

| Priority | ISO Edition | Why |
|----------|------------|-----|
| **1st** | **Windows Pro / Pro for Workstations** | Full consumer load. All removal targets present and processed. Gives you the most control over what stays and what goes. Consistent, predictable results. |
| **2nd** | **Windows Enterprise** | Fewer consumer integrations than Pro. Moderate error count during stripping, but fewer removal targets available for customization. |
| **3rd** | **Windows Home** | Heaviest consumer integrations. Works fine but takes the longest to strip and produces the most errors (all harmless). |
| **Not recommended** | **Windows LTSC (IoT/Enterprise LTSC)** | **Already stripped by Microsoft.** LTSC ships without Store apps, Widgets, Copilot, AI, Xbox, Bing, and most consumer features — these are the very things the debloater is designed to remove. Running the debloater on LTSC produces mountains of "not found" errors (because LTSC never had these components) and offers very little benefit. You're not debloating — you're just running DISM operations that find nothing to remove. **If you want a lean Windows install, just install LTSC as-is.** |

**Key insight:** Debloating works best on full editions (Pro/Home) because you can selectively remove what you don't want. LTSC already removed everything — there's nothing left to debload. Running the debloater on LTSC is wasted time.

### LTSC: Great for Modding, Terrible for Debloating

LTSC editions serve two very different purposes depending on which tool you use:

| Tool | Recommended for LTSC? | Why |
|------|----------------------|-----|
| **isoDebloater.ps1** | **NO** — do not use | The debloater tries to strip AppX packages, features, and components that LTSC doesn't even have. Every removal operation produces errors. In testing, the debloater's mount operations repeatedly failed with `Error: 0xc1420127` ("already mounted") and `Error: 0xc1420117` ("could not be completely unmounted"), leaving the WIM in a broken state where EVERY subsequent DISM command returned "The specified image is invalid." The entire debloat run cascades into failure. |
| **rintechtoolkit\isoToolkit.ps1** | **YES** — excellent base | The modding toolkit doesn't try to remove what's already gone. It adds things: drivers, updates, .NET, registry tweaks, OEM branding, wallpapers, autounattend.xml, WinRE recovery. LTSC is the ideal canvas for modding because you start clean and only add what you want. |

**If you tried debloating an LTSC ISO and it failed:** That's expected. The debloater assumes a full consumer ISO with all the bloat present. On LTSC, the mount gets confused by the already-clean state, DISM reports errors trying to remove nonexistent packages, and the entire session falls apart. **Use LTSC for modding with the toolkit, not debloating with the debloater.**

### Multi-Pass Stripping (Double / Triple Strip)

Instead of running one massive debloat session, you can **run the debloater multiple times on the same ISO.** Each pass catches components the previous pass exposed or missed:

```
Pass 1: isoDebloater.ps1 -MicroMode yes -OOBEBypass yes -isoPath "stock.iso" -outputISO "Pass1"
Pass 2: isoDebloater.ps1 -MicroMode yes -OOBEBypass yes -isoPath "Pass1.iso" -outputISO "Pass2"  
Pass 3: isoDebloater.ps1 -UltraMicroMode yes -isoPath "Pass2.iso" -outputISO "Final"
```

**Why this works:**
- **DISM component cleanup** between passes (which the script runs automatically) removes orphaned packages and unlocks deeper WinSxS layers that were previously protected by dependency chains
- The **Health Check & Repair** phase after each pass fixes any component store issues before the next pass
- After the first pass strips the bulk, subsequent passes can reach deeper into the servicing stack and catch leftovers the first pass left behind
- **Each pass compresses the WIM** (export), shrinking the ISO further — a triple-stripped LTSC ISO can be 40-50% smaller than a single-pass result

**Recommended multi-pass strategy:**

| Goal | Passes | Settings |
|------|--------|----------|
| **Clean daily-driver** | 1 pass | Standard tier (`-SafeMode` or custom options) |
| **Maximum storage savings** | 2 passes | Both Micro tier (`-MicroMode yes -OOBEBypass yes`) |
| **Micro 10/11 comparable** | 3 passes | 2x Micro + 1x Ultra Micro (final pass) |
| **Absolute minimum footprint** | 3 passes on Pro/Enterprise | 2x Micro + 1x Ultra Micro on a full-featured edition base |

**Note:** Multi-pass stripping only makes sense on Micro or Ultra Micro tiers. Standard-tier passes won't gain much from repetition since they don't touch WinSxS or the servicing stack.

### Key Realization

The installation problems previously attributed to "corrupted ISOs" or "edition incompatibility" were actually caused by:

1. **Hyper-V Generation 1 boot order** — the VM boots the ISO instead of the hard disk on every restart, looping back to "Install Now" (Section 1 of [VM Troubleshooting](#virtual-machine-troubleshooting)).
2. **Stripped OOBE hooks** — Micro/Ultra Micro tiers remove the telemetry and online account frameworks the OOBE wizard expects, causing it to hang at "Just a moment..." (Section 2 of [VM Troubleshooting](#virtual-machine-troubleshooting)).

**Both are easily fixed** — unmount the ISO after install, or use `-OOBEBypass yes` to skip the wizard entirely. The debloated ISO itself is fine.

### For Home/Pro ISOs — Use the Tiers

Home and Pro editions respond identically to debloating. Choose the tier that matches your needs:

| Tier | Command | Result |
|------|---------|--------|
| **Standard** | `-SafeMode` or pick options | Clean ISO, OOBE works normally. ~18 GB free. |
| **Micro** | `-MicroMode yes -OOBEBypass yes` | Heavily stripped, OOBE skipped, auto-logon. ~8-12 GB free. |
| **Ultra Micro** | `-UltraMicroMode yes` | Maximum stripping, everything auto-enabled. ~122 GB free. |

Or if you prefer to debloat a live system instead of the ISO:
```
BakuretsuClean — live-OS debloater (included in the repo)
Run: BakuretsuClean\RunDebloater.bat as Administrator
Features: AppX removal, service disabling, telemetry blocking, privacy tweaks, revert support
```

---

## LTSC Guidance

LTSC is already debloated by Microsoft — don't run the debloater on it. See [LTSC: Great for Modding, Terrible for Debloating](#ltsc-great-for-modding-terrible-for-debloating) above for details.

**Use LTSC with the rintechtoolkit** to add drivers, updates, wallpapers, OEM branding, registry tweaks, WinRE recovery, and autounattend.xml. LTSC is the perfect clean canvas for modding.

If you must run the debloater on LTSC, skip every removal option — only service disabling, performance tweaks, and TPM bypass make sense:

```
-AppxRemove no -CapabilitiesRemove no -OnedriveRemove no -EDGERemove no
-AIRemove no -WidgetsRemove no -DefenderRemove no -TaskCleanup no -WinSxSCleanup no
```

```
-TPMBypass yes -UserFoldersEnable yes -ESDConvert yes
-useOscdimg yes -PerformanceTweaks yes -ServicesDisable yes
```

---

## Tips

- **Understand the tiers** — Standard is safe for all editions. Micro breaks OOBE but produces a working desktop (use `-OOBEBypass yes`). Ultra Micro maximizes free space but removes more components.
- **LTSC ISOs are already debloated by Microsoft** — running the debloater on LTSC provides little to no benefit and will likely fail. Use the rintechtoolkit for modding LTSC instead. See [LTSC: Great for Modding, Terrible for Debloating](#ltsc-great-for-modding-terrible-for-debloating).
- **Always test in a VM first** — before deploying a debloated ISO to real hardware, install it in Hyper-V or VMware to verify it boots and runs.
- **Use a fast SSD** for the working directory — DISM operations are disk-intensive.
- **Disable real-time AV** temporarily — some AV products interfere with DISM mounting operations.
- **The backup ISO** at `ISOBackup\original_backup.iso` can be deleted after confirming the debloated ISO works.
- **Check the error logs** at `ErrorLogs\` if the debloated ISO has installation issues.
- **Use the rintechtoolkit after debloating** — to add drivers, customize wallpaper, set OEM branding, create autounattend.xml, or recover removed components (WinRE, features) from a donor ISO.

---

## Tested Versions

- **Windows 10**: 22H2 (Build 19045)
- **Windows 11**: 24H2 (Build 26100)

---

## Credits

- Created by shirayukimikoto and fujiwarasayo
- Inspired by MSMG Toolkit, KernelOS, and Windows X-Lite stripped builds
- [tiny11builder](https://github.com/ntdevlabs/tiny11builder) for inspiration
- [Winaero](https://winaero.com/) for registry optimization techniques
- [RemoveWindowsAI](https://github.com/zoicware/RemoveWindowsAI) for AI removal approach

## License

GPL-3.0 — Use at your own risk. Always test debloated ISOs in a VM before deploying.
