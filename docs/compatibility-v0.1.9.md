# ColorOSDepthMask v0.1.9 兼容性验证

版本：**0.1.9 / versionCode 60**。

## 发布包

- 应用 ID：`dev.coloros.depthmask`
- APK：`ColorOSDepthMask-v0.1.9.apk`
- APK 大小：`3114804` bytes
- APK SHA-256：`9a749440bec3aeca8634052d62b2a4304e7170083637b7f4779df8e541dc315a`
- 签名证书 SHA-256：`b389cf6e91508e445a0be2e28b981cf0a78b9e3531b40bc017ab28b87f4752ef`
- APK Signature Scheme v2：通过
- 对应 private source tag：`v0.1.9`
- 对应 private source commit：`ca23b3e800e441839ff53eb0aa2c9978663d6684`

## 本版兼容结论

v0.1.9 在既有静态景深与 3D 自定义景深基础上加入视频/动态壁纸景深适配，并重新审计 ColorOS 16.10 的
Olive/COE 播放链。系统自带 `[RGB | depth]` packed 视频继续由 ColorOS 原生管线解码：模块不会复制系统
视频、不会改写 `playableMediaType`，也不会为了关闭景深重新转码一份 RGB-only 视频。普通 VIDEO + 独立
深度资源则继续使用 ColorOS 原生视频 depth 绑定路径。

本次对 **20 个不同 Wallpapers 版本**都定义了明确能力与精确 ABI 期望，并额外验证了一个 DEX/resources
不同、但同样标记为 `16.03.0021` 的第二 APK 构建。

| Wallpapers | 静态景深 | 视频景深 | 原生 3D 自定义景深 |
| --- | --- | --- | --- |
| `15.06.0102` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.08.0022` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.08.0022_sp6` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.08.0026` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.08.0027` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.09.0036` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.09.0040` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.09.0041` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.09.0043` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `15.10.0004` | 支持 | 不支持（明确 fail-closed） | 不支持 |
| `16.02.0027_sp2` | 支持 | 支持 | 不支持 |
| `16.02.0036_sp2` | 支持 | 支持 | 不支持 |
| `16.03.0021` | 支持 | 支持 | 不支持 |
| `16.04.0005_sp2` | 支持 | 支持 | 不支持 |
| `16.04.0005_sp6` | 支持 | 支持 | 不支持 |
| `16.04.0005_sp7` | 支持 | 支持 | 不支持 |
| `16.05.0012` | 支持 | 支持 | 支持 |
| `16.05.0023` | 支持 | 支持 | 支持 |
| `16.05.0030_sp8` | 支持 | 支持 | 支持 |
| `16.10.0082` | 支持 | 支持 | 支持 |

其中视频景深接口存在于 `16.02.0027_sp2` 至 `16.10.0082` 的 10 个受测构建；15.x 的受测版本明确验证为
没有该厂商能力，模块会 fail-closed，不会误装视频 Hook。原生 3D 自定义景深只在以下四个受测构建启用：
`16.05.0012`、`16.05.0023`、`16.05.0030_sp8`、`16.10.0082`。

## 自动验证结果

最终发布源码和签名 release payload 完成：

- JVM / Robolectric：**163 tests，0 failures，0 errors，1 skipped**；
- Android lint：**0 Error / Fatal**；
- 系统视频架构不变量：**18 项通过**；
- 20 个固定真实 Wallpapers APK：**319 条静态景深 / 3D ABI 检查通过**；
- 视频 ABI / ColorOS 语义：**392 条通过**；
- 20 / 20 版本的隔离 Android 静态景深 runtime：通过；
- 视频 runtime：最终 R8 release 共 **100 条检查通过**；
- 4 / 4 原生 3D 版本：真实 mesh / Bitmap / 缓存、最终 R8 profile resolver、R8 scene + 厂商 mesh parser
  preview/export 等价检查通过；
- 第二个 `16.03.0021` 构建：额外 17 项构建检查，并通过静态景深与最终 R8 视频 runtime。

历史厂商 APK 在测试中只通过隔离 `app_process` / `PathClassLoader` 加载，**没有安装到用户系统**。

为让公开 CI 能复现同一个 APK，release build 不包含 AGP 的 Git VCS metadata。完整 Android runtime 矩阵所测
payload 与最终公开 APK 的所有运行相关 ZIP entry（包括 `classes.dex`、manifest、resources、native libs）
逐字节一致；最终 APK 只移除了非运行时的 `META-INF/version-control-info.textproto`。

## 验证边界

上述结果证明的是：模块的 ABI resolver、R8 后代码、状态机与资源格式可以和这些保存的真实 Wallpapers APK
正确匹配。它**不等同于**在 20 套历史 ColorOS ROM 上逐一人工操作所有 OEM UI、Surface、GPU、系统服务和
锁屏模板组合。未来 Wallpapers/ROM 再次改变内部 ABI，仍可能需要继续适配。
