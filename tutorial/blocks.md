# 添加一个方块
### 介绍
要向mod添加块,您需要注册Block类的新实例.要更好地控制块,可以创建自定义块类.我们还将考虑添加块模型.在本教程中,"wikitut"命名空间和"example_block"名称用作占位符,应替换为mod和块的适当值.

### 创建一个方块
首先,在main mod类中创建一个Block实例.Block的构造函数使用FabricBlockSettings构建器来设置块的基本属性,例如硬度和阻力,如下所示：
```
public class ExampleMod implements ModInitializer
{
    // an instance of our new block
    public static final Block EXAMPLE_BLOCK = new Block(FabricBlockSettings.of(Material.METAL).build());
    [...]
}
```

### 注册一个方块
我们将注册我们的块,就像您将项目或任何其他添加.您可以调用Registry.register来执行此操作; 第一个参数是注册表类型,第二个参数是注册主题的标识符,final是您注册的实例.
```
public class ExampleMod implements ModInitializer
{
    // block creation
    […]
 
    @Override
    public void onInitialize()
    {
        Registry.register(Registry.BLOCK, new Identifier("wikitut", "example_block"), EXAMPLE_BLOCK);
    }
}
```


您的块将无法作为项目访问,但可以使用`/setblock ~~~ modid：name`在游戏中查看.

### 注册BlockItem
在大多数情况下,您希望能够使用项目放置块.为此,您需要在项目注册表中注册相应的BlockItem.您可以通过在Registry.ITEM下注册BlockItem实例来完成此操作.项的注册表名称通常应与块的注册表名称相同.

```
public class ExampleMod implements ModInitializer
{
    // block creation
    […]
 
    @Override
    public void onInitialize()
    {
        // block registration
        [...]
 
        Registry.register(Registry.ITEM, new Identifier("wikitut", "example_block"), new BlockItem(EXAMPLE_BLOCK, new Item.Settings().itemGroup(ItemGroup.MISC)));
    }
}
```

### 给你的块一个模型
你可能已经注意到,这个块在游戏中只是一个紫色和黑色的棋盘图案.这是Minecraft向你展示该块没有模型的方式.对块进行建模比对项目建模要困难一些.您将需要三个文件：块状态文件,块模型文件和项目模型文件（如果块具有BlockItem）.如果您不使用香草,也需要纹理.文件应位于此处：

```
Blockstate: src/main/resources/assets/wikitut/blockstates/example_block.json
Block Model: src/main/resources/assets/wikitut/models/block/example_block.json
Item Model: src/main/resources/assets/wikitut/models/item/example_block.json
Block Texture: src/main/resources/assets/wikitut/textures/block/example_block.png
```

blockstate文件根据它的blockstate确定块应该使用哪个模型.由于我们的块只有一个状态,因此文件很简单：

`src/main/resources/assets/wikitut/blockstates/example_block.json`
```
{
  "variants": {
    "": { "model": "wikitut:block/example_block" }
  }
}
```

块模型文件定义块的形状和纹理.我们将使用block / cube_all,这将允许我们在块的所有边上轻松设置相同的纹理.

`src/main/resources/assets/wikitut/models/block/example_block.json`
```
{
  "parent": "block/cube_all",
  "textures": {
    "all": "wikitut:block/example_block"
  }
}
```


在大多数情况下,您希望块看起来相同.为此,您可以创建一个继承自块模型文件的项目文件：

`src/main/resources/assets/wikitut/models/item/example_block.json`

```
{
  "parent": "wikitut:block/example_block"
}
```

加载我的世界,你的块最终应该有一个纹理！

### 添加方块战利品表
块必须有一个战利品表,以便在块被破坏时丢弃任何物品.假设您已为块创建了一个项目并使用与该块相同的名称进行了注册,则以下文件将生成常规块丢弃`src/main/resources/data/wikitut/loot_tables/blocks/example_block.json`注意这里是data目录下不是assets

```
    {
      "type": "minecraft:block",
      "pools": [
        {
          "rolls": 1,
          "entries": [
            {
              "type": "minecraft:item",
              "name": "wikitut:example_block"
            }
          ],
          "conditions": [
            {
              "condition": "minecraft:survives_explosion"
            }
          ]
        }
      ]
    }


```

在生存模式中被破坏时,该块现在将丢弃一个物品.

### 创建一个Block类
创建一个简单的块时,上述方法效果很好,但有时您需要一个具有独特机制的特殊块.我们将创建一个单独的类来扩展Block来执行此操作.该类需要一个接受BlockSettings参数的构造函数.

```
public class ExampleBlock extends Block
{
    public ExampleBlock(Settings settings)
    {
        super(settings);
    }
}
```

就像我们在项目教程中所做的那样,您可以覆盖块类中的方法以获得自定义功能.假设您希望您的块透明：
```
    @Environment(EnvType.CLIENT)
    public BlockRenderLayer getRenderLayer() {
        return BlockRenderLayer.TRANSLUCENT;
    }
```

要将此块添加到游戏中,请在注册时将新块替换为新的ExampleBlock.
```
public class ExampleMod implements ModInitializer
{
    // an instance of our new item
    public static final ExampleBlock EXAMPLE_BLOCK = new ExampleBlock(Block.Settings.of(Material.STONE));
    [...]
}
```

您的自定义块现在应该是透明的！