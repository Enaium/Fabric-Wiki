# 添加盔甲
## 介绍
尽管Armor的添加要比普通的方块/物品复杂一些，但是一旦您了解了它，制作起来就变得很简单。要添加Armor，我们将首先创建一个自定义材质类，然后注册项目。我们还将看一下如何纹理化它们。

## 创建盔甲材料类
由于需要使用新名称设置新的盔甲（以及诸如盔甲点和耐用性之类的其他内容），因此我们必须为自定义ArmorMaterial创建一个新类。

此类将实现ArmorMaterial并将是枚举类型。它需要很多参数，主要是名称，耐用性等，因此现在我们将其留空。现在不用担心任何错误。

由于需要大量的论据，因此下面列出了其中的每个论点。

* 字符串名称。稍后将用作“盔甲标签”。
* 耐久性乘数。这是将用于基于基本值确定耐久性的数字。
* 盔甲值或香草代码中的“保护量”。这将是一个int数组。
* 附魔。这将是盔甲在附魔书中获得高等级或多重附魔的可能性。
* 声音事件。普通盔甲使用的标准是SoundEvents.ITEM_ARMOR_EQUIP_X，X是盔甲类型。
* 韧性。这是第二个保护值，在这种情况下，盔甲对高价值攻击更为耐久。
* 修复成分。这将是一个Supplier<Ingredient>实例，而不是一个实例Item，该实例将稍稍过去。有了这些参数，现在看起来应该像这样：
```
public enum CustomArmorMaterial implements ArmorMaterial {
    CustomArmorMaterial(String name, int durabilityMultiplier, int[] armorValueArr, int enchantability, SoundEvent soundEvent, float toughness, Supplier<Ingredient> repairIngredient) {
 
    }
}
```
我们还必须定义这些值并使其可用，所以现在看起来像这样：
```
blic enum CustomArmorMaterial implements ArmorMaterial {
    private final String name;
    private final int durabilityMultiplier;
    private final int[] armorValues;
    private final int enchantability;
    private final SoundEvent equipSound;
    private final float toughness;
    private final Lazy<Ingredient> repairIngredient;
 
    CustomArmorMaterial(String name, int durabilityMultiplier, int[] armorValueArr, int enchantability, SoundEvent soundEvent, float toughness, Supplier<Ingredient> repairIngredient) {
        this.name = name;
        this.durabilityMultiplier = durabilityMultiplier;
        this.armorValues = armorValueArr;
        this.enchantability = enchantability;
        this.equipSound = soundEvent;
        this.toughness = toughness;
        this.repairIngredient = new Lazy(repairIngredient); // We'll need this to be a Lazy type for later.
    }
}
```
`ArmorMaterial` 还需要其他几种方法，因此我们将在此处快速添加它们。

我们还必须添加基本耐用性值，因此现在我们将使用原始值 `[13, 15, 16, 11]`
```
public enum CustomArmorMaterial implements ArmorMaterial {
    private static final int[] baseDurability = {13, 15, 16, 11};
    private final String name;
    private final int durabilityMultiplier;
    private final int[] armorValues;
    private final int enchantability;
    private final SoundEvent equipSound;
    private final float toughness;
    private final Lazy<Ingredient> repairIngredient;
 
    CustomArmorMaterial(String name, int durabilityMultiplier, int[] armorValueArr, int enchantability, SoundEvent soundEvent, float toughness, Supplier<Ingredient> repairIngredient) {
        this.name = name;
        this.durabilityMultiplier = durabilityMultiplier;
        this.armorValues = armorValueArr;
        this.enchantability = enchantability;
        this.equipSound = soundEvent;
        this.toughness = toughness;
        this.repairIngredient = new Lazy(repairIngredient);
    }
 
    public int getDurability(EquipmentSlot equipmentSlot_1) {
        return BASE_DURABILITY[equipmentSlot_1.getEntitySlotId()] * this.durabilityMultiplier;
    }
 
    public int getProtectionAmount(EquipmentSlot equipmentSlot_1) {
        return this.protectionAmounts[equipmentSlot_1.getEntitySlotId()];
    }
 
    public int getEnchantability() {
        return this.enchantability;
    }
 
    public SoundEvent getEquipSound() {
        return this.equipSound;
    }
 
    public Ingredient getRepairIngredient() {
        // We needed to make it a Lazy type so we can actually get the Ingredient from the Supplier.
        return this.repairIngredientSupplier.get();
    }
 
    @Environment(EnvType.CLIENT)
    public String getName() {
        return this.name;
    }
 
    public float getToughness() {
        return this.toughness;
    }
}
```
现在您已具备盔甲材料类别的基础知识，现在可以制作自己的盔甲材料。可以在代码顶部完成此操作，如下所示：


```
public enum CustomArmorMaterial implements ArmorMaterial {
    WOOL("wool", 5, new int[]{1,3,2,1}, 15, SoundEvents.BLOCK_WOOL_PLACE, 0.0F, () -> {
        return Ingredient.ofItems(Items.WHITE_WOOL);
    });
    [...]
}
```
随时更改任何值。

## 创建盔甲物品
回到主类，您现在可以像这样创建它：
```
public class ExampleMod implements ModInitializer {
    public static final Item WOOL_HELMET = new ArmorItem(CustomArmorMaterial.WOOL, EquipmentSlot.HEAD, (new Item.Settings().group(ItemGroup.COMBAT)));
    public static final Item WOOL_CHESTPLATE = new ArmorItem(CustomArmorMaterial.WOOL, EquipmentSlot.CHEST, (new Item.Settings().group(ItemGroup.COMBAT)));
    public static final Item WOOL_LEGGINGS = new ArmorItem(CustomArmorMaterial.WOOL, EquipmentSlot.LEGS, (new Item.Settings().group(ItemGroup.COMBAT)));
    public static final Item WOOL_BOOTS = new ArmorItem(CustomArmorMaterial.WOOL, EquipmentSlot.FEET, (new Item.Settings().group(ItemGroup.COMBAT)));
}
```

## 注册盔甲项目
以与注册普通物品相同的方式注册它们。


```
   [...]
    public void onInitialize() {
        Registry.register(Registry.ITEM,new Identifier("tutorial","wool_helmet"), WOOL_HELMET);
	Registry.register(Registry.ITEM,new Identifier("tutorial","wool_chestplate"), WOOL_CHESTPLATE);
	Registry.register(Registry.ITEM,new Identifier("tutorial","wool_leggings"), WOOL_LEGGINGS);
	Registry.register(Registry.ITEM,new Identifier("tutorial","wool_boots"), WOOL_BOOTS);
    }
```

## 纹理化
由于您已经知道如何制作项目模型和纹理，因此在此不再赘述。（它们的处理与物品完全相同。）由于Minecraft认为它是香草盔甲，因此盔甲的材质略有不同。为此，我们将创建一个pack.mcmeta文件，以便我们的资源可以像资源包一样工作。


`src / main / resources / pack.mcmeta`
```
{
    "pack":{
        "pack_format":4,
        "description":"Tutorial Mod"
    }
}
```


现在，您终于可以在中放置纹理了`src/main/resources/assets/minecraft/textures/models/armor/`请记住，它们被分成两张图片。（请使用香草纹理作为参考。）

如果您遵循了所有步骤，则现在应该可以拥有全套的防具套装！