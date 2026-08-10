# AGENTS.md - betterMiniMap 模组说明

## 文件结构（当前仓库）
```text
betterMiniMap/
|-- .github/
|   \-- workflows/
|       \-- release.yml
|-- src/
|   \-- main/
|       |-- java/
|       |   \-- betterminimap/
|       |       |-- BetterMiniMapMod.java
|       |       |-- GithubUpdateCheck.java
|       |       \-- features/
|       |           \-- BetterMiniMapFeature.java
|       \-- resources/
|           \-- bundles/
|-- .gitignore
|-- AGENTS.md
|-- build.gradle
|-- CHANGELOG.md
|-- dist/
|-- gradle/
|-- gradlew
|-- gradlew.bat
|-- LICENSE
|-- mod.json
|-- README.md
|-- README_EN.md
\-- settings.gradle
```

## 维护约束
- 保持 Java 8 兼容（如本项目包含 Java 源码）。
- 变更优先聚焦性能与可读性，不做无关重构。
- 用户可见文案优先走 bundle/资源文件，不硬编码。

命令操作请使用 PowerShell 7（`pwsh`）。
