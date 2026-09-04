# ColorOSDepthMask

ColorOS 16 锁屏景深壁纸增强模块。

它通过 LSPosed 注入 `com.oplus.wallpapers`，允许你为不同壁纸分别管理景深蒙版：既可以直接调用 ColorOS 自带 AI 自动生成，也可以导入 PNG，并在真实壁纸底图上继续手工修整。独立 App 只负责 Root 状态、环境信息，以及快速重启壁纸进程 / SystemUI。

## 功能

- 在 ColorOS 锁屏壁纸编辑页加入 **蒙版** 入口。
- 每张壁纸独立保存自己的蒙版。
- 可直接调用 ColorOS 原生 AI 自动生成蒙版，并将系统抠图结果保存为当前壁纸蒙版。
- 可在当前真实壁纸底图上编辑蒙版：画笔 / 橡皮、笔刷大小、撤销 / 重做、双指缩放和平移。
- 仍可导入 Photoshop 等工具制作的 PNG 蒙版。
- 保存后的蒙版覆盖 ColorOS AI 抠图结果，AI 漏检时也可手工修正景深前景。
- 景深开关保持手动状态，不被 ColorOS 自动勾选、取消、禁用或灰显。
- 拖动、缩放、裁切壁纸后，手动开启的景深前景仍保持显示。
- 独立 App 可显示 Root / 模块 / 壁纸应用 / 设备信息。
- 独立 App 可一键重启 `com.oplus.wallpapers`，或同时重启壁纸进程与 SystemUI。

## 兼容性

当前主要针对以下环境开发与测试：

- ColorOS 16
- `com.oplus.wallpapers` 16.10.x
- Android 16
- LSPosed 兼容环境
- Root：Magisk / KernelSU / KernelSU Next / APatch 等提供 `su` 的方案

不同 ColorOS / Wallpapers 版本的内部类名与布局可能变化，因此系统更新后可能需要适配。

## 安装

1. 从本仓库的 **Releases** 下载最新 APK 并安装。
2. 在 LSPosed 中启用 **DepthMask** 模块。
3. 作用域只选择：

   ```text
   com.oplus.wallpapers
   ```

4. 打开 DepthMask App。App 会主动请求 Root，用于重启壁纸进程和 SystemUI。
5. 点击 **壁纸** 重载，或手动强行停止并重新打开系统壁纸应用，使 Hook 重新注入。

### Root 授权

DepthMask 使用 `libsu` 主动建立 root shell，因此 Magisk / APatch 等环境通常会在首次使用时弹出授权请求。

KernelSU / KernelSU Next 采用以 Root 管理器为准的白名单授权模型时，管理器可能不会弹出传统授权对话框。若 App 显示 **Root 未授权**，可直接点击 Root 卡片里检测到的管理器名称打开 Root 管理器，允许 DepthMask 后回到 App 点击 **重新请求**。

## 使用蒙版

1. 打开 ColorOS 的锁屏壁纸编辑页。
2. 在景深相关功能栏中点击 **蒙版**。
3. 在蒙版面板中选择一种方式：
   - **自动生成**：调用 ColorOS 原生 AI 抠图，生成完成后会自动进入编辑器。
   - **编辑**：直接在当前真实壁纸底图上编辑已有蒙版；若当前没有蒙版，则从空白蒙版开始。
   - **导入**：选择外部 PNG 蒙版，例如 Photoshop 制作的蒙版。
4. 编辑器中：
   - 单指使用画笔 / 橡皮修整前景区域。
   - 可调整笔刷像素大小。
   - 支持撤销 / 重做 / 重置本次编辑。
   - 双指缩放和平移，方便处理边缘细节。
5. 保存后，可根据需要调整 **替换 / 并集 / 交集** 和 **反转**。
6. 开启系统的 **景深** 开关并应用壁纸。

外部 PNG 规则仍然是：

- 白色 / 不透明：前景
- 黑色 / 透明：背景
- 灰色 / 半透明：羽化过渡

推荐外部 PNG 保持和原始壁纸相同的尺寸 / 比例。每张壁纸都有自己的蒙版绑定，切换到另一张壁纸不会复用上一张的蒙版。

## DepthMask App

App 本身保持简单，只提供：

- Root 授权状态
- 模块版本
- `com.oplus.wallpapers` 版本
- 设备 / Android 信息
- LSPosed 作用域信息
- **壁纸**：重启 `com.oplus.wallpapers`
- **SystemUI**：重启壁纸进程并重启 SystemUI

## 源码与构建

本 public 仓库只保存用户文档和 GitHub Actions 发布流程。实际源码保存在私有源码仓库中，Release 由 GitHub Actions 从私有源码仓库拉取、签名并构建。

本仓库不保存 ColorOS APK、反编译产物或其他逆向资源。
