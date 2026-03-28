# `src/main/resources/bundles` 目录详解

## 这一层在做什么

这里是具体的本地化资源目录，负责给设置界面和筛选对话框提供中英文文本。

当前包含两个文件：

- `bundle.properties`：默认英文文本。
- `bundle_zh_CN.properties`：简体中文文本。

Mindustry/Arc 会按语言环境选择合适的 bundle。若找不到更具体的语言包，则会回退到默认 bundle。

## 资源键的组织方式

这些 key 大体可以分成三组：

### 1. 设置分类标题

- `settings.betterminimap`

这个 key 被 `BetterMiniMapMod` 用来创建设置页分类。

### 2. 设置项名称与说明

格式主要是：

- `setting.mmplus-*.name`
- `setting.mmplus-*.description`

这类 key 与 `buildSettings()` 中注册的开关和滑条一一对应。

例如：

- `setting.mmplus-enabled.name`
- `setting.mmplus-unitScale.name`
- `setting.mmplus-updatecheck.description`

### 3. 对话框和辅助交互文本

- `mmplus.units.title`
- `mmplus.blocks.title`
- `mmplus.search`
- `mmplus.search.hint`
- `mmplus.allon`
- `mmplus.alloff`
- `mmplus.invert`

这些 key 主要服务于两个筛选对话框。

## 默认英文 bundle 的作用

`bundle.properties` 不只是“英文版”，它也是回退基线：

- 当运行环境没有命中更具体语言包时，系统至少还能显示英文。
- 新增 key 时，通常应先补默认 bundle，再补目标语言包。

当前英文文本整体风格偏简洁、偏功能说明，适合作为默认语言。

## 中文 bundle 的作用

`bundle_zh_CN.properties` 是面向中文玩家的本地化版本。它做的不只是字面翻译，还做了语境适配：

- “Minimap: Enable overlay” 翻成“小地图：启用叠加层”，符合中文玩家理解习惯。
- “Show update dialog” 翻成“弹窗提示更新”，比直译更自然。
- 搜索提示改成“名称 / 内部名”，直接对应单位和方块的两种识别方式。

## 与 Java 代码的耦合点

这里的 key 不是随意命名，而是被代码直接依赖：

- `@settings.betterminimap` 在入口类里被引用。
- `Core.bundle.get("mmplus.units.title")` 在单位筛选对话框里被引用。
- `SettingsTable.checkPref` / `sliderPref` 会按 `setting.<key>.name` 规则查找显示名。

这意味着：

- 改 key 名必须同步改 Java 代码或设置 key。
- 只改 value 文案则不会影响行为。

## 为什么这一层值得单独写明

因为它体现了一个很重要的维护边界：

- 逻辑状态 key 如 `mmplus-enabled` 是代码稳定接口。
- UI 文案 key 如 `setting.mmplus-enabled.name` 是资源接口。

当前目录就是这套“文案接口层”的实现。

## 与其他层级的关系

- 上层 `resources/`：这里只是资源层中的 bundle 子域。
- 平级 `java/`：Java 层只消费 key，不维护具体文案。
- 发布层 `dist/`：最终产物会原样包含这里的两个 properties 文件。
