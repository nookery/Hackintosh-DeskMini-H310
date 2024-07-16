# Hackintosh-Deskmini310

## 最新情况

2024年8月，因为CPU有些落后，它的工作被Mac mini M2 Pro取代。  

不过，它不但没有被抛弃，还有了更重要的使命：作为一台 All In One 小主机！   

具体的情况记录在这里：  
<https://cofficlab.github.io/zh/all-in-one/all-in-one.html>

## 我的配置

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

- Web 开发，主要用 VSCode 编辑器
- Xcode 开发，编译过程明显比 MacBook Air(M1,8G) 慢

目前系统版本14.4 (23E214)。

相比白苹果，不足的地方：
- 蓝牙鼠标、蓝牙键盘的响应速度稍慢
- 通用控制（一个鼠标控制两个电脑）失败率较高

## BIOS设置

  - CPU Configuration, CPU C states Support, Enabled
  - CPU Configuration, CPU C states Support, CFG Lock Disabled (必须）
  - Chipset Configuration, Vt-d, Disabled
  - Chipset Configuration, Onboard HD Audio: Enabled
  - USB Configuration, XHCI Hand-off, Enabled  （关键）
  - Super IO Configuration, Serial Port, Disabled（必须）
  - Security Secure Boot, Disabled(by default)
  - Boot, CSM, disabled
  - BIOS版本必须在4.0及以上。

## Sonoma

### 步骤

- 先更改 OpenCore 配置文件中的三码
- 安装系统
- 进系统后需要安装 OpenCore-Patcher，Wi-Fi 才能正常
  - 下载 OpenCore-Patcher
  - 点击 “Post-Install Root Patch” 按钮
  - 按提示安装后重启

### 异常的功能

- 连接某些 Wi-Fi，会导致蓝牙鼠标卡顿
- HDMI接4K显示器花屏

## Ventura

### 步骤

- 先更改 OpenCore 配置文件中的三码
- 安装系统

### 异常的功能

暂未发现

## 致谢

作者没有开发调试 OpenCore 的能力，而是通过查询网络公开资料和以下项目来完成本项目，感谢他们提供的帮助。

- 🎉 <https://github.com/xjn819/Hackintosh-Deskmini310-EFI>
- 🎉 <https://github.com/hackintosh-club/ASRock-DeskMini-310>
