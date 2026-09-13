# ColorOSDepthMask

ColorOSDepthMask 是一个用于 **ColorOS 锁屏景深壁纸** 的 LSPosed 模块。

它可以让你自己控制景深蒙版，不再只能依赖系统 AI 自动抠图。

## 实际界面

<table>
  <tr>
    <td align="center" width="33%">
      <a href="screenshots/lockscreen-editor.jpg"><img src="screenshots/lockscreen-editor.jpg" alt="ColorOS 锁屏编辑页中的蒙版入口" width="260"></a><br>
      <sub>锁屏编辑页：在原生「景深」旁加入「蒙版」入口</sub>
    </td>
    <td align="center" width="33%">
      <a href="screenshots/mask-panel.jpg"><img src="screenshots/mask-panel.jpg" alt="ColorOSDepthMask 蒙版功能面板" width="260"></a><br>
      <sub>蒙版面板：自动生成、编辑、导入、导出；可反转或删除当前自定义蒙版</sub>
    </td>
    <td align="center" width="33%">
      <a href="screenshots/mask-editor.jpg"><img src="screenshots/mask-editor.jpg" alt="ColorOSDepthMask 蒙版编辑器" width="260"></a><br>
      <sub>蒙版编辑器：直接在真实壁纸上使用画笔、橡皮、撤销和重做</sub>
    </td>
  </tr>
</table>

## 有什么用

你可以：

- 在 ColorOS 锁屏壁纸编辑页直接打开 **蒙版** 功能；
- 调用 ColorOS 自带 AI 自动生成景深蒙版；
- 在真实壁纸上手工修改蒙版；
- 在原生支持 3D 与景深的壁纸上使用自定义蒙版，晃动时让选区随人物表面运动；
- 导入自己制作的 PNG 蒙版；
- 将当前有效蒙版导出为 PNG，并通过系统文件选择器指定保存位置和文件名；
- 使用画笔 / 橡皮、撤销 / 重做、缩放和平移继续修边；
- 每张壁纸单独保存自己的蒙版；
- 手动控制景深开关；
- 保存后立即刷新最新景深预览，不先闪回旧蒙版；
- 最终应用时使用当前最新的自定义蒙版，而不是旧的 AI 结果或之前缓存的旧结果。

独立 App 还可以：

- 查看当前版本、ColorOS、Wallpapers、Root 等信息；
- 一键重载 Wallpapers / SystemUI；
- 检查 GitHub Release 更新并下载安装；
- 一键复制诊断信息；
- 打开项目仓库、Releases、Issues 和开发者 Blog。

## 兼容环境

当前版本已针对多代 ColorOS Wallpapers 内部结构做兼容，不再只绑定单一 16.10.x 方法名。已使用真实 APK 验证 `com.oplus.wallpapers` **15.06.0102 至 16.10.0082**，共覆盖 20 个不同版本，包括 15.08 / 15.09 / 15.10、16.02 / 16.03 / 16.04 / 16.05 的多个小版本与 SP 构建；另外还验证了一个 DEX/resources 不同、但同样标记为 `16.03.0021` 的第二 APK 构建。

运行环境：

- Android 15（API 35）及以上；
- LSPosed；
- KernelSU / KernelSU Next / Magisk / APatch 等 Root 环境。

模块会根据 Wallpapers 的实际 class / method 结构选择兼容路径，而不是只判断版本号。未来系统或 Wallpapers 更新如果再次改变内部 ABI，仍可能需要继续适配。

v0.1.6 还修复了多版本景深数据 getter、补边高度、ThemeEdit 状态、AI fallback 与删除后旧景深残留问题，并对上述 20 个真实构建重新执行了 release/R8 兼容回归。

v0.1.7 新增 **蒙版导出**：通过 Android 标准“创建文件”流程将当前蒙版保存到指定位置；未开启反转时原样导出当前 PNG，开启反转时会把当前有效反转烘焙进导出 PNG，并增加导出 revision 一致性保护。

## v0.1.9：视频 / 动态壁纸景深

本版新增 **视频 / 动态壁纸蒙版与景深适配**。ColorOS 系统自带、视频本身已经包含 `[RGB | depth]` 的资源会继续使用原生 Olive/COE 播放管线；模块不会复制系统视频、不会把媒体类型强制改成其它类型，也不会为了关闭景深重新转码整段视频。

系统原生视频蒙版可以直接使用、逻辑删除和恢复；普通 VIDEO + 独立深度资源继续走 ColorOS 原生视频 depth 绑定。对没有厂商视频景深接口的旧 Wallpapers，模块会明确 fail-closed，不会猜接口或强行安装 Hook。

同时修复了景深 checkbox 因 ColorOS 内部 enabled / gray / listener 状态不同步而无法点击的问题，并保留自定义视频蒙版导入、编辑、删除与手动景深开关。

本版已经对 20 个不同 Wallpapers 版本执行逐版本兼容矩阵，其中 10 个 16.x 构建具备视频景深接口；另有一个不同 DEX/resources 的 `16.03.0021` 构建也单独通过检查。详细结果和测试边界见 [v0.1.9 兼容性验证](docs/compatibility-v0.1.9.md)。

如果你从公开的 v0.1.7 升级，v0.1.9 也包含下面 v0.1.8 章节中记录、此前尚未单独打公共 tag 的 3D 壁纸自定义景深功能。

## v0.1.8：3D 壁纸景深

本版把自定义蒙版接入 ColorOS 原生 3D 壁纸。进入“我的壁纸”的编辑预览时，会自动恢复保存的景深开关；导入或绘制的新蒙版保存后会刷新到预览中。选区的运动绑定人物表面，修复晃动时人物周围出现背景条带、错误盖住时钟的问题。

