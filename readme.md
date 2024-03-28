# Hackintosh-Deskmini310

## 我的配置

- 主板: ASROCK 310-com
- 硬盘: Fanxiang S500PRO 1TB
- CPU: i7-8700
- 无线网卡: BCM94360CS2+转接卡
- 内存: 16G DDR4 X2

## 使用情况

自2019年以来，用于

- Web 开发，主要用 VSCode 编辑器
- Xcode 开发，编译过程明显比 MacBook Air(M1,8G) 慢

目前系统版本14.4 (23E214)。

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

## Ventura

### 步骤

- 先更改 OpenCore 配置文件中的三码
- 安装系统

### 异常的功能

暂未发现
