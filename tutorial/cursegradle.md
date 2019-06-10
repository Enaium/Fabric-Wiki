# 使用CurseGradle在CurseForge上发布mod
要熟悉CurseGradle,请阅读该[项目的官方维基](https://github.com/matthewprenger/CurseGradle/wiki)

## 特定于结构的更改
（注意：最后更新了Fabric 0.2.3）

使用Fabric所需的附加功能已突出显示为绿色.

![img1](img/cursegradle/cursegradle_changes.png)

它们按顺序排列：
* `afterEvaluate { ... }` - Loom的remapJar调整目前在评估后发生,因此只能读取remapJar.output,
* `mainArtifact(remapJar.output)` - 提交给CurseForge的主要工件应该是remapJar的输出,即重新映射（生产就绪）mod .JAR文件,
* `uploadTask.dependsOn(remapJar)` - 确保CurseForge上载任务仅在构建重新映射的JAR后运行,
* `forgeGradleIntegration = false` - 由于您没有使用ForgeGradle,因此必须禁用该特定集成.