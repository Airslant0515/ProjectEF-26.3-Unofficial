# ProjectEF-26.3-Unofficial
这是ProjectEF的 26.3 AI移植版本，内容完全来源于对AI许愿，但是在实际20小时游玩测试中并未发现bug和冲突，可以流畅运行。

> **非官方移植 / Unofficial port.** 本仓库是社区成员把 **ProjectEF**（ProjectE 的 Fabric 版）移植到
> **Minecraft 26.3 + Fabric** 的成果，**不是**上游官方发布，也**未获**上游 ProjectEF / ProjectE 团队背书。
> 上游项目：[wchiway/ProjectEF](https://github.com/wchiway/ProjectEF) ·
> [ProjectE-Integration/ProjectE](https://github.com/ProjectE-Integration/ProjectE)

[![Minecraft](https://img.shields.io/badge/Minecraft-26.3-informational)]()
[![Loader](https://img.shields.io/badge/Fabric%20Loader-0.20.1%2B-informational)]()
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

---

## 这是什么

**等价交换（Equivalent Exchange / EMC）** 在 Minecraft 26.3 上的完整可玩实现：

- 核心是 **EMC 系统** 与 **转化桌（Transmutation Table / Tablet）**
- 在此基础上保留 ProjectE 的全部玩法：收集器 / 继电器 / 凝聚器、炼金袋、暗物质与红物质工具与盔甲、
  各色戒指、世界转换（World Transmutation）、知识共享与 EMC 命令等
- 支持 **自定义 EMC**：`config/ProjectE/custom_emc.json`，或用 `/projecte setemc` 命令写入

> 一句话：装上就能玩等价交换——把东西丢进转化桌换成 EMC，再用 EMC 换出你想要的东西。

## 前置模组要求（必须满足）

| 组件 | 版本要求 | 说明 |
| --- | --- | --- |
| Minecraft | **26.3** | 仅此版本，其它版本不加载 |
| Fabric Loader | **≥ 0.20.1** | |
| Fabric API | **0.166.2+26.3** 或更高的 26.3 构建 | 必需 |
| **Forge Config API Port** | **v26.3.1（Fabric 版，≥ 26.3.0）** | **必需**，缺失时游戏会直接提示依赖不满足 |
| Java | **25** | 26.3 要求 |

**可选前置（不装也能正常玩）**

| 组件 | 版本 | 作用 |
| --- | --- | --- |
| JEI | 31.18.0.114+（26.3 Fabric） | 配方查询与 EMC 信息展示 |
| Jade | 26.3.5+（26.3 Fabric） | 方块信息 HUD |

**不再需要、也请不要安装旧版本前置**：Trinkets、EMI、REI、WTHIT 在 26.3 没有可用版本，
本移植已移除对这些模组的集成（戒指等饰品改为从**快捷栏 / 副手**生效）。

## 安装

1. 安装 Minecraft 26.3 与 Fabric Loader 0.20.1+（Java 25）。
2. 把 **Fabric API** 与 **Forge Config API Port v26.3.1 (Fabric)** 放进 `mods/`。
3. 把本模组 `ProjectEF-26.3-PE1.3.1.jar` 放进 `mods/`。
4. 启动游戏。首次启动会在 `config/ProjectE/` 生成 `common.toml`、`custom_emc.json` 等配置文件。

## 自定义 EMC

游戏内（需要 OP / 权限）：

```
/projecte setemc <EMC值> [物品ID|#标签]     # 设置某个物品/标签的 EMC；省略物品时作用于手上物品
/projecte emc set|add|remove|get|test <玩家> <数值>   # 查询/修改玩家 EMC
```

配置文件 `config/ProjectE/custom_emc.json`（可用命令写入，也可手动编辑，重启或 `/reload` 后生效）：

```json
{
  "comment": "Use the in-game commands to edit this file",
  "entries": {
    "minecraft:stone": { "emc": 1 },
    "minecraft:diamond": { "emc": 8192 },
    "#c:ores": { "emc": 128 }
  }
}
```

- 键是物品 ID（`namespace:path`）或标签（`#namespace:path`）
- 值必须是 `{"emc": <非负整数>}`，单位与游戏内 EMC 一致
- 自定义值优先于内置映射；标签会展开成该标签下的所有物品

## 与上游 ProjectEF（1.21.1）的差异

| 项目 | 上游 1.21.1 | 本移植 26.3 |
| --- | --- | --- |
| 游戏/加载器 | 1.21.1 / Fabric | 26.3 / Fabric 0.20.1 |
| 饰品（戒指等） | Trinkets 饰品槽 | 快捷栏 / 副手生效（26.3 无 Trinkets） |
| EMI / REI 集成 | 有 | 无（26.3 无对应版本），保留 JEI / Jade |
| 盔甲 | ArmorItem + 耐久 | 数据驱动装备（`equipment` 资产），沿用 ProjectE 的"不消耗耐久"设定 |
| 物品模型 | `models/item` + `overrides` | `items/*.json` + `range_dispatch` / `condition` 自定义模型属性 |
| 配方 / 进度 / 世界转换 | 1.21.1 数据格式 | 迁移为 26.3 数据格式（成分用 HolderSet 字符串等） |
| 渲染 | `RenderType` + `GuiGraphics` | 新渲染管线（renderpearl）+ `GuiGraphicsExtractor` |
| 配置 | Forge Config API Port 21.x | Forge Config API Port 26.3.x（v5 API） |

**存档兼容性说明**：mod id 保持 `projecte`、所有注册名与 1.21.1 完全一致，因此配置路径、数据包命名空间
与物品/方块 ID 都不变。但 Minecraft 26.3 本身的世界格式与 1.21.1 不同，**1.21.1 的旧存档无法直接被 26.3 打开**
（这是游戏版本差异，与本模组无关）。同一 26.3 存档在安装/卸载本模组之间切换时，方块与物品数据保持完整。

## 从源码构建

```bash
# 需要 JDK 25
./gradlew build          # Windows: gradlew.bat build
# 产物: build/libs/ProjectEF-26.3-PE1.3.1.jar
```

依赖说明：

- **JEI / Jade / Forge Config API Port** 通过 [Modrinth Maven](https://api.modrinth.com/maven) 以 `compileOnly`
  方式解析（`build.gradle` 里已固定为测试时使用的确切版本），不会被打进发布 jar；
  `Forge Config API Port` 同时以 `localRuntime` 提供，用于本地开发环境运行。
- 离线/无法访问 Modrinth 时，可把对应 jar 放到 `libs/` 并把 `build.gradle` 中的坐标改回 `files('libs/*.jar')`。
- `commons-math3` 与 `PinIn` 会以 jar-in-jar 方式打包进模组本体。

## 仓库结构

```
src/api/java        # 公开 API（EMC 数值、NSS、自定义转换等）
src/main/java       # 主体实现
src/main/resources  # fabric.mod.json、mixin、accesswidener、语言文件
src/datagen/generated  # 生成的资源与数据（物品模型、配方、进度、EMC 数据等）
tools/              # 只操作 tmp\ 目录的 PowerShell 辅助脚本
```

## 辅助脚本（Windows / PowerShell）

两个脚本都**只读写仓库下的 `tmp\`**：解压前会校验目标路径确实位于 `tmp\` 内，拒绝在磁盘根目录或仓库根目录执行删除，
也**不会**结束任何 `java` 进程。适合从已构建的 jar 中批量提取 EMC / 配方数据做对照或翻译。

```powershell
# 从 build\libs 最新的 jar 中提取配方、语言文件与 EMC 自定义数据到 tmp\export-<时间戳>
powershell -ExecutionPolicy Bypass -File .\tools\Extract-EmcAndRecipes.ps1

# 指定 jar 与输出目录（输出目录必须位于 tmp\ 下）
powershell -ExecutionPolicy Bypass -File .\tools\Extract-EmcAndRecipes.ps1 `
    -JarPath .\build\libs\ProjectEF-26.3-PE1.3.1.jar -OutDir .\tmp\my-export

# 清理 tmp\ 下由脚本产生的目录（仅限 tmp\）
powershell -ExecutionPolicy Bypass -File .\tools\Clear-Temp.ps1
```

## 已验证环境

- 专用服务器与客户端均可启动、进入世界、保存并重新加载
- 与整合包 26 个模组共存实测（Sodium、Iris、FerriteCore、ImmediatelyFast、EntityCulling、
  LambDynamicLights、JEI、Jade、Xaero 系列、Tom's Storage、spark 等），无崩溃、无 mixin 冲突
- 28 模组同开时 spark 显示 TPS 稳定 `20.0`

## 已知限制

- 26.3 没有 Trinkets / EMI / REI / WTHIT 可用版本，相关集成被移除；戒指等改为快捷栏/副手生效
- 盔甲沿用 ProjectE 设计：不消耗耐久、不可附魔（避免与 26.3 数据驱动装备的耐久机制冲突）
- 更新检查指向本仓库；上游 1.21.1 的更新信息不适用于本移植

## 许可证与致谢

- 本仓库代码沿用上游 **MIT** 许可，原始 `LICENSE`（`Copyright (c) 2026 Chiway Wang`）**原样保留**。
- 移植部分：`Copyright (c) 2026 Airslant0515`，同样以 MIT 发布。详见 [NOTICE](NOTICE)。
- 上游 ProjectEF：<https://github.com/wchiway/ProjectEF>
- 上游 ProjectE：<https://github.com/ProjectE-Integration/ProjectE>
- EE2 原作者 x3n0ph0b3；GUI 贴图 MidnightLightning；前贴图画师 Magic Banana。
- 源码中保留原作者 `@author` 注释。

---

## English summary

Unofficial, community-made Fabric port of **ProjectEF / ProjectE (Equivalent Exchange 2)** to
**Minecraft 26.3** (Fabric Loader 0.20.1). Not affiliated with or endorsed by the upstream projects.

**Required:** Minecraft 26.3, Fabric Loader ≥ 0.20.1, Fabric API 0.166.2+26.3,
**Forge Config API Port v26.3.1 (Fabric)**, Java 25.
**Optional:** JEI 31.18.0.114+, Jade 26.3.5+ (both for 26.3 / Fabric).

Core gameplay: the **EMC system** and the **Transmutation Table**, plus collectors, relays, condensers,
alchemical bags, dark/red matter tools and armour, rings and world transmutation.
Custom EMC is supported through `config/ProjectE/custom_emc.json` and the `/projecte setemc` command.

Licensed under MIT (upstream `LICENSE` kept verbatim; port modifications © 2026 Airslant0515). See [NOTICE](NOTICE).
