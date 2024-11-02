---
title: vmware与wsl不兼容问题解决方法
description: bcdedit /set hypervisorlaunchtype auto/off命令的使用
categories: [Linux, 虚拟化]
tags: [虚拟化]
image: 
    path: assets/img/bg/pic02.jpg
    lqip: assets/img/bg/pic02.jpg
---
在使用 VMware 与 Windows Subsystem for Linux（WSL）时，可能会遇到不兼容的问题。这种不兼容主要是由于两者都需要对系统的虚拟化技术进行控制和使用。当你尝试在同一台机器上同时运行 VMware 和 WSL 时，可能会遇到以下问题：

## 一、常见问题

1. **虚拟化冲突**：WSL 2 使用的是基于 Hyper-V 的虚拟化技术，而 VMware Workstation 需要独占访问硬件虚拟化功能。如果两者同时启用，可能会导致虚拟化冲突，使得其中一个无法正常工作。

2. **启动问题**：当 Hyper-V 启用时，VMware Workstation 可能无法启动虚拟机，或者启动后性能较差，甚至崩溃。

3. **性能问题**：即使能同时运行，性能可能会受到影响，因为两者会竞争同样的硬件资源。

## 二、解决方案

以下是一些可能的解决方案，可以帮助你在同一台机器上更好地使用 VMware 和 WSL：

### 1. 禁用 Hyper-V

如果你主要使用 VMware Workstation，可以考虑禁用 Hyper-V。这可以通过以下步骤完成：

- 打开“控制面板” -> “程序和功能” -> “启用或关闭 Windows 功能”。
- 找到并取消选中“Hyper-V”选项，重启系统。

### 2. 使用 WSL 1

WSL 1 不依赖 Hyper-V，可以和 VMware Workstation 一起使用。你可以将 WSL 2 切换到 WSL 1，方法如下：

打开 PowerShell，以管理员身份运行以下命令：

```powershell
wsl --set-version <distribution name> 1
```

### 3. 使用不同的虚拟化解决方案

如果必须使用 WSL 2 和虚拟机，可以考虑使用其他不依赖 Hyper-V 的虚拟化解决方案，例如 VirtualBox。

### 4. 双系统安装

如果需要频繁使用两者，可以考虑在同一台机器上安装双系统，一个用于 WSL，另一个用于 VMware。

### 5. 调整启动顺序

在某些情况下，先启动 WSL，然后启动 VMware Workstation，可能会有所帮助。你可以尝试不同的启动顺序，看看是否有改善。

## 三、Windows 11 上没有 Hyper-V 的解决方案

### 1. 检查 Hyper-V 支持

如果你的 Windows 11 版本支持 Hyper-V，可以通过以下步骤启用它：

- 确保你的 Windows 11 版本是专业版、企业版或教育版。
- 在命令提示符中运行以下命令，检查系统是否支持 Hyper-V：

```shell
systeminfo
```

如果看到 `Hyper-V Requirements` 部分显示“Virtualization Enabled In Firmware: Yes”，则表明系统支持 Hyper-V。

### 2. 在 BIOS 中启用虚拟化技术

- 进入 BIOS 设置（通常在启动时按 F2、Delete 或 Esc 键）。
- 在 BIOS 设置中找到“Intel Virtualization Technology”或“AMD-V”选项，并启用它。
- 保存设置并重启计算机。

### 3. 启用 Hyper-V

- 打开“控制面板” -> “程序和功能” -> “启用或关闭 Windows 功能”。
- 找到并勾选“Hyper-V”选项，然后点击“确定”。
- 重启计算机以应用更改。

### 4. 如果系统不支持 Hyper-V

如果你使用的是 Windows 11 家庭版，可以尝试以下方法之一：

- 升级到 Windows 11 专业版：你可以购买 Windows 11 专业版的升级密钥并升级系统，以便使用 Hyper-V。
- 使用 WSL 1：WSL 1 不依赖 Hyper-V，可以和 VMware Workstation 一起使用。
- 使用其他虚拟化解决方案：如 VirtualBox，它不依赖 Hyper-V，可以与 WSL 2 一起使用。

## 四、关于 `bcdedit /set hypervisorlaunchtype auto` 命令

### 1. 作用

`bcdedit /set hypervisorlaunchtype auto` 命令用于配置 Windows 启动管理器，以便在系统启动时自动加载 Hyper-V 虚拟化堆栈。

### 2. 使用场景

- **启用 Hyper-V**：当你需要在 Windows 上启用 Hyper-V 时，除了通过“启用或关闭 Windows 功能”来启用 Hyper-V 功能外，还需要确保 Hyper-V 虚拟化堆栈在启动时自动加载。
- **解决兼容性问题**：在某些情况下，Hyper-V 可能没有正确加载，导致虚拟机无法启动。运行这个命令可以确保 Hyper-V 在系统启动时被正确加载。

### 3. 如何使用

打开命令提示符或 PowerShell：以管理员身份运行。

执行命令：

```shell
bcdedit /set hypervisorlaunchtype auto
```

重启计算机：使更改生效。

### 4. 禁用 Hyper-V

如果你不需要使用 Hyper-V，可以将启动类型设置为 off：

```shell
bcdedit /set hypervisorlaunchtype off
```

### 5. 检查当前设置

你可以使用 `bcdedit` 查看当前的启动配置：

```shell
bcdedit /enum
```


通过这些命令，你可以控制 Hyper-V 虚拟化堆栈的加载行为，确保系统根据你的需求进行相应的配置。
