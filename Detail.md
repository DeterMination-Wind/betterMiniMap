# betterMiniMap 根目录详解

## 这一层在做什么

仓库根目录负责把整个模组项目组织成一个可以被 Gradle 构建、被 GitHub Actions 发布、被 Mindustry 识别和被玩家阅读的完整单元。这里不是“业务实现层”，而是“装配层”和“项目入口层”：

- 通过 `build.gradle` 定义源码如何编译、如何生成桌面与 Android 共用产物、以及如何复制到 `dist/`。
- 通过 `settings.gradle` 固定 Gradle 根项目名，保证构建输出命名稳定。
- 通过 `mod.json` 向 Mindustry 声明这是一个 Java 模组、入口类是谁、版本号和仓库地址是什么。
- 通过 `README.md`、`README_EN.md`、`CHANGELOG.md`、`LICENSE` 提供人类可读信息。
- 通过 `.github/` 提供自动化发布流程。
- 通过 `src/` 存放真正的源码与本地化资源。
- 通过 `dist/` 存放已经构建完成、可直接投放到游戏 `mods` 目录的产物。

换句话说，根目录负责回答四个问题：

1. 这个模组是什么。
2. 这个模组怎么构建。
3. 这个模组怎么发布。
4. 这个模组的代码和资源放在哪里。

## 根目录关键文件分别承担什么职责

### `build.gradle`

这是整个项目最关键的工程文件。它把“Java 代码”变成“Mindustry 可加载模组包”的流程定义清楚了。

这里的实现重点有几件事：

- 开启 `java` 插件，说明项目本质是 Java 模组。
- 强制 `sourceCompatibility` 和 `targetCompatibility` 为 Java 8，匹配仓库约束，也兼容 Mindustry 侧运行环境。
- 使用 `MindustryJitpack:core` 作为 `compileOnly` 依赖，表示编译时需要 Mindustry API，但发布包不把游戏本体一起打进去。
- 通过 `resolutionStrategy.eachDependency` 把特定 Arc 依赖版本改写为 `mindustryVersion`，目的是在 JitPack 依赖解析上更稳定。
- 默认游戏版本来自 `mindustryVersion` 属性，没有提供时回落为 `v154.2`。
- 定义一整套产物任务：
  - `jar`：输出 `betterMiniMap.zip`。
  - `jarDesktop`：输出桌面版 `betterMiniMap.jar`。
  - `jarAndroid`：输出 Android 版 `betterMiniMap-android.jar`。
  - `d8InputJar` + `dexAndroid`：把 Java 字节码再转成 `classes.dex`，用于 Android 端加载。
  - `jarMerged`：把普通类文件、`classes.dex`、资源和 `mod.json` 合并成单个跨平台 `jar`。
  - `zipMerged`：把合并后的 jar 再按 zip 形式输出，兼容不同用户的安装习惯。
  - `deploy`：统一构建 merged jar 和 zip，是 README 与 CI 都调用的发布入口任务。
- 定义复制任务，把产物复制到仓库内 `dist/`，以及仓库外的 `构建/betterMiniMap/` 目录，说明作者同时照顾仓库内分发和本地工作流。

### `settings.gradle`

只做一件事：固定根项目名为 `betterMiniMap`。这件事非常小，但它会影响 Gradle 的任务上下文、默认产物命名和 IDE 导入结果。

### `mod.json`

这是 Mindustry 识别模组的元数据入口。它不实现功能，但控制“游戏如何认识这个模组”：

- `name` 是内部模组名，代码中更新检查也用到了它。
- `displayName` 是展示名。
- `repo` 让游戏或用户知道仓库来源。
- `version` 与 Gradle 版本保持一致，避免发布物和元数据不一致。
- `main` 指向 `betterminimap.BetterMiniMapMod`，这就是 Java 入口类。
- `hidden: true` 表示它更偏客户端增强型模组，不强调在模组列表里高曝光。
- `java: true` 表示由 Java 入口驱动，而不是纯脚本或纯资源包。

### `README.md` / `README_EN.md`

这两份文档是面对用户的说明层，不参与运行逻辑，但决定了外部如何理解项目。内容与代码实现是一一对应的：

- “用单位真实图标替代几何标记”对应 `BetterMiniMapFeature.drawUnitCluster` 中的 `Draw.rect(c.type.uiIcon, ...)`。
- “支持朝向旋转”对应聚合时累计朝向向量并在绘制时调用 `rotation()`。
- “同类同队单位聚合”对应 `buildUnitClusters`。
- “支持筛选和参数调整”对应 `buildSettings` 与两个筛选对话框。
- 构建命令 `gradle deploy` 对应 `build.gradle` 中的 `deploy` 任务。

### `CHANGELOG.md`

记录功能演进。目前内容不长，但能看出 v1.0.0 就已经把本项目的核心方向定死了：图标化、朝向、聚合、可调参数、自动发布。

### `LICENSE`

使用 GPL v3，决定了项目分发与修改的法律边界。它不影响运行时，但会影响二次发布、合并代码和派生项目的合规方式。

### `.gitignore`

明确排除了 `.gradle/`、`build/`、`out/`、`.idea/` 和 `*.iml`。这说明作者把“源码与发布物”视为仓库资产，但把“构建缓存与 IDE 状态”视为临时文件。

### `AGENTS.md`

这是给自动化编码代理的仓库约束说明，不参与模组运行。它告诉维护者：

- Java 8 兼容性不能破坏。
- 优先关注性能和可读性。
- 用户可见文案要优先进 bundle，而不是硬编码。

这三条都能在源码中看到对应痕迹，尤其是 bundle 和 Java 8 约束。

## 这一层是怎么把下面几层串起来的

根目录是“上层意图”和“下层实现”的对接点：

- 它把 `.github/` 的 CI 工作流和 `build.gradle` 绑定起来，让标签发布时自动构建。
- 它把 `src/main/java/` 的类通过 `mod.json` 暴露给 Mindustry。
- 它把 `src/main/resources/` 的 bundle 一起打进最终产物，让设置界面文案可本地化。
- 它把 `dist/` 暴露为最终交付层，供用户直接安装。

可以把它看成一张路由表：

- 玩家进入项目时先看 README。
- 游戏加载项目时先看 `mod.json`。
- Gradle 构建项目时先看 `settings.gradle` 和 `build.gradle`。
- GitHub 发布项目时先看 `.github/workflows/release.yml`。

## 与其他层级的关系

- 与 `.github/` 的关系：根目录提供构建脚本，CI 只是远程调用它。
- 与 `src/` 的关系：根目录不写业务，但负责声明业务代码如何被编译和打包。
- 与 `dist/` 的关系：`dist/` 是根目录构建规则的落地结果，不是独立维护层。

## 额外说明

工作区中还存在 `.git/`、`.gradle/`、`build/` 等目录。它们分别属于版本控制内部数据、Gradle 缓存和本地产物中间层，不是本项目的语义实现层，因此没有为这些工具内部目录逐层补文档；真正影响模组设计、构建和分发的内容已经集中在当前根目录说明以及下级语义目录说明中。
