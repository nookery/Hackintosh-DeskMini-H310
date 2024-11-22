# Hackintosh-Deskmini310

[中文](./zh_CN.md)

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

Current system version 14.4 (23E214).

Compared to genuine Apple computers, shortcomings (possibly due to inferior antenna):

- Slightly slower response time for Bluetooth mouse and keyboard
- Universal Control (controlling two computers with one mouse) has high failure rate

In August 2024, due to my work requiring higher CPU performance (the CPU would be more than sufficient for regular office work), it was replaced by a Mac mini M2 Pro.

However, it wasn't abandoned but given an even more important mission: serving as an All In One mini computer!

Details are recorded here:
<https://cofficlab.github.io/zh/all-in-one/all-in-one.html>

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

## Ventura

### Steps

- First modify the three system identifiers in OpenCore configuration file
- Install the system

### Known Issues

None found yet

## Projects Developed on This Computer

- [JuiceEditor Rich Text Editor](https://github.com/CofficLab/JuiceEditor)
- [Apple Platform Player](https://github.com/CofficLab/Cisum_SwiftUI)
- [Build-a-Website-with-Laravel](https://github.com/nookery/Build-a-Website-with-Laravel)

## Acknowledgments

The author lacks the ability to develop and debug OpenCore, and completed this project by researching publicly available information and referencing the following projects. Thanks for their help.

- 🎉 <https://github.com/xjn819/Hackintosh-Deskmini310-EFI>
- 🎉 <https://github.com/hackintosh-club/ASRock-DeskMini-310>
