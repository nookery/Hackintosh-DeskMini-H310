# Hackintosh-Deskmini310

🌐️ [English](./README.md) | 中文

## My Configuration

- 主板: ASROCK 310-com
- 硬盘: Fanxiang S500PRO 1TB
- CPU: i7-8700
- 内存: 16G DDR4 X2
- 鼠标: Magic Mouse
- 键盘: Magic Keyboard
- 显示器:
  - Dell 1080p，通过HDMI连接
  - Aoc 4K，通过DP连接
- 无线网卡: BCM94360CS2+转接卡

## 使用情况

自2019年以来，用于

- Web 开发，主要用 VSCode 编辑器，体验良好
- Xcode 开发，编译过程明显比 MacBook Air(M1,8G) 慢

目前系统版本为 14.8.7 (23J520)。

相比白苹果，不足的地方（可能是天线较差引起的）：

- 蓝牙鼠标、蓝牙键盘的响应速度稍慢
- 通用控制（一个鼠标控制两个电脑）失败率较高

2024年8月，因为我的工作对 CPU 的性能要求比较高（如果是普通办公，该 CPU 性能绰绰有余），它的工作被Mac mini M2 Pro取代。  

不过，它不但没有被抛弃，还有了更重要的使命：作为一台 All In One 小主机！

具体的情况记录在这里：  
<https://nookery.github.io/Deskmini-All-In-One/>

## BIOS设置

这部分内容来自：<https://github.com/xjn819/Hackintosh-Deskmini310-EFI>

- CPU Configuration, CPU C states Support, Enabled
- CPU Configuration, CPU C states Support, CFG Lock Disabled (必须）
- Chipset Configuration, Vt-d, Disabled
- Chipset Configuration, Onboard HD Audio: Enabled
- USB Configuration, XHCI Hand-off, Enabled  （关键）
- Super IO Configuration, Serial Port, Disabled（必须）
- Security Secure Boot, Disabled(by default)
- Boot, CSM, disabled
- BIOS版本必须在4.0及以上。

## Sequoia

未测试。

## Sonoma

### 步骤

- 先更改 OpenCore 配置文件中的三码
- 安装系统
- 进系统后需要安装 OpenCore-Patcher，Wi-Fi 才能正常
  - 下载 OpenCore-Patcher
  - 点击 “Post-Install Root Patch” 按钮
  - 按提示安装后重启

### 异常的功能

- 连接某些 Wi-Fi，会导致蓝牙鼠标卡顿（可能是某些频段的 Wi-Fi 信号干扰蓝牙）
- HDMI 接4K显示器花屏（所以我的4K显示器接了 DP 接口）

### Intel UHD 630 GPU 卡死调查（2026 年 11 月）

测试环境为 macOS 14.8.7 (23J520)、OpenCore 0.9.9、Lilu 1.6.7、WhateverGreen 1.6.6 和 VirtualSMC 1.3.2。i7-8700 的 UHD 630 使用 `AAPL,ig-platform-id = 07009B3E`，并将设备 ID 仿冒为 `0x3E9B`。

五份 GPU Reset 报告具有相同的故障签名：

- 进程：`mediaanalysisd`
- 调度器：`Host Preemptive`
- 卡死引擎：`RCS Ring`
- 最后执行的指令头：`0x7a000004`

反复出现的故障顺序如下：

1. `mediaanalysisd` 在屏幕睡眠或 Power Nap 期间启动文件系统分析活动。
2. Vision TextRecognition 处理多个开发目录中的 Draw.io PNG 图集。卡死前的日志出现 `Failed to obtain IOSurface; falling back to rendering path`。
3. Intel RCS 引擎停止响应，macOS 生成 GPU Reset 报告。
4. 随后的睡眠状态转换在 `com.apple.iokit.IOGraphicsFamily` 中超时 180 秒，最终触发内核 panic。

使用仅 CPU 的 `VNRecognizeTextRequest` 对一张反复被扫描的 PNG 进行识别，请求成功完成。这只能证明图片可读取、该 CPU 请求可以完成，不能证明 Vision 的所有 GPU 计算路径都正常。

目前证据更倾向于 Apple Vision/TextRecognition 与 Intel 图形驱动路径存在缺陷或兼容性问题，而不是 OpenCore 引导器本身发生故障。黑苹果的设备 ID、framebuffer 和电源管理差异仍可能影响故障是否被触发；尚未在真正的 Macmini8,1 上进行对照测试。

以下方案均**不是已验证的修复**：

- `-disablegfxfirmware`：强制使用 Host Preemptive 调度并避免了早期的 GuC 固件异常，但同样的 RCS 卡死仍然发生，因此 GuC 不是唯一根因。
- `-igfxgl=1`：该参数已出现在 `boot-args` 中，但 `MetalPluginName` 仍然存在，并且发生了相同的 GPU 卡死。它在这台机器上没有实际禁用 Metal，不能当作有效方案。
- Lilu 1.7.2 和 WhateverGreen 1.7.1：目前可查的更新说明没有提到针对此故障签名的修复，因此不能把升级视为已验证的解决方案。

作为临时止血措施，已在受影响的电脑上禁用并停止 `mediaanalysisd`：

```sh
launchctl disable gui/$(id -u)/com.apple.mediaanalysisd
launchctl bootout gui/$(id -u)/com.apple.mediaanalysisd
```

已确认禁用状态生效且进程不再运行。这可以阻断已经证实的崩溃触发源，但也会暂停后台文件系统和照片 OCR、人脸识别及场景分析。它并没有修复图形驱动，而且这项针对本机的止血措施不包含在本项目的 EFI 文件中。

以后测试新的修复方案时，可以使用以下命令恢复后台媒体分析：

```sh
launchctl enable gui/$(id -u)/com.apple.mediaanalysisd
launchctl bootstrap gui/$(id -u) /System/Library/LaunchAgents/com.apple.mediaanalysisd.plist
```

诊断报告位于 `/Library/Logs/DiagnosticReports/Kernel_*.gpuRestart` 和 `/Library/Logs/DiagnosticReports/Kernel-*.panic`。

## Ventura

### 步骤

- 先更改 OpenCore 配置文件中的三码
- 安装系统

### 异常的功能

暂未发现

## 用这台电脑开发的项目

- [Cisum 音乐播放器](https://github.com/CofficLab/Cisum) — Apple 平台音乐播放器
- [TravelMode（Netto）](https://github.com/CofficLab/Netto) — macOS 应用联网控制工具
- [GitOK](https://github.com/CofficLab/GitOK) — macOS 项目管理工具
- [Lumi](https://github.com/CofficLab/Lumi) — AI 驱动的 macOS 桌面助手
- [Wakey](https://github.com/CofficLab/Wakey) — 轻量的 macOS 菜单栏状态助手，支持防休眠

## 致谢

作者没有开发调试 OpenCore 的能力，而是通过查询网络公开资料和参考以下项目来完成本项目，感谢他们提供的帮助。

- 🎉 <https://github.com/xjn819/Hackintosh-Deskmini310-EFI>
- 🎉 <https://github.com/hackintosh-club/ASRock-DeskMini-310>
