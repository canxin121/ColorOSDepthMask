# ColorOSDepthMask

> 为 ColorOS 锁屏壁纸编辑器补上可控、可保存、可编辑的景深蒙版能力。

ColorOSDepthMask 是一个面向 **ColorOS 16 / `com.oplus.wallpapers` 16.10.x** 的 LSPosed 模块，同时附带一个独立控制 App。

它的核心目标不是重新实现一套景深系统，而是尽量复用 ColorOS 自己的景深链路，同时把系统原本不可控的部分变成可编辑、可保存、可恢复、可手动控制的状态。

你可以直接调用 ColorOS 原生 AI 生成景深蒙版，也可以导入外部 PNG，再在真实壁纸底图上继续修整。保存后的自定义蒙版会参与预览和最终锁屏资源生成，并按壁纸独立保存。

---

## 这项目解决什么问题

ColorOS 的锁屏景深功能依赖系统内部的 AI 抠图和一组状态字段。实际使用中常见的问题包括：

- AI 漏检、误检，前景轮廓不够准确；
- 系统生成的蒙版无法方便地继续手工修改；
- 同一张壁纸重新进入编辑器后，自定义结果可能丢失；
- 景深按钮会被系统 AI 状态自动勾选、取消、禁用或灰显；
- 预览正确，但最终应用到锁屏后使用了旧的 AI 前景；
- 保存蒙版后，“应用”按钮仍是灰色，必须额外滑动一下壁纸才可应用。

ColorOSDepthMask 的作用，就是把这整条链路重新收口到“**当前壁纸 + 当前自定义蒙版 + 用户手动景深状态**”。

---

## 核心功能

### 自定义景深蒙版

在 ColorOS 锁屏壁纸编辑页加入 **蒙版** 入口。

支持三种来源：

- **自动生成**：直接调用 ColorOS 原生 AI 抠图；
- **编辑**：在真实壁纸底图上继续修整当前蒙版；
- **导入**：选择外部 PNG 蒙版，例如 Photoshop / Affinity Photo / GIMP 制作的蒙版。

### 蒙版编辑器

内置编辑器支持：

- 画笔 / 橡皮；
- 笔刷大小调节；
- 撤销 / 重做；
- 重置当前编辑；
- 单指绘制；
- 双指缩放与平移；
- 真实壁纸作为编辑底图；
- 保存后立即刷新当前景深预览。

### 蒙版组合

保存蒙版后可选择：

- **替换**：完全使用自定义蒙版；
- **并集**：自定义蒙版与系统结果合并；
- **交集**：只保留两者同时存在的区域；
- **反转**：反转当前蒙版前景 / 背景关系。

### 每张壁纸独立保存

蒙版不会只绑定到某个临时主题目录，而是会尽量根据壁纸内容、ColorOS 模型字段和已应用景深资源恢复稳定关联。

因此同一张壁纸重新进入编辑页后，已保存的蒙版可以继续使用；切换到其他壁纸时也不会错误复用上一张壁纸的蒙版。

### 手动景深开关

存在自定义蒙版时，景深开关以用户操作为准。

模块会同步 ColorOS 内部与景深相关的状态，避免出现：

```text
按钮显示：开启
实际景深：关闭
```

或：

```text
预览：自定义蒙版
最终锁屏：旧 AI 蒙版
```

拖动、缩放、裁切、重新进入编辑页后，也会继续保持当前手动状态和自定义前景。

### 最终锁屏资源同步

最终点击“应用”时，模块会参与 ColorOS 的锁屏景深资源生成流程，确保当前自定义蒙版写入最终景深资源，而不是复用旧主题 / 旧 AI 前景。

保存蒙版后也会正确触发 ColorOS 自己的“已编辑”状态，因此不再需要额外滑动壁纸才能让“应用”按钮解灰。

---

## 独立 App

DepthMask App 不是单纯的模块入口，而是一个小型控制中心。

### 首页

首页只保留高频操作：

- 当前模块版本；
- `com.oplus.wallpapers` 版本；
- Root 状态；
- ColorOS / Android 摘要；
- GitHub Release 更新状态；
- 一键重载 Wallpapers；
- 一键重载 SystemUI。

### 诊断

诊断页按分组折叠显示，避免首页变成超长列表。

可查看并复制：

