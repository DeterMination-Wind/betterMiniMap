# `dist` 目录详解

## 这一层在做什么

`dist/` 是项目的最终交付层。这里不再关心源码如何组织、Gradle 如何编排、UI 如何绘制，而是只关心一件事：给用户一个可以直接放进 Mindustry `mods` 目录的文件。

当前目录包含两个产物：

- `betterMiniMap.jar`
- `betterMiniMap.zip`

二者当前大小一致，内容列表也一致，说明这不是“两个不同功能版本”，而是“同一份模组内容的两种分发封装形式”。

## 产物里实际包含什么

从归档内容看，这两个文件都包含：

- `META-INF/MANIFEST.MF`
- `betterminimap/*.class`
- `betterminimap/features/*.class`
- `bundles/bundle.properties`
- `bundles/bundle_zh_CN.properties`
- `classes.dex`
- `mod.json`

这说明它们都是 merged 产物，也就是：

- 既有桌面端可直接加载的 `.class`
- 又有 Android 端需要的 `classes.dex`
- 同时带上运行所需资源和模组元数据

因此，这两个文件本质上都是跨平台安装包。

## 为什么同时保留 jar 和 zip

从 `build.gradle` 可以看出，项目同时生成 merged jar 与 merged zip。这样做通常有两个目的：

- 某些用户或分发渠道习惯使用 `.jar`。
- Mindustry 社区里也常见以 `.zip` 形式分发模组。

由于内容一致，区别主要在文件扩展名和用户安装习惯，而不是内部实现。

## 这一层是如何产生的

`dist/` 不是手写目录，而是构建链路的结果：

1. `src/main/java` 编译出 `.class`
2. `dexAndroid` 生成 `classes.dex`
3. `jarMerged` 合并类文件、dex、资源和 `mod.json`
4. `zipMerged` 以 zip 形式重打同样内容
5. `copyMergedJarToDist` / `copyMergedZipToDist` 把结果复制到当前目录

所以 `dist/` 是整个项目“所有层级协作后的落点”。

## 与其他层级的关系

- 与根目录：由 `build.gradle` 定义如何把文件复制到这里。
- 与 `src/`：这里的内容最终都源自源码和资源目录。
- 与 `.github/workflows/`：GitHub Release 上传的正是这里的文件。
- 与玩家使用场景：这是唯一一个用户可以直接拿来安装的目录。

## 这一层不负责什么

这里不负责：

- 决定小地图图标怎么画。
- 决定设置界面有哪些选项。
- 决定检查更新的逻辑。

它只是这些逻辑最终被封装后的结果层。

## 额外观察

当前 `git status` 显示这两个产物有未提交修改，说明仓库维护流程里确实会跟踪 `dist/` 的发布文件。这和一些只在 CI 临时生成产物的仓库不同，当前项目把 `dist/` 当成仓库内容的一部分来维护。
