# Aur0ra_B0r3alis

[![GitHub Repo](https://img.shields.io/badge/GitHub-aur0rab0r3alis-blue?logo=github)](https://github.com/samueltogepi/aur0rab0r3alis)  
**Version:** 0.5 (as of October 06, 2025)  
**Author:** Samuel Bowers (3-BSD)  
**License:** MIT (see LICENSE file)  

Aur0ra_B0r3alis is an advanced, open-source tethered downgrade and jailbreak activation tool designed specifically for legacy Apple A8/A8X devices. Built on the foundations of Legacy-iOS-Kit and turdus merula, it enables users to downgrade from iOS 15.8.5 back to any version between iOS 9.2 and iOS 11.4.1, with automated activation restoration and jailbreak bootstrapping via JBME (JailbreakMe). This tool is particularly useful for enthusiasts restoring old iPads to their nostalgic glory, running 32-bit apps/games, or experimenting with semi-untethered jailbreaks.

**Warning:** Downgrading iOS can brick your device if mishandled. This is for advanced users only. Always back up your data, ensure you have a USB-A Lightning cable, and follow instructions precisely. No support for Cellular models with baseband issues (WiFi recommended). Proceed at your own risk.

## Features

- **Full Tethered Downgrade Support:** Downgrade to **every minor and point release** from iOS 9.2 (13C75) to iOS 11.4.1 (15G77), including all 39+ variants. Uses turdus merula for SEP patching (SHCBlock/PTEBlock extraction) and checkm8 exploitation.
- **Automated Activation Restoration:** Extracts and restores activation files (activation_records, data_ark.plist, IC-Info.sisv, commcenter prefs, factorydata) from a temporary iOS 15.8.5 restore via SSH ramdisk.
- **Jailbreak Bootstrap:** Integrates JBME loader for semi-untethered jailbreaks post-downgrade. Generates per-device boot scripts for tethered booting (e.g., `boot_mini4-11.3.1_15E302-serial.sh`).
- **Device-Specific Handling:** Supports iPad Air 2 (A8X, iPad5,3 WiFi / iPad5,4 Cellular) and iPad mini 4 (A8, iPad5,1 WiFi / iPad5,2 Cellular). Detects TSMC/Samsung chips; WiFi models prioritized.
- **Cross-Platform:** Runs on Linux (x86_64/arm64) and macOS (arm64/x86_64). No Windows support.
- **IPSW Management:** Auto-downloads IPSWs from Apple's CDN if not cached. Intermediate restore to iOS 10.2.1 (HFS+) prevents DFU crashes and Wi-Fi greying.
- **Safety Checks:** Enforces Recovery Mode, pairs devices, validates activation, and handles common errors (e.g., usbmuxd resets).
- **Nostalgia-Focused:** Ranked version recommendations based on performance, compatibility (32-bit apps until iOS 10), and user memories (iOS 11 > 10 > 9).

| iOS Major | Versions Supported | Key Highlights |
|-----------|---------------------|----------------|
| **iOS 9** | 9.2 to 9.3.5 (9 total) | Lightest on A8; full 32-bit; untethered Pangu9 jailbreak. iMessage tweaks needed for 9.3+. |
| **iOS 10** | 10.0 to 10.3.4 (13 total) | Last 32-bit support; Yalu102/h3lix semi-untethered; Raise to Wake, Night Shift. |
| **iOS 11** | 11.0 to 11.4.1 (16 total) | No 32-bit; ARKit, modern UI; Electra/h3lix; best security/memories. |

**Recommended First Downgrade (for iPad mini 4 WiFi 128GB):** iOS 11.3.1 – Balances nostalgia, smoothness (~8hr battery), and 64-bit app compatibility. Follow with iOS 10.3.4 for 32-bit games, then iOS 9.3.5 for original feel.

## Requirements

### Hardware
- **Device:** iPad Air 2 (A8X) or iPad mini 4 (A8) on iOS 15.8.5. WiFi model preferred (no baseband SEP blobs needed).
- **Cable:** USB-A to Lightning (USB-C adapters may cause issues).
- **Computer:** Linux (Ubuntu/Debian recommended) or macOS (Ventura+). Root/sudo on Linux.

### Software
- **Dependencies:** Bundled via Legacy-iOS-Kit (auto-installed on first run). Includes:
  - libimobiledevice, usbmuxd, irecovery, idevice tools.
  - turdus merula (v1.1_b0ea3ee7).
  - expect, sshpass for ramdisk automation.
- **No Internet for Core Tools:** But required for IPSW downloads (~1-4GB each).
- **Build Tools (if compiling from source):** bash, curl, tar, expect.

**Note:** Samsung chip variants (CPID 0x7000/0x7001) may require manual SEP blobs; TSMC (0x8004/0x8015) fully supported.

## Installation

1. **Download Release:**
   - Clone or download from [GitHub](https://github.com/samueltogepi/aur0rab0r3alis).
   - Extract to a folder (e.g., `~/aur0rab0r3alis-v0.5`).
   - Ensure `payload/com.alex.activate.plist` exists (release-only; build if from source).

2. **On Linux (Recommended):**
   ```bash
   cd ~/aur0rab0r3alis-v0.5
   sudo chmod +x aur0ra_b0r3alis.sh
   sudo ./aur0ra_b0r3alis.sh  # Installs deps on first run
   ```

3. **On macOS:**
   ```bash
   cd ~/aur0rab0r3alis-v0.5
   chmod +x aur0ra_b0r3alis.sh
   xattr -rc .  # Remove quarantine
   ./aur0ra_b0r3alis.sh
   ```

4. **First Run Setup:**
   - Script auto-installs Legacy-iOS-Kit deps.
   - Reconnect USB cable as prompted.
   - If updating from older release: `./aur0ra_b0r3alis.sh -u /path/to/old/release` (copies data/boot scripts).

## Usage

1. **Connect Device:**
   - Put iPad in Recovery Mode (or Normal; script handles entry).
   - Connect via USB-A Lightning.

2. **Run Script:**
   ```bash
   sudo ./aur0ra_b0r3alis.sh  # Linux root required
   ```
   - Detects device/chip (e.g., "iPad mini 4 detected! TSMC A8 chip").
   - Prompts for iOS version selection (full menu of 39 options).
   - Downloads IPSW if needed (cached in `data/ipsw/`).

3. **Downgrade Process (Automated):**
   - **Step 1:** If not on iOS 15.8.5, restores to it for activation files.
   - **Step 2:** Extracts activation files via SSH ramdisk (complete Setup.app/iCloud on temp iOS).
   - **Step 3:** Intermediate restore to iOS 10.2.1 (stability fix).
   - **Step 4:** Patches SEP (SHC/PTE blocks), restores target iOS.
   - **Step 5:** Injects activation + JBME bootstrap via ramdisk.
   - **Step 6:** Tethered boot; enable JBME in Safari (http://jbme.ddw.nu).
   - Generates boot script (e.g., `boot_mini4-9.3.5_13G36-ABC123.sh`) for future boots.

4. **Post-Downgrade:**
   - **Jailbreak:** Visit JBME link; reboot to Recovery, run boot script: `sudo ./boot_mini4-11.3.1_15E302-ABC123.sh`.
   - **iCloud:** For iOS 9/11, skip in Setup; sign in via Settings.
   - **iMessage/FaceTime:** Works on iOS 10/11; tweaks for iOS 9.3+.
   - **Reboot Loop:** Always tethered until semi-untethered jailbreak (e.g., h3lix for iOS 10/11).

**Example Output:**
```
Detected Linux on x86_64.
iPad mini 4 detected!
Info: TSMC A8 chip detected!
Serial Number Identifier: ABC123DEF
Select iOS version to downgrade to: 11.3.1 (15E302) (04/24/2018)
Restoring iOS 11.3.1_15E302 with iPad_64bit_TouchID_11.3.1_15E302_Restore.ipsw..
Your iPad is now jailbroken! Enable with http://jbme.ddw.nu
Activation was restored! Run boot_mini4-11.3.1_15E302-ABC123DEF.sh for future boots.
iOS 11: No 32-bit, but ARKit and modern apps. Sign in iCloud via Settings.
```

**Boot Script Usage (After First Downgrade):**
```bash
sudo ./boot_mini4-9.3.5_13G36-ABC123DEF.sh  # Boots from Recovery
```
- Handles usbmuxd, turdusra1n PTE load, and JBME reminder.

## Supported Versions (Detailed Ranking)

Based on A8 performance (lightest: iOS 9 > 10 > 11), 32-bit support (iOS 10 last), security (later better), and nostalgia (iOS 11 memories > iOS 9 first > iOS 10 games).

(Full list in script menu.)

## Troubleshooting

- **"No device detected":** Reset usbmuxd (`sudo systemctl restart usbmuxd`), reconnect cable, trust device.
- **DFU Loop:** Hold Home+Power until Recovery; script auto-exits DFU.
- **Download Fails (404):** Update URL in script with fresh from [ipsw.me](https://ipsw.me). E.g., for iPad5,1-11.3.1: Copy redirect link.
- **Activation Stuck:** Ensure Find My off, iCloud signed in on temp iOS 15. Retry `get_activation`.
- **Ramdisk SSH Fails:** Check iproxy port 6414; kill processes, rerun.
- **Samsung Chip Errors:** SEP blobs needed; use future-proof TSMC devices.
- **Jailbreak Crashes:** Enable JBME immediately; re-run boot script.
- **Logs:** Check `data/$device-$serial/` for blocks/tars.

**Common Errors & Fixes:**
| Error | Cause | Fix |
|-------|-------|-----|
| "Failed to open handle" | USB issue | Reconnect after Checkm8. |
| "Not activated" | Incomplete Setup | Complete iCloud on iOS 15 temp. |
| "PTEBlock missing" | turdus merula bug | Retry `turdursra1n_pwn`. |

## Building from Source

1. Clone repo.
2. Run `./build.sh` (generates release with signed binaries).
3. Test: `./aur0ra_b0r3alis.sh --test` (dry-run mode).

## Credits & Thanks

- **Base Tools:** Legacy-iOS-Kit (ramdisk), turdus merula (checkm8/tethered boot), JBME (bootstrap).
- **Inspiration:** A999 Activator (iPhone adaptation), palera1n (Linux usbmuxd tricks).
- **Community:** r/LegacyJailbreak, ipsw.me for IPSW verification.
- **Author Note:** (C) 2026 Samuel Bowers. Contributions welcome via PRs.

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*Last Updated: October 06, 2025. Report issues at [GitHub Issues](https://github.com/samueltogepi/aur0rab0r3alis/issues).*
