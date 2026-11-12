# Hackintosh-Deskmini310

🌐️ English | [中文](./zh_CN.md)

## My Configuration

- Motherboard: ASROCK 310-com
- SSD: Fanxiang S500PRO 1TB
- CPU: i7-8700
- Memory: 16G DDR4 X2
- Mouse: Magic Mouse
- Keyboard: Magic Keyboard
- Monitors:
  - Dell 1080p, connected via HDMI
  - Aoc 4K, connected via DP
- Wireless Card: BCM94360CS2 + adapter

## Usage

Since 2019, used for:

- Web development, mainly using VSCode editor, good experience
- Xcode development, compilation process notably slower than MacBook Air(M1,8G)

Current system version 14.8.7 (23J520).

Compared to genuine Apple computers, shortcomings (possibly due to inferior antenna):

- Slightly slower response time for Bluetooth mouse and keyboard
- Universal Control (controlling two computers with one mouse) has high failure rate

In August 2024, due to my work requiring higher CPU performance (the CPU would be more than sufficient for regular office work), it was replaced by a Mac mini M2 Pro.

However, it wasn't abandoned but given an even more important mission: serving as an All In One mini computer!

Details are recorded here:
<https://nookery.github.io/Deskmini-All-In-One/>

## BIOS Settings

This section is from: <https://github.com/xjn819/Hackintosh-Deskmini310-EFI>

- CPU Configuration, CPU C states Support: Enabled
- CPU Configuration, CPU C states Support, CFG Lock: Disabled (required)
- Chipset Configuration, Vt-d: Disabled
- Chipset Configuration, Onboard HD Audio: Enabled
- USB Configuration, XHCI Hand-off: Enabled (crucial)
- Super IO Configuration, Serial Port: Disabled (required)
- Security Secure Boot: Disabled (by default)
  - Boot, CSM: Disabled
  - BIOS version must be 4.0 or above

## Sequoia

Not tested.

## Sonoma

### Steps

- First modify the three system identifiers in OpenCore configuration file
- Install the system
- After entering the system, OpenCore-Patcher needs to be installed for Wi-Fi to work normally
  - Download OpenCore-Patcher
  - Click "Post-Install Root Patch" button
  - Restart after installation as prompted

### Known Issues

- Connecting to certain Wi-Fi networks can cause Bluetooth mouse lag (possibly due to certain Wi-Fi frequencies interfering with Bluetooth)
- HDMI connection to 4K monitor shows screen artifacts (that's why my 4K monitor is connected via DP)

### Intel UHD 630 GPU Hang Investigation (November 2026)

Tested on macOS 14.8.7 (23J520) with OpenCore 0.9.9, Lilu 1.6.7, WhateverGreen 1.6.6, and VirtualSMC 1.3.2. The i7-8700 UHD 630 uses `AAPL,ig-platform-id = 07009B3E` and a `0x3E9B` device ID spoof.

Five GPU Reset reports showed the same signature:

- Process: `mediaanalysisd`
- Scheduler: `Host Preemptive`
- Hung engine: `RCS Ring`
- Last instruction header: `0x7a000004`

The recurring failure sequence was:

1. `mediaanalysisd` started its filesystem activity during screen sleep or Power Nap.
2. Vision TextRecognition processed Draw.io PNG atlases stored in several development directories. Logs showed `Failed to obtain IOSurface; falling back to rendering path` shortly before the hang.
3. The Intel RCS engine stopped responding and macOS generated a GPU Reset report.
4. A subsequent sleep transition timed out after 180 seconds in `com.apple.iokit.IOGraphicsFamily`, causing the kernel panic.

A CPU-only `VNRecognizeTextRequest` completed successfully on one repeatedly scanned PNG. This proves that the image is readable and that the CPU request can finish, but it does not validate every Vision GPU compute path.

The current assessment is an Apple Vision/TextRecognition and Intel graphics driver defect or compatibility problem, not an OpenCore bootloader failure. Hackintosh device ID, framebuffer, and power-management differences may still affect whether the problem is triggered. No real Macmini8,1 control test has been performed.

The following are **not verified fixes**:

- `-disablegfxfirmware`: forces Host Preemptive scheduling and avoids the earlier GuC firmware exception, but the same RCS hang still occurs. GuC is therefore not the sole root cause.
- `-igfxgl=1`: the argument appeared in `boot-args`, but `MetalPluginName` remained present and the same GPU hang occurred. It did not disable Metal on this installation and must not be treated as a working solution.
- Lilu 1.7.2 and WhateverGreen 1.7.1: their available release notes did not identify a fix for this failure signature, so upgrading has not been validated as a solution.

As a temporary mitigation, `mediaanalysisd` was disabled and stopped on the affected machine:

```sh
launchctl disable gui/$(id -u)/com.apple.mediaanalysisd
launchctl bootout gui/$(id -u)/com.apple.mediaanalysisd
```

The disabled state and the absence of the process were verified. This blocks the demonstrated crash trigger, but also pauses background filesystem and Photos OCR, face recognition, and scene analysis. It does not repair the graphics driver, and this machine-specific mitigation is not part of the EFI files in this repository.

To restore background media analysis when testing a future fix:

```sh
launchctl enable gui/$(id -u)/com.apple.mediaanalysisd
launchctl bootstrap gui/$(id -u) /System/Library/LaunchAgents/com.apple.mediaanalysisd.plist
```

Diagnostic reports are stored in `/Library/Logs/DiagnosticReports/Kernel_*.gpuRestart` and `/Library/Logs/DiagnosticReports/Kernel-*.panic`.

## Ventura

### Steps

- First modify the three system identifiers in OpenCore configuration file
- Install the system

### Known Issues

None found yet

## Projects Developed on This Computer

- [Cisum](https://github.com/CofficLab/Cisum) — Music player for Apple platforms
- [TravelMode (Netto)](https://github.com/CofficLab/Netto) — Per-app network access control for macOS
- [GitOK](https://github.com/CofficLab/GitOK) — Project management tool for macOS
- [Lumi](https://github.com/CofficLab/Lumi) — AI-powered desktop assistant for macOS
- [Wakey](https://github.com/CofficLab/Wakey) — Lightweight macOS menu bar assistant with sleep prevention

## Acknowledgments

The author lacks the ability to develop and debug OpenCore, and completed this project by researching publicly available information and referencing the following projects. Thanks for their help.

- 🎉 <https://github.com/xjn819/Hackintosh-Deskmini310-EFI>
- 🎉 <https://github.com/hackintosh-club/ASRock-DeskMini-310>
