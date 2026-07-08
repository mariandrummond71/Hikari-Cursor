# Hikari-Cursor — 在 macOS 上使用 Windows 鼠标指针

[English](README.md) | **简体中文** | [日本語](README.ja.md)

把 Windows 的 `.cur` / `.ani` 鼠标指针主题直接用到 macOS 上,支持动画光标,全局生效(所有应用,包括浏览器/Electron)。在 macOS 26 (Tahoe) 上开发验证。

## 安装(普通用户)

到 [Releases](../../releases) 下载最新的 `Hikari-Cursor.dmg`,打开后把 **Hikari-Cursor** 图标拖到 **Applications** 文件夹即可。

首次打开会被 macOS 拦截(App 未经 Apple 公证,只做了本机签名)。任选一种放行:

- **推荐**:在「访达 → 应用程序」里 **右键点 Hikari-Cursor → 打开**,弹窗里再点「打开」;或到「系统设置 → 隐私与安全性」页面底部点「仍要打开」。
- 或者终端执行一次:`xattr -dr com.apple.quarantine /Applications/Hikari-Cursor.app`

需要 macOS 15 或更高版本(在 macOS 26 上开发验证)。

## 从源码构建

需要 Xcode / Swift 6 工具链,运行 `./build_app.sh`,产物在 `dist/` 下:

| 产物 | 说明 |
|---|---|
| `Hikari-Cursor.app` | 图形界面 + 菜单栏常驻工具:选主题文件夹 → 预览 → 逐个指定映射 → 一键应用/恢复 |
| `mousecur` | 命令行工具,功能相同并支持开机自动应用(CLI 保留原名,与 GUI 品牌名独立) |

## 手动映射

选好主题文件夹后,列表里每一行右侧都有一个下拉菜单,可以指定这个文件替换 macOS 的哪个光标(菜单里每个选项旁边带 macOS 光标预览图,按「常用 / 调整大小 / 窗口边缘·角 / 其他」分组)。默认按文件名自动识别(显示为「自动 · xxx」),也可以:

- 手动改成任意 macOS 光标 —— 连自动识别为「无对应」的文件(手写、个人选择等)也能强行映射;
- 选「不映射」跳过某个文件。

辅助显示:同名文件(如某些包每套配色各有一份 `busy.ani`)会标出所在子文件夹区分;多个已勾选文件映射到同一 macOS 光标时,行上出现橙色警告(实际只有最后应用的那个生效)。手动映射会随「应用主题」保存,下次打开自动恢复。

## 菜单栏常驻

`Hikari-Cursor.app` 是纯菜单栏应用(`LSUIElement`,不占 Dock)。关闭主窗口不会退出 App——右上角菜单栏始终有一个箭头图标,点开可以:

- 打开 Hikari-Cursor…(重新弹出主窗口)
- 重新应用上次主题 / 恢复系统默认(不用开窗口就能操作)
- 登录时自动应用(勾选框,实时反映 LaunchAgent 是否已安装)
- 退出 Hikari-Cursor(彻底终止进程,菜单栏图标消失)

## 命令行速用

```bash
# 应用整套主题(按文件名自动识别角色并映射到 macOS 光标槽位)
./dist/mousecur apply <主题文件夹>

# 恢复 macOS 默认光标
./dist/mousecur reset

# 光标大小(pt,默认 32)
./dist/mousecur apply <主题文件夹> --size 40

# 单个文件应用到指定槽位
./dist/mousecur apply arrow.cur --slot arrow

# 光标替换在注销后失效;安装登录时自动重新应用
./dist/mousecur agent install

# 取消登录时自动重新应用
./dist/mousecur agent uninstall

# 其他
./dist/mousecur info <文件>       # 查看帧数/尺寸/热点
./dist/mousecur preview <文件>    # 导出 PNG 帧
./dist/mousecur slots             # 列出可替换的系统光标槽位
./dist/mousecur doctor            # 检查系统私有接口可用性
```

## 支持的文件格式

- 静态 `.cur`(BMP 1/4/8/24/32 位 + AND 掩码,或内嵌 PNG)
- 动画 `.ani`(RIFF/ACON,支持 `rate`/`seq` 块、8 位调色板帧)
- **伪装成 `.cur` 的 ANI**(部分国内主题包把 RIFF 动画直接命名为 .cur,按内容自动识别)

## Windows 角色 → macOS 槽位映射

| Windows 文件名 | macOS 光标 |
|---|---|
| Normal / 正常选择 | 箭头(含 Tahoe 新版 ArrowS、浏览器 cursor.0) |
| Text / 文本选择 | 文本 I 型(含 IBeamS、cursor.1) |
| Link / 链接选择 | 手型指点(cursor.13 / PointingHand) |
| Busy / 忙 | 等待彩球(Wait) |
| Working / 后台选择 | 忙碌可点按(cursor.4) |
| Unavailable / 不可用 | 禁止(cursor.3 / NotAllowed) |
| Precision / 精确选择 | 十字线 |
| Help / 帮助选择 | 帮助 |
| Move / 移动 | 移动 |
| Vertical/Horizontal/Diagonal / 调整大小 | 各方向调整 + 窗口边缘/角 |
| Handwriting、Person、Pin、Alternate 等 | macOS 无对应,自动跳过 |

## 注意事项

- 光标注册只在当前登录会话有效,注销/重启后自动还原;想常驻用 `mousecur agent install`。
- 「恢复默认」如个别光标未立即还原,注销重新登录即可(注册本来就不跨会话)。
- 使用了系统私有接口,大版本升级后如失效,先跑 `mousecur doctor` 检查。
- 二进制为本机 ad-hoc 签名,只适合自用,分发需要重新签名。

## 许可证

[MIT](LICENSE) © 2026 zhy072
