# betterMiniMap

一个专注于 **Mindustry 小地图显示体验** 的客户端模组。  
This is a client-side mod focused on improving **Mindustry minimap readability**.

## 中文说明

### 功能
- 用单位 `icon` 显示小地图单位（替代三角/方块标记）。
- 支持单位朝向旋转显示。
- 同类型同队单位会按小地图像素距离动态聚合为一个放大图标。
- 聚合阈值基于小地图像素距离，随小地图缩放动态生效。
- 支持单位/建筑筛选与显示参数调整。

### 安装
- 从 Releases 下载产物：
  - `betterMiniMap-<version>.zip`
  - `betterMiniMap-<version>.jar`
  - `betterMiniMap-<version>-android.jar`
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

### Install
- Download one of these artifacts from Releases:
  - `betterMiniMap-<version>.zip`
  - `betterMiniMap-<version>.jar`
  - `betterMiniMap-<version>-android.jar`
- Put it into your Mindustry `mods` directory and restart the game.

### Build
```bash
gradle deploy
```
