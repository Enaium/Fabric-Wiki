# 映射
## 介绍

映射定义类，字段和方法的名称。在普通的织机环境中，使用[映射](https://github.com/FabricMC/yarn)，它为社区决定的Minecraft代码库提供有意义的名称。[[调解](https://github.com/FabricMC/intermediary)也是Fabric使用的基本映射类型。对映射的需求来自对Minecraft版本的混淆，这会带来多重挑战。重映射是将映射应用于已编译的类或源文件的过程。

## 使用映射

在Loom中，映射定义了开发环境中使用的Minecraft类，字段和方法的名称。根据安装的映射，这些名称可能因开发环境而异。

Yarn是Loom使用的默认映射。随着捐款被接受，纱线逐渐改善并接收新版本。Loom中的映射是使用`mappings`buildscript中的依赖关系配置指定的，可以通过更新依赖关系来更新。使用`modCompile`映射重新映射Minecraft以及依赖关系配置中包含的依赖项。类，未在纱线映射的字段和方法给予中介的名称，如`class_1234`，`method_1234`和`field_1234`。
```
dependencies { 
    [...] 
    mappings“net.fabricmc：yarn：$ {project.yarn_mappings}” 
}
```

通过更改开发环境中的映射，您可以预期Minecraft中的类，方法和字段的名称以及任何包含的mod都已更改，并且您的代码可能必须更新以引用更改的名称。该过程可以部分自动化。您还必须运行`genSources`以使用更新的映射访问Minecraft源。

Loom的`remapJar`任务将产生主要mod工件，这是一个使用中间名称的内置jar。此外，如果存在`sourcesJar`任务，`remapSourcesJar`将使用中间名称生成源jar。这些jar可以作为mod安装，也可以包含在具有`modCompile`依赖关系配置的开发环境中。

* '-dev'`jar`（jar任务输出）不使用中间名称，因此没用。它不能作为开发环境之外的mod安装，只能在具有匹配映射的开发环境中工作。`remapJar`应该使用常规jar（任务输出），并使用`modCompile`依赖关系配置将其安装在开发环境中。
* 纱线名称仅适用于开发环境。在开发环境之外，只存在中间名称，这意味着代码与您查看和编写的内容完全不匹配。织机透明地为您处理此过渡，但在使用反射时要小心。

## 重新映射

重映射是将映射应用于代码，从一组名称转换为另一组名称的过程。可以重新映射Java源代码和编译的Java代码。它涉及根据映射更改引用的名称，以及仔细重命名方法以保留覆盖。虽然它会影响反射中使用的名称，但它不会改变代码的作用。

[Tiny Remapper(https://github.com/FabricMC/tiny-remapper)]是一个可以重新映射编译的Java代码的工具。它有一个命令行界面和一个可编程接口。Loom使用Tiny Remapper执行许多任务，Fabric Loader使用Tiny Remapper将Minecraft代码重新映射到中介。Loom还能够重新映射Java源代码。
## 混淆和反混淆

Minecraft Java版的发布是模糊的jar文件，这意味着它们是被编译的二进制文件，剥离了任何有意义的命名信息，只留下了裸逻辑。混淆背后的动机是防止逆向工程和减小文件大小。像Minecraft这样的Java程序反编译起来相当简单，但混淆是剥离了许多对修改目的有用的信息。人们可能想知道如何首先为Minecraft开发。

像Yarn这样的映射为开发提供了有意义的名称。使用映射可以理解Minecraft代码并为其创建mod。映射可以提供类，字段，方法，参数和局部变量的名称。很明显，这些映射并不完美。映射整个Minecraft涉及来自多个贡献者的大量猜测。映射可能不完整，有时会随着更准确的名称而发生变化。

## 调解

Minecraft混淆的一个属性是它在Minecraft版本之间并不总是一致的。可以abc在一个版本的Minecraft中调用一个类，abd在另一个版本中调用。相同的不一致适用于字段和方法。不一致性会在Minecraft版本之间产生二进制不兼容性。

Java代码可以针对库的一个版本进行编译，并且仍然可以与另一个版本一起使用，从而使两个版本的库二进制兼容。简而言之，如果库使用相同的方法和具有相同名称的字段公开至少相同的类，则实现二进制兼容性。由于缺乏二进制兼容性，使用Minecraft作为mods库时，Minecraft混淆的不一致性提出了挑战。

中间人为Minecraft版本定义了Minecraft内部的稳定名称。中介名称的目的是始终引用相同的类，字段或方法。与纱线的名字，中间的名字是没有意义的，而是遵循一个数字格式，如`class_1234`，`method_1234`和`field_1234`。

作为一个稳定的映射，中介可以使Minecraft二进制版本兼容多个版本（例如快照版本）！只保证版本之间不变的游戏部分的兼容性。当在开发环境之外安装时，Fabric Loader通过在游戏开始之前重新映射Minecraft（和Realms客户端）来提供具有中间名称的环境。通过查看安装了Fabric Loader的生产环境的崩溃报告可以观察到这一点，该生成环境将包含中间名称。使用Loom应用的中间名编译的Mod自然与此环境兼容。

# 汉化提示
如果你配置了forge 在C:\Users\用户名\.gradle 下发现这些混淆的对照(class_1234 method_1234 field_1234)