# betterMiniMap
<h1 align="center">
  <a href="https://github.com/DeterMination-Wind/betterMiniMap/releases/latest"><img src="https://img.shields.io/github/v/release/DeterMination-Wind/betterMiniMap?display_name=release&label=Latest%20Release&color=green"></a>
  <a href="https://github.com/DeterMination-Wind/betterMiniMap/releases"><img src="https://img.shields.io/github/downloads/DeterMination-Wind/betterMiniMap/total?label=Downloads&color=blue"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/DeterMination-Wind/betterMiniMap?label=License"></a>
  <a href="https://github.com/DeterMination-Wind/betterMiniMap"><img src="https://img.shields.io/github/stars/DeterMination-Wind/betterMiniMap?style=flat&label=Star%20this%20mod!&color=yellow"></a>
</h1>

一个专注于 **Mindustry 小地图显示体验** 的客户端模组。  
This is a client-side mod focused on improving **Mindustry minimap readability**.

## 中文说明

### 功能
- 用单位 `icon` 显示小地图单位（替代三角/方块标记）。
- 支持单位朝向旋转显示。
- 同类型同队单位会按小地图像素距离动态聚合为一个放大图标。
- 聚合阈值基于小地图像素距离，随小地图缩放动态生效。
- 支持单位/建筑筛选与显示参数调整。
- GitHub 自动更新检查：每隔 6 小时对比 GitHub Releases 最新版本（API 失败时回退读取仓库 mod.json），发现新版本时弹出更新提示（可打开 Releases 下载、忽略该版本，可在设置中关闭检查或弹窗）。

### 安装
- 从 Releases 下载产物（文件名不含版本号）：
  - `betterMiniMap.jar`（已内置 dex，桌面与 Android 通用）
  - `betterMiniMap.zip`
- 将文件放入 Mindustry 的 `mods` 目录并重启游戏。

### 开发构建
```bash
gradle deploy
```

## English

### Features
- Replaces minimap unit markers with real unit `icons`.
- Supports icon rotation based on unit facing direction.
- Dynamically clusters same-type, same-team units into a larger icon.
- Cluster threshold is based on minimap pixel distance, so it scales with zoom.
- Includes unit/building filters and visual tuning settings.
- Automatic GitHub update check: compares against the latest GitHub release every 6 hours (falls back to the repo's mod.json when the API fails) and shows an update dialog when a new version exists (open Releases, ignore this version, or disable the check/dialog in settings).

### Install
- Download one of these artifacts from Releases (file names have no version suffix):
  - `betterMiniMap.jar` (merged, includes dex; works on both desktop and Android)
  - `betterMiniMap.zip`
- Put it into your Mindustry `mods` directory and restart the game.

### Build
```bash
gradle deploy
```
