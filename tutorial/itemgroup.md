# 物品组
### 创建一个简单的物品组
要`ItemGroup`在物品组中正确显示，请使用`FabricItemGroupBuilder`以创建它们：

```
public class ExampleMod implements ModInitializer
{
	// ...
	public static final ItemGroup ITEM_GROUP = FabricItemGroupBuilder.build(
		new Identifier("mod_id", "general"),
		() -> new ItemStack(Blocks.COBBLESTONE));
 
	public static final ItemGroup OTHER_GROUP = FabricItemGroupBuilder.create(
		new Identifier("mod_id", "other"))
		.icon(() -> new ItemStack(Items.BOWL))
		.build();
	// ...
}
```

一旦`FabricItemGroupBuilder#build`被调用，你的团队将在创意菜单添加到物品组的列表。

确保替换参数1）您传递给`Identifier`构造函数，并使用您的实际mod ID和稍后要为您的物品组进行本地化的翻译键2）。

#### 将商品添加到商品分组
要将物品添加到物品组，请`Item.Settings#itemGroup`在创建物品时调用，方法与将其添加到vanilla物品组相同：

`public static final Item YOUR_ITEM = new Item(new Item.Settings().itemGroup(ExampleMod.ITEM_GROUP));`

#### 使物品组按特定顺序显示特定物品
打电话`FabricItemGroupBuilder#appendItems`给任何人。然后，您可以按某种顺序将所需的堆栈添加到给定列表中。可用于在组中放置空格。 `Consumer<List<ItemStack>>ItemStack.EMPTY`

```
public class ExampleMod implements ModInitializer
{
	// ...
	public static final ItemGroup ITEM_GROUP = FabricItemGroupBuilder.build(
		new Identifier("mod_id", "general"),
		() -> new ItemStack(Blocks.COBBLESTONE));
 
	public static final ItemGroup OTHER_GROUP = FabricItemGroupBuilder.create(
		new Identifier("mod_id", "other"))
		.icon(() -> new ItemStack(Items.BOWL))
		.appendItems(stacks ->
		{
			stacks.add(new ItemStack(Blocks.BONE_BLOCK));
			stacks.add(new ItemStack(Items.APPLE));
			stacks.add(PotionUtil.setPotion(new ItemStack(Items.POTION), Potions.WATER));
			stacks.add(ItemStack.EMPTY);
			stacks.add(new ItemStack(Items.IRON_SHOVEL));
		})
		.build();
	// ...
}
```

![img1](https://raw.githubusercontent.com/Lightcolour-666/Fabric-Wiki/master/tutorial/img/itemgroup/item_group_append_items.png)

---
1） 请记住，传递给`Identifier`构造函数的参数只能包含某些字符。
两个参数（`namespace`＆`path`）可以包含小写字母，数字，下划线，句点或短划线。`[a-z0-9_.-]`
第二个参数（the `path`）也可以包含斜杠。`[a-z0-9/._-]`
避免使用其他符号，否则`InvalidIdentifierException`会被抛出！
2） 第一个例子的完整翻译密钥`ItemGroup`是`itemGroup.mod_id.general`