用户导入、手绘、擦除和反转后的选区会完整保留。修改预览通过内存更新纹理，保存或应用时才生成持久资源，并保留原始 3D 数据供以后重新编辑。

已针对以下四个同时具备原生 **3D 壁纸 + 景深** 能力的 Wallpapers 构建适配：

| Wallpapers 版本 | 3D 自定义景深 |
| --- | --- |
| `16.05.0012` | 支持 |
| `16.05.0023` | 支持 |
| `16.05.0030_sp8` | 支持 |
| `16.10.0082` | 支持 |

既有兼容清单中其余 16 个构建继续使用原来的静态景深路径。模块不会为没有原生 3D 编辑能力的 Wallpapers 增加 3D 壁纸生成能力。

详细测试范围和结果见 [v0.1.8 兼容性验证](docs/compatibility-v0.1.8.md)。跨版本验证使用真实 APK 的接口、状态方法和资源加载器；当前 `16.10.0082` 的预览及锁屏效果另经真机手持测试确认。其它系统版本的完整界面效果仍受对应 ROM、壁纸素材和锁屏模板影响。

## 安装

1. 从 Releases 下载最新版 APK：  
   <https://github.com/canxin121/ColorOSDepthMask/releases>

2. 安装 APK。

3. 在 LSPosed 中启用 **DepthMask**。

4. 作用域只选择：

   ```text
   com.oplus.wallpapers
   ```

5. 打开 DepthMask App，授予 Root 权限。

6. 点击首页里的 **重载壁纸**，让 Wallpapers 重新加载 Hook。

## 怎么用

### 自动生成蒙版

1. 打开 ColorOS 锁屏壁纸编辑页。
2. 选择一张支持景深的壁纸。
3. 点击底部的 **蒙版**。
4. 点击 **自动生成**。
5. 等待 ColorOS AI 生成蒙版。
6. 生成后可以继续手工编辑。
7. 点击保存。
8. 手动开启 **景深**。
9. 点击 **应用**。

### 编辑已有蒙版

1. 点击 **蒙版**。
2. 点击 **编辑**。
3. 使用画笔或橡皮修改。
4. 双指缩放 / 平移查看细节。
5. 点击保存。

保存后景深预览会自动刷新到最新蒙版，不需要退出 Wallpapers，也不会先显示旧蒙版再跳变。

### 导入 PNG 蒙版

1. 点击 **蒙版**。
2. 点击 **导入**。
3. 选择 PNG 文件。
4. 保存后手动开启景深。
5. 点击应用。

PNG 推荐和原始壁纸保持相同尺寸 / 比例。

### 导出 PNG 蒙版

1. 点击 **蒙版**。
2. 在已有自定义蒙版时点击 **导出蒙版**。
3. 系统会打开“创建文件”界面，可自行选择保存目录并修改 PNG 文件名。
4. 未开启 **反转** 时，导出的 PNG 与当前保存的原始蒙版一致；开启 **反转** 时，会把当前反转效果直接烘焙进导出 PNG，因此重新导入后仍保持相同的景深前景语义。
5. 如果选择文件期间蒙版被删除或修改，导出会拒绝写入旧 revision，避免导出错误版本。

蒙版规则：

- 白色 / 不透明：前景
- 黑色 / 透明：背景
- 灰色 / 半透明：过渡区域

## 自定义蒙版行为

从 v0.1.4 开始，只要当前壁纸存在自定义蒙版，它就会**直接替换 ColorOS 原生 AI 前景**。不再提供并集 / 交集模式，避免最终效果继续依赖一份不可控的系统 AI 蒙版。

现在蒙版面板只保留和“这份自定义蒙版本身”有关的操作：**自动生成、编辑、导入、导出、反转、删除蒙版**。界面里不再显示“替换”按钮，因为存在自定义蒙版时本身就固定是替换语义。

其中 **反转** 用来交换当前自定义蒙版的前景和背景；**删除蒙版** 会立即清除当前壁纸在编辑会话中的旧景深数据并关闭景深。删除后景深入口会保持不可用，上一份已删除的前景不会被继续预览、导出或应用；只有重新自动生成、导入或编辑保存一份新蒙版后，景深才会重新可用。

## 独立 App

App 有三个页面：

### 首页

用于：

- 查看当前版本；
- 检查更新；
- 查看 Root 状态；
- 重载 Wallpapers；
- 重载 SystemUI。

### 诊断

用于查看设备和模块信息。

遇到兼容性问题时，可以直接点击 **复制全部诊断信息**，再提交到 Issues。

### 项目

可以打开：

- GitHub：<https://github.com/canxin121>
- Blog：<https://blog.cxits.cn/>
- 项目仓库：<https://github.com/canxin121/ColorOSDepthMask>
- Releases：<https://github.com/canxin121/ColorOSDepthMask/releases>
- Issues：<https://github.com/canxin121/ColorOSDepthMask/issues>

## 遇到问题

如果出现：

- 蒙版按钮没有出现；
- 景深开关显示开启，但实际没有效果；
- 保存后最终锁屏和预览不一致；
- 系统更新后功能失效；

请先：

1. 确认 LSPosed 模块已启用；
2. 确认作用域是 `com.oplus.wallpapers`；
3. 在 DepthMask App 里执行一次 **重载壁纸**；
4. 如果仍有问题，到诊断页复制完整诊断信息并提交 Issue。

Issues：  
<https://github.com/canxin121/ColorOSDepthMask/issues>
