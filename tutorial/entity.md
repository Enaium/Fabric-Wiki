# 添加实体(Entity)
## 介绍

实体是在向游戏添加项目和阻止后采取的下一步。

要添加实体，您需要3个主要类：

* 实体类，它为您的生物提供逻辑/AI
* Renderer类，允许您将实体连接到模型
* 一个Model类，这是你的玩家在游戏中看到的

我们将创建一个cookie爬行器，它在爆炸时随处可以启动cookie。

## 注册实体(Entity)

与块和项目不同，您基本上总是希望完全专注于您的实体。我们正在制作一个爬行克隆，所以我们将使我们的实体类扩展CreeperEntity：
```
public class CookieCreeperEntity extends CreeperEntity {
    [...]
}
```


您的IDE应该指示您创建一个与现在的super-do匹配的构造函数。

要注册您的实体，我们将使用Registry.ENTITY_TYPE。要获取所需的注册表实例，您可以使用EntityType.Builder或FabricEntityTypeBuilder-我们建议使用第二个。
```
public static final EntityType<CookieCreeperEntity> COOKIE_CREEPER =
    Registry.register(
        Registry.ENTITY_TYPE,
        new Identifier("wiki_entity", "cookie_creeper"),
        FabricEntityTypeBuilder.create(EntityCategory.AMBIENT, CookieCreeperEntity::new).size(1, 2).build()
    );
```


size（）方法允许您设置实体的hitbox。爬行者宽1块，高2块，所以我们将使用（1,2）。

如果你此时加载你的游戏，你将能够使用/召唤来查看你的创作。如果一切顺利，它应该看起来像一个普通的爬行者。我不建议进入生存。

## 创建渲染器
我们的Cookie creeper自动拥有一个模型，因为它扩展了Creeper类。我们要将皮肤变成饼干皮肤，而不是正常的绿色迷彩颜色。

首先，创建一个MobEntityRenderer类。MobEntityRenderer有两种通用类型：实体和模型。因为我们正在使用Creeper模型开始，所以我们还必须通过给它一个类型来告诉Creeper模型这不是Creeper实体。

```
public class CookieCreeperRenderer extends MobEntityRenderer<CookieCreeperEntity, CreeperEntityModel<CookieCreeperEntity>> {
    [...]
}
```

您需要覆盖getTexture方法以及添加构造函数。默认情况下，构造函数有3个参数（EntityRenderDispatcher，EntityModel，float），但我们可以删除最后的2个并自己创建它们：
```
public CookieCreeperRenderer(EntityRenderDispatcher entityRenderDispatcher_1)
{
    super(entityRenderDispatcher_1, new CreeperEntityModel<>(), 1);
}
```
对于getTexture方法，您需要返回模型的纹理。如果为null，则您的实体将不可见。这是100％保证的方式，花3个小时试图找出你的模型不工作的原因。为了您的方便，我创建了一个可供所有人使用的Cookie Creeper纹理，您可以从[此处](https://imgur.com/a/o3TOlxN)下载。

默认的实体纹理文件夹约定是：textures/entity/entity_name/entity.png。这是一个示例实现：
```
@Override
protected Identifier getTexture(CookieCreeperEntity cookieCreeperEntity)
{
    return new Identifier("wiki_entity:textures/entity/cookie_creeper/creeper.png");
}
```
文件存储在resources/assets/wiki_entity/textures/entity/cookie_creeper/creeper.png中。

最后，您需要将实体连接到渲染器。由于渲染只发生在客户端，因此您应该始终在ClientModInitializer中执行此类工作：
```
EntityRendererRegistry.INSTANCE.register(CookieCreeperEntity.class, (entityRenderDispatcher, context) -> new CookieCreeperRenderer(entityRenderDispatcher));
```
这将我们的实体链接到我们的新渲染器类。如果您加载到游戏中，您应该看到我们的新朋友：

https://i.imgur.com/8Gfc2sV.jpg

如果您想使用自己的模型，可以创建一个扩展EntityModel的新类，并在我们的渲染器中为它交换Creeper模型。这非常复杂，将在单独的教程中介绍。