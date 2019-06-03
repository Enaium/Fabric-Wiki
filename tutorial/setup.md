# 设置mod开发环境
## 必备

* JDK 8+
* 任何IDE，例如 [Intellij IDEA](https://www.jetbrains.com/idea/download/#section=windows)

## 步骤
1. 复制[fabric-example-mod](https://github.com/FabricMC/fabric-example-mod/)中的起始文件，不包括[LICENSE]和[README.md]文件 - 这些文件适用于模板本身，不一定适用于你的mod。
2. 编辑
``` gradle.properties ```：
* 确保设置[archives_base_name]和[maven_group]您的首选值
* 确保更新Minecraft的版本，映射和加载程序 - 所有这些都可以通过[本网站](https://modmuss50.me/fabric.html)查询- 以匹配您希望定位的版本。
* 添加您计划使用的任何其他依赖项[build.gradle]。
3. 将项目导入IDE。按照以下说明将项目导入Visual Studio代码。
4. 运行``` genSourcesGradle```任务。如果您的IDE没有Gradle集成，请在终端中运行以下命令：
``` key
./gradlew genSources
```
5. 如果您想让IDE运行配置，您可以运行以下命令：
* 对于IntelliJ IDEA : 
``` key
./gradlew idea.
```
* 对于Eclipse : 
``` key
./gradlew eclipse.
```
* 如果使用VS代码，则在步骤3生成配置。
6. Happy modding!
## 建议
* 虽然Fabric API对于开发mod并不是绝对必要的，但它的主要目标是提供交叉兼容性和游戏引擎没有的钩子，因此强烈建议使用它！
* 由于Fabric处于早期开发阶段，偶尔会随着Fabric-loom（我们的Gradle构建插件）的发展而出现需要手动清除（删除）缓存（可以在其中找到.gradle/caches/fabric-loom）的问题。这些通常会在确定后公布。
* 不要犹豫提问！我们在这里帮助您并与您合作，让您的梦想成为现实。

## 故障排除
### 缺少声音

有时，将Gradle项目导入IDE时，资产可能无法正确下载。在这种情况下，请downloadAssets手动运行任务 - 使用IDE的内置菜单或只是运行[gradlew downloadAssets]。

## 汉化总结

* 打开 [fabric-example-mod](https://github.com/FabricMC/fabric-example-mod/)

* 下载 为zip然后解压 在目录打开 Powershell或者cmd （shift+右键打开）输入命令 IDEA ：
``` key
./gradlew idea
```
 Eclipse： 
``` key
./gradlew eclipse
``` 
然后出现 Success 就是配置成功 IDEA 打开方法：双击 fabric-example-mod.ipr 用IDEA打开