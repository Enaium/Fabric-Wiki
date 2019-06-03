# 添加方块实体(BlockEntity)
## 介绍

BlockEntity主要用于在块内存储数据。在创建之前，您需要一个Block。本教程将介绍BlockEntity类的创建，以及它的注册。

## 创建方块实体(BlockEntity)

最简单的块实体只是扩展`BlockEntity`，并使用默认构造函数。这是完全有效的，但不会为您的块授予任何特殊功能。
```
public class DemoBlockEntity extends BlockEntity {
   public DemoBlockEntity() {
      super(ExampleMod.DEMO_BLOCK_ENTITY);
   }
}
```
贝娄将向您展示如何创建该`ExampleMod.DEMO_BLOCK_ENTITY`领域。

您可以简单地将变量添加到此准系统类或实现接口，例如`Tickable`和`Inventory`添加更多功能。`Tickable`提供了一个`tick()`方法，对于世界上每个已加载的方块实例，每个tick都调用一次。同时`Inventory`允许你的BlockEntity与自动化程序（如漏斗）进行交互 - 以后可能会有一个单独的教程专门用于此接口。

## 重新锁定您的方块实体
创建完`BlockEntity`课程后，您需要注册它才能使其正常运行。这个过程的第一步是创建一个`BlockEntityType`链接你`Block`和你的人`BlockEntity`。假设您`Block`已创建并保存到局部变量`DEMO_BLOCK`，您将创建`BlockEntityType`与下面的行匹配。`modid:demo`应替换为您的Mod ID以及您希望在`BlockEntity`其下注册的名称。

本`BlockEntityType`应该在你的注册`onInitialize`方法，这是保证它能够在正确的时间注册。
```
public static BlockEntityType<DemoBlockEntity> DEMO_BLOCK_ENTITY;
 
@Override
public void onInitialize() {
   DEMO_BLOCK_ENTITY = Registry.register(Registry.BLOCK_ENTITY, "modid:demo", BlockEntityType.Builder.create(DemoBlockEntity::new, DEMO_BLOCK).build(null));
}
```

一旦你的`BlockEntityType`创建和注册如上所示，你可以`BlockEntityProvider`在你的`Block`班级中实现，并覆盖`createBlockEntity`你的新实例`BlockEntity`！
```
@Override
public BlockEntity createBlockEntity(BlockView blockView) {
   return new DemoBlockEntity(ExampleMod.DEMO_BLOCK_ENTITY);
}
```

## 序列化数据

如果要在您的数据中存储任何数据`BlockEntity`，则需要保存并加载它，或者仅在`BlockEntity`加载时保留数据，并且只要您返回数据，数据就会重置。幸运的是，保存和加载非常简单 - 您只需要覆盖`toTag()`和`fromTag()`。

`toTag()`返回 `CompoundTag`，其中应包含您的所有数据`BlockEntity`。此数据将保存到磁盘，如果需要`BlockEntity`与客户端同步数据，还会通过数据包发送。调用默认实现非常重要`toTag`，因为它将“识别数据”（位置和ID）保存到标记中。如果没有这个，您尝试保存的任何其他数据都将丢失，因为它与位置无关`BlockEntityType`。知道了这一点，下面的例子演示了如何从`BlockEntity`标签中保存一个整数。在该示例中，整数保存在键下`“number”`- 您可以将其替换为任何字符串，但是您的标记中的每个键只能有一个条目，并且您需要记住该键以便稍后检索数据。
```
public class DemoBlockEntity extends BlockEntity {
 
   // Store the current value of the number
   private int number = 7;
 
   public DemoBlockEntity() {
      super(ExampleMod.DEMO_BLOCK_ENTITY);
   }
 
   // Serialize the BlockEntity
   public CompoundTag toTag(CompoundTag tag) {
      super.toTag(tag);
 
      // Save the current value of the number to the tag
      tag.putInt("number", number);
 
      return tag;
   }
}
```



为了以后检索数据，您还需要覆盖`fromTag`。此方法与`toTag`- 相反，而不是将数据保存到a `CompoundTag`，您将获得之前保存的标记，使您可以检索所需的任何数据。与此同时`toTag`，您必须致电`super.fromTag`，并且需要使用相同的密钥来检索您保存的数据。要检索我们之前保存的数字，请参阅下面的示例。
```
// Deserialize the BlockEntity
public void fromTag(CompoundTag tag) {
   super.fromTag(tag);
   number = tag.getInt("number");
}
```

一旦实现了`toTag`和`fromTag`方法，您只需确保在正确的时间调用它们。每当您的BlockEntity数据发生变化并需要保存时，请致电`markDirty()`。这将强制`toTag`通过将块所在的块标记为脏来在下次保存世界时调用该方法。作为一般经验法则，只要`markDirty()`在`BlockEntity`类中更改任何自定义变量时，只需调用即可。

如果您需要将某些`BlockEntity`数据同步到客户端，出于渲染等目的，您应该`BlockEntityClientSerializablle`从`fabric-networking-blockentityFabric` API的模块中覆盖。此类提供`fromClientTag和toClientTag`方法，它们与前面讨论过的方法`fromTag`和`toTag`方法大致相同，只是它们专门用于在客户端上发送和接收数据。

## 概观
您现在应该拥有自己的`BlockEntity`，可以通过各种方式扩展以满足您的需求。您注册了一个`BlockEntityType`，并用它来连接您Block和您的`BlockEntity`课程。然后，您`BlockEntityProvider`在Block类中实现，并使用该接口提供新实例`BlockEntity`。最后，您学习了如何将数据保存到您的数据中`BlockEntity`，以及如何在以后重新使用。