- 模块 `versionName / versionCode`；
- 包名；
- build type；
- compileSdk / targetSdk / minSdk；
- 安装来源；
- 模块签名 SHA-256；
- LSPosed 作用域；
- Wallpapers 版本 / versionCode / 更新时间 / 签名；
- 设备 Manufacturer / Model / Brand / Product / Device / Hardware；
- Android / API / Security Patch；
- ColorOS 版本；
- Kernel；
- ABI；
- Build ID / incremental；
- Android fingerprint；
- Root 管理器 / Root shell；
- LSPosed 版本；
- SELinux；
- Verified Boot；
- Boot slot。

每一项都可以单独复制，也支持一键复制整份诊断信息，方便提交 Issue 或排查兼容性问题。

### 项目

项目页包含独立可点击入口：

- 开发者 GitHub：<https://github.com/canxin121>
- 个人 Blog：<https://blog.cxits.cn/>
- 项目仓库：<https://github.com/canxin121/ColorOSDepthMask>
- Releases：<https://github.com/canxin121/ColorOSDepthMask/releases>
- Issues：<https://github.com/canxin121/ColorOSDepthMask/issues>

手机使用底部导航，宽屏 / 横屏布局会自动切换为侧边导航。

---

## 自动更新

DepthMask App 会在启动后检查本仓库最新的 GitHub Release。

发现更高版本时，可以直接在 App 内下载 APK。

下载完成后会进行校验：

1. GitHub Release 提供的 SHA-256（若存在）；
2. APK 包名；
3. `versionCode`；
4. `versionName`；
5. APK 签名证书。

全部通过后才会交给 Android 系统安装器。

首次从 App 内安装更新时，Android 可能要求允许 DepthMask **安装未知应用**。

> 更新器只接受签名与当前已安装版本一致的 APK。

---

## 兼容性

当前主要开发和实测环境：

| 项目 | 当前主要环境 |
| --- | --- |
| 系统 | ColorOS 16 |
| Android | Android 16 |
| Wallpapers | `com.oplus.wallpapers` 16.10.x |
| LSPosed | LSPosed 兼容环境 |
| Root | KernelSU / KernelSU Next / Magisk / APatch 等 |

ColorOSDepthMask 依赖 `com.oplus.wallpapers` 的内部类、字段和资源。

**系统更新、Wallpapers 更新或 ColorOS 内部实现变化都可能导致 Hook 失效。**

如果更新系统后发现蒙版入口消失、景深状态异常或最终锁屏不一致，请在 Issue 中附上 App 的完整诊断信息，并注明当前 Wallpapers 版本。

---

## 安装

### 1. 下载 APK

从本仓库 Releases 下载最新版：

<https://github.com/canxin121/ColorOSDepthMask/releases>

### 2. 安装并启用 LSPosed 模块

安装 APK 后，在 LSPosed 中启用 **DepthMask**。

作用域只选择：

```text
com.oplus.wallpapers
```

不需要给 SystemUI、Launcher 或其他包添加作用域。

### 3. 重启 Wallpapers

可以直接打开 DepthMask App，在首页使用：

- **重载壁纸**

或者手动强行停止 `com.oplus.wallpapers` 后重新打开系统壁纸编辑器。

如果刚升级模块，建议先重载一次 Wallpapers，确保新 Hook 被重新加载。

---

## Root 授权

独立 App 使用 Root 的主要场景：

- 重启 `com.oplus.wallpapers`；
- 重启 SystemUI；
- 读取更多系统诊断信息。

DepthMask 使用 `libsu` 获取 Root shell。

Magisk / APatch 等环境通常会在首次使用时显示授权请求。

KernelSU / KernelSU Next 更依赖 Root 管理器自身的授权列表。如果 App 显示 **Root 未授权**：

1. 打开对应 Root 管理器；
2. 找到 DepthMask；
3. 允许 Root；
4. 回到 DepthMask App；
5. 点击重新检测 / 请求 Root。

LSPosed 模块本身的核心 Hook 能力和 Root shell 是两件不同的事：Root 未授权主要影响独立 App 的重载与诊断能力。

---

## 使用方法

### 自动生成蒙版

1. 打开 ColorOS 锁屏壁纸编辑页；
2. 选择支持景深的壁纸；
3. 点击底部功能栏里的 **蒙版**；
4. 点击 **自动生成**；
5. 等待 ColorOS AI 完成抠图；
6. 生成完成后可直接进入编辑器继续修整；
7. 保存；
8. 根据需要调整“替换 / 并集 / 交集 / 反转”；
9. 手动开启景深；
10. 点击应用。

### 编辑已有蒙版

1. 点击 **蒙版**；
2. 点击 **编辑**；
3. 使用画笔 / 橡皮修改；
4. 双指缩放查看边缘；
5. 保存。

