# DouDuo 1.3.14 · iOS 17.0 构建

官方源码原样编译：<https://github.com/81199000/DouDuo> `v1.3.14`（build 92）。

## 改了什么

**UI 什么都没改。** 布局、顶栏、倍速角标、界面模式、API 接入全部与官方一致。

只做了一件构建必需的事：把上游依赖 **iOS 27 SDK** 的符号裁掉，否则用 iPhoneOS 26.x SDK
编译不过。这些代码在 iOS 17.0 上本来就被 `#available(iOS 27.1, *)` 挡在外面，永不执行：

| 符号 | 位置 | 处理 |
|---|---|---|
| `GeometryProxy.reservedRegions` | DouyinDuoLayout / DouyinFeedView / DouyinLiveRoom | 折痕/遮挡区返回空，回退到按尺寸推断 |
| `DeviceHinge` / `onHingeChange` | DouyinLiveRoom | 删掉 27.1 专用铰链回调 |
| `ArrangementView` / `arrangementViewStyle` | DouyinMessages | 走官方自带的 `legacyMessageContent` 回退分支 |
| `toolbarVerticalEdge` / `overlayArrangementZIndex` | DouyinDuoLayout | 补同名 `EnvironmentValues` shim，让 27.1 分支保持可编译 |

`FollowLiveLogic.swift` 补一行 `init(ttl:)`：`DYFollowMetadataCache` 有 `private` 存储属性
+ 自定义 `store`，Swift 6 编译器不再合成逐成员初始化器，`DYFollowMetadataCache(ttl:)` 调不通。

## 构建

GitHub Actions（`macos-26` + Xcode 26.6 / iPhoneOS 26.5 SDK），archs=arm64，未签名。

产物：Release 里的 `DouDuo-1.3.14-ios17-unsigned.ipa`

## 安装

未签名 IPA，需要自签（Sideloadly / Feather / ESign / TrollStore）。已有 DouDuo 的话用同一证书
覆盖安装可保留数据。
