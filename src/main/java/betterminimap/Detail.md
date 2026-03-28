# `src/main/java/betterminimap` 目录详解

## 这一层在做什么

这个包是模组 Java 代码的核心命名空间。它把模组的入口类、更新检查逻辑和功能实现子包收拢在一起，形成一个完整的运行模块。

当前目录下有两个直接类和一个子包：

- `BetterMiniMapMod.java`
- `GithubUpdateCheck.java`
- `features/`

这三个元素正好对应三个不同职责：

- 模组启动挂接。
- 版本更新检查。
- 小地图增强功能本体。

## `BetterMiniMapMod.java` 的作用和实现

这是 `mod.json` 中声明的主入口类。Mindustry 在加载模组时会实例化它并调用生命周期方法。

它的 `init()` 非常克制，只做三件事：

1. 调用 `BetterMiniMapFeature.init()`。
2. 监听 `ClientLoadEvent`。
3. 在客户端 UI 就绪后把设置页注册进去，并触发更新检查。

这里的设计很合理，因为入口类本身不承担复杂状态：

- 真正功能初始化放到 feature 类，入口只负责转发。
- 使用 `settingsAdded` 防止设置分类重复添加。
- 先 `GithubUpdateCheck.applyDefaults()`，再注册设置项，这样设置页里对应的布尔值就有默认值。
- `ui.settings.addCategory("@settings.betterminimap", Icon.map, BetterMiniMapFeature::buildSettings)` 这一句把资源层和功能层连接起来了：
  - 分类标题来自 bundle。
  - 图标使用 Mindustry 自带 `Icon.map`。
  - 设置项内容由 `BetterMiniMapFeature.buildSettings` 动态构建。

## `GithubUpdateCheck.java` 的作用和实现

这是独立的配套能力，不参与小地图绘制，但会影响用户获取新版本的体验。

### 它解决的问题

玩家安装客户端模组后，通常不会频繁手动检查仓库版本。这个类负责在游戏启动后到 GitHub 查询最新版本，并在发现新版本时提醒用户。

### 它的实现结构

- 通过几个固定 key 存储设置和状态：
  - 是否启用检查。
  - 是否弹窗提示。
  - 上次检查时间。
  - 忽略的版本。
- 用 `checked` 避免同一运行周期内多次执行。
- 用 `checkIntervalMs = 6 小时` 避免每次都打 GitHub API。

### 它的请求策略

先请求 GitHub Releases API：

- `https://api.github.com/repos/{owner}/{repo}/releases/latest`

失败时再回退到原始 `mod.json`：

- `https://raw.githubusercontent.com/{owner}/{repo}/main/mod.json`

这个设计说明作者考虑了两种现实情况：

- API 可用时，优先获得标准 release 信息和跳转链接。
- API 限流或失败时，至少还能从仓库原始元数据里知道 `version`。

### 它的版本比较方式

不是简单字符串比较，而是用正则提取数字片段后逐段比较。这让它能正确比较：

- `v1.1.2`
- `1.1.10`
- `release-2.0`

至少在常见 tag 命名下不会被字典序误导。

### 它的 UI 行为

找到新版本后有两条分支：

- 若关闭弹窗：显示一个 toast。
- 若开启弹窗：弹出 `BaseDialog`，允许打开 Release 页面或忽略该版本。

这说明它不是强侵入式更新器，只做提醒，不做自动下载或热更新。

## 当前包为什么不把所有逻辑写在一个类里

因为这里承担的是“模组骨架”职责，而不是“渲染细节”职责：

- `BetterMiniMapMod` 负责接入游戏。
- `GithubUpdateCheck` 负责外部版本源交互。
- `features/` 负责真正的 UI 叠加和筛选逻辑。

这样的拆分避免了一个巨型类同时处理生命周期、网络、渲染和设置。

## 与上下层的关系

- 上层 `src/main/java/`：这里只是代码根，这一层才开始体现业务模块边界。
- 下层 `features/`：这里把小地图主功能继续下沉，保持当前包职责清晰。
- 与 `mod.json`：入口类全名直接被 `mod.json` 引用。
- 与 `resources/bundles/`：设置分类名和更新相关设置最终依赖 bundle 文案。
