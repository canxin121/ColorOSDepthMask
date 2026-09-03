# ColorOSDepthMask

ColorOS 16 锁屏景深壁纸增强模块。

它通过 LSPosed 注入 `com.oplus.wallpapers`，允许你为不同壁纸分别指定手工 PNG 蒙版，并让景深开关保持真正的手动控制。独立 App 只负责 Root 状态、环境信息，以及快速重启壁纸进程 / SystemUI。

## 功能

- 在 ColorOS 锁屏壁纸编辑页加入 **蒙版** 入口。
- 每张壁纸独立绑定自己的 PNG 蒙版。
- 手工蒙版覆盖 ColorOS AI 抠图结果，AI 漏检时也可生成景深前景。
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

KernelSU / KernelSU Next 采用以 Root 管理器为准的白名单授权模型时，管理器可能不会弹出传统授权对话框。若 App 显示 **Root 未授权**，请在对应 Root 管理器里允许 DepthMask，然后回到 App 点击 **重新请求**。

## 使用蒙版

1. 用 Photoshop 或其他图像工具制作与壁纸对应的 PNG 蒙版。
2. 推荐保持和原始壁纸相同的尺寸 / 比例。
3. 蒙版规则：
   - 白色 / 不透明：前景
   - 黑色 / 透明：背景
   - 灰色 / 半透明：羽化过渡
4. 打开 ColorOS 的锁屏壁纸编辑页。
5. 在景深相关功能栏中点击 **蒙版**。
6. 选择 PNG。
7. 根据需要调整 **替换 / 并集 / 交集** 和 **反转**。
8. 开启系统的 **景深** 开关并应用壁纸。

每张壁纸都有自己的蒙版绑定，切换到另一张壁纸不会复用上一张的蒙版。

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
