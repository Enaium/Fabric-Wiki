# Modding Tips
以下是从为API用户提供建议的经验中收集的各种与Fabric相关的修改技巧的集合.
## Basics （API）
* 由于Fabric的API开发基于注入的方法,我们不倾向于以最终用户可见的方式完全修补类.因此,当您遇到无法做到的事情时,您可能偶尔会发现vanilla类的Fabric扩展.例如：
>* Block.Settings→FabricBlockSettings
>* EntityType.Builder→FabricEntityTypeBuilder
>* 在正在使用官方配置系统的同时,现在的一个替代是使用Java .properties或JSON.
>* 对于内置资源包或数据包,请确保分别存在"assets / [mod id]"或"data / [mod id]"目录路径！IDEA用户可能会发现自己不小心创建了"assets.[mod id]"目录 - 这不起作用.

## Mixins


* 要将类强制转换为未实现的接口,或者转换最终类,或将mixin转换为目标类,可以使用"（TargetClass）（Object）sourceClassObject"技巧.
* 要修改构造函数,请使用"<init>"（或"<clinit>"表示静态构造函数）作为方法名称.请注意,构造函数上的@Inject仅适用于@At（"RETURN"） - 没有其他形式的注入正式支持！
* @Redirect和@ModifyConstant mixins目前无法嵌套（同时在同一区域中由多个mod应用）.这可能会在开发的后期发生变化 - 但是,就目前而言,与@Overwrite一起,请尽可能避免使用它们（或者讨论将结果带到Fabric的API,或者 - 对于更多利基的事情 - 考虑将其放入JAR-in-JAR中那个）.
* 如果您要添加自定义字段或方法,特别是如果它们未附加到接口 - 请在其前面添加"[modid] _"或其他唯一字符串.基本上,"mymod_secretValue"而不是"secretValue".这是为了避免添加以相同方式命名的字段或方法的mod之间的冲突！

## Networking
* 数据包总是在网络线程上开始执行,但是对大多数Minecraft事物的访问都不是线程安全的.通常,如果您不确定自己在做什么,则需要在网络线程上解析数据包（读取所有值）,然后使用任务队列在主服务器/客户端线程上执行其他操作.