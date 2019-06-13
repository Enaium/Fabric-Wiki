# 更新Java代码库中的Yarn映射
## Hacky 方法
### 要求
* Fabric-Loom 0.2.2或以上
* Java代码库 - 目前Kotlin和Scala不会这样做。
* 需要一些装配
### 说明
1. 找出你的目标映射版本.例如,“net.fabricmc：yarn：1.14.1 Pre-Release 2 + build.2”.
2. 确保创建此版本的映射.这是hacky部分,因为目前唯一的方法是将build.gradle中的“minecraft”和“mappings”字段编辑到新版本,运行任何Gradle命令（“gradle build”将执行,即使它崩溃）,然后改回字段.
3. 运行以下魔法魔法命令:`gradle migrateMappings -PtargetMappingsArtifact="net.fabricmc:yarn:1.14.1 Pre-Release 2+build.2" -PinputDir=src/main/java -PoutputDir=remappedSrc`,其中:
“targetMappingsArtifact”指的是目标映射版本.运行此命令时,必须将build.gradle设置为mod的当前映射版本！
“inputDir”是输入目录,包含Java源代码,
“outputDir”是输出目录.如果缺少它将被创建.注意:您可能需要在引号中指定完整路径,如果遇到问题,请尝试此操作.
4. 将重新映射的源代码复制到输入目录,如果一切正常,
5. 希望最好的.

这应该适用于Minecraft版本,只要我们没有大规模打破中介或做同样愚蠢的事情（也就是说:大部分时间）.