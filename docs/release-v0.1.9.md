# ColorOSDepthMask v0.1.9

v0.1.9 是一次较大的景深壁纸兼容更新，重点加入 **视频 / 动态壁纸景深**，并整合此前已完成的 **3D 壁纸自定义景深**。

## 主要变化

- 新增视频 / 动态壁纸蒙版与景深适配；
- ColorOS 系统自带 packed `[RGB | depth]` 视频直接使用原生 Olive/COE 管线，不复制、不重编码、不改媒体类型；
- 支持系统视频原生蒙版的逻辑删除与恢复，不破坏原始视频资源；
- 普通 VIDEO + 独立深度资源继续走 ColorOS 原生视频景深绑定；
- 修复景深开关不可点击、内部 enabled/gray 状态不同步等问题；
- 保留自定义视频蒙版导入、编辑、删除与手动景深开关；
- 包含此前 README 中记录的 v0.1.8 3D 壁纸自定义景深能力；
- 扩展并固化 20 个 Wallpapers 版本的逐版本 ABI/runtime 兼容矩阵。

如果你从公开的 v0.1.7 升级，本版同时包含此前尚未单独打公共 tag 的 3D 功能更新。

## 兼容性

自动验证覆盖 20 个不同 `com.oplus.wallpapers` 版本（`15.06.0102` ～ `16.10.0082`），以及一个额外的
`16.03.0021` 不同 APK 构建。视频景深在 10 个具备厂商接口的 16.x 构建启用；旧版本没有对应能力时会
fail-closed。原生 3D 自定义景深支持 4 个已验证构建。

完整范围、每版本能力表与测试边界见：
[**v0.1.9 兼容性验证**](https://github.com/canxin121/ColorOSDepthMask/blob/v0.1.9/docs/compatibility-v0.1.9.md)

## 安装包校验

- versionCode：`60`
- SHA-256：`9a749440bec3aeca8634052d62b2a4304e7170083637b7f4779df8e541dc315a`
- 签名证书 SHA-256：`b389cf6e91508e445a0be2e28b981cf0a78b9e3531b40bc017ab28b87f4752ef`

与之前正式版使用同一签名证书，可直接覆盖安装。安装/更新后请在 LSPosed 中保持作用域为
`com.oplus.wallpapers`，并重载 Wallpapers 让新 Hook 生效。