保存后预览会自动刷新，无需退出 Wallpapers。

### 导入外部 PNG

外部 PNG 推荐与原始壁纸保持相同尺寸和比例。

蒙版语义：

| 蒙版内容 | 含义 |
| --- | --- |
| 白色 / 不透明 | 前景 |
| 黑色 / 透明 | 背景 |
| 灰色 / 半透明 | 羽化过渡 |

导入后默认使用：

```text
模式：替换
反转：关闭
```

这样可以避免继承上一份蒙版的组合模式导致实际前景为空。

---

## 常见问题

### 蒙版按钮没有出现

先确认：

- 模块已在 LSPosed 中启用；
- 作用域包含 `com.oplus.wallpapers`；
- 安装 / 更新模块后已经重启 Wallpapers；
- 当前 Wallpapers 版本仍在兼容范围内。

可以先在 DepthMask App 首页执行一次 **重载壁纸**。

### 景深按钮显示开启，但实际没有景深

当前版本会主动同步：

- 景深按钮内部状态；
- ColorOS 景深范围开关；
- 纵向 / 横向景深显示状态；
- `supportDepth`；
- 当前 / 缓存 depth bitmap；
- 实际 DepthView 可见性。

如果仍然发生，请复制完整诊断信息并提交 Issue。

### 保存蒙版后“应用”按钮是灰色

当前版本已经接入 ColorOS 自己的用户编辑状态，正常情况下保存 / 导入 / 自动生成 / 修改组合模式后都会自动解灰。

如果仍需额外滑动壁纸才可应用，请附上 Wallpapers 版本和完整诊断信息。

### 重新进入同一张壁纸后蒙版丢失

当前版本会综合使用：

- ColorOS 模型字段；
- 壁纸内容身份；
- RGB 指纹；
- 已应用景深资源；

恢复之前保存的蒙版。

如果某个特定系统版本仍无法恢复，请保留现场，不要先删除旧蒙版，再提交 Issue。

### 最终锁屏和预览不一致

模块会在最终 ColorOS 锁屏资源生成阶段重新写入当前自定义蒙版。

如果最终锁屏仍出现旧 AI 前景，请附上：

- 模块版本；
- Wallpapers 版本；
- ColorOS 版本；
- 完整诊断信息；
- 问题出现前的操作顺序。

---

## 权限、联网与隐私

DepthMask 本身没有账号系统，也不会自动上传诊断信息。

独立 App 可能使用：

- **Internet**：访问 GitHub Releases API 和下载公开 Release APK；
- **安装未知应用**：仅用于把已经校验过的更新 APK 交给系统安装器；
- **Root**：用于重启 Wallpapers / SystemUI 和读取额外系统诊断。

“复制完整诊断信息”只会写入本机剪贴板，由用户自行决定是否发送。

不会自动上传：

- 壁纸图片；
- 蒙版图片；
- 剪贴板内容；
- Root 文件；
- 设备上的其他私人文件。

---

## 发布与源码

这个 public 仓库保存：

- 用户文档；
- Release 页面；
- GitHub Actions 发布流程。

实际 Android 源码保存在私有源码仓库中。

正式 Release 由 GitHub Actions：

1. 拉取指定私有源码 commit；
2. 使用正式签名构建 Release APK；
3. 验证版本；
4. 上传到本 public 仓库的 GitHub Release。

public 仓库不会提交：

- ColorOS APK；
- SystemUI APK；
- JADX / 反编译产物；
- 实机数据库；
- 壁纸资源；
- 用户蒙版；
- 其他运行时分析文件。

---

## 反馈问题

Issue：

<https://github.com/canxin121/ColorOSDepthMask/issues>

提交问题时建议包含：

- 问题现象；
- 复现步骤；
- DepthMask 完整诊断信息；
- 当前 ColorOS / Android / Wallpapers 版本；
- 是否在系统更新后开始出现；
- 必要时附上截图或录屏。

请不要公开上传包含私人内容的壁纸或系统文件。

---

## 链接

- Releases：<https://github.com/canxin121/ColorOSDepthMask/releases>
- Issues：<https://github.com/canxin121/ColorOSDepthMask/issues>
- GitHub：<https://github.com/canxin121>
- Blog：<https://blog.cxits.cn/>

---

## 说明

ColorOSDepthMask 是针对特定 ColorOS 内部实现的实验性增强模块。

系统组件的内部 API 没有稳定兼容性保证，请在系统 / Wallpapers 大版本更新后留意 Release 和兼容性说明。
