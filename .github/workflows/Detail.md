# `.github/workflows` 目录详解

## 这一层在做什么

这一层存放 GitHub Actions 工作流定义。对当前仓库来说，它的实际作用只有一个：自动发布模组构建产物。

当前目录里只有 `release.yml`，说明维护者希望把发布路径收束成单条流水线，而不是散落到多个 CI 文件里。

## `release.yml` 的具体行为

### 触发条件

工作流在两种情况下触发：

- 手动触发 `workflow_dispatch`。
- 推送 tag 且 tag 名匹配 `v*`。

这意味着仓库支持两种发布节奏：

- 正式版本：通过 `v1.1.2` 这类 tag 触发。
- 临时验证：手工触发工作流检查构建链路是否正常。

### 权限

`permissions.contents: write` 允许工作流创建 GitHub Release 并上传构建产物。这是最小必要权限，不涉及包仓库或更高敏感权限。

### 执行步骤

1. `actions/checkout@v4`
   - 拉取仓库源码。
   - 没有源码就无法读到 `build.gradle`、`mod.json` 和 `src/`。

2. `actions/setup-java@v4`
   - 安装 Temurin JDK 17。
   - 虽然源码目标是 Java 8，但使用 JDK 17 构建是允许的，因为 `build.gradle` 已通过 `options.release.set(8)` 控制字节码目标。

3. `gradle/actions/setup-gradle@v4`
   - 配置 Gradle 缓存和环境。
   - 减少重复下载依赖的成本。

4. `android-actions/setup-android@v3`
   - 准备 Android SDK。
   - 这是因为项目要跑 D8，把类编译成 `classes.dex`，否则无法生成真正的跨平台合并包。

5. `sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"`
   - 安装 D8 所在的 Android build-tools。
   - 对本项目非常关键，因为 `build.gradle` 的 `findD8()` 会去找可执行的 D8。

6. `gradle clean deploy`
   - 这是整个流水线的核心调用。
   - 没有重新实现构建步骤，而是直接复用仓库的标准任务 `deploy`。
   - `clean` 确保旧产物不污染新发布。

7. `softprops/action-gh-release@v2`
   - 仅在 tag 场景下执行。
   - 自动生成 Release Notes。
   - 上传 `dist/*.jar` 和 `dist/*.zip`。

## 为什么工作流里直接用 `deploy`

这是这个目录最值得注意的设计点：CI 没有写一堆手动复制、打包、压缩命令，而是把真正的打包知识集中在 `build.gradle`。

优点很明确：

- 本地构建和云端构建用的是同一套任务。
- 如果产物结构变化，只改 Gradle 即可。
- `dist/` 的生成路径被统一，发布动作更稳定。

## 与其他层级的关系

- 与根目录：读取 `build.gradle`、`settings.gradle`、`mod.json`。
- 与 `src/`：源码是构建输入。
- 与 `dist/`：CI 上传的文件来自这里。
- 与 `build/`：工作流运行时会生成中间目录，但这些中间目录只是执行痕迹，不是发布接口。

## 这一层的边界

这个目录不负责：

- 实现小地图叠加层逻辑。
- 管理本地化文本。
- 定义模组入口类。

它只负责把上面这些现成内容正确地编译、打包、发布出去。
