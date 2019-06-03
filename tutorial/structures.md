# 生成结构
## 特征

本教程中使用的所有代码均可在此处获得：[fabric-structure-example-repo](https://github.com/Draylar/fabric-structure-example-repo)
### 介绍

我们将着眼于在您的世界中注册和放置结构。

要查看1.14香草结构的实例，IglooGenerator和IglooFeature是一个很好的起点。

您将需要一个功能和生成器来获得最基本的结构。该功能处理注册结构并在世界生成时加载它的过程 - 它回答诸如“我应该在这里产生吗？”之类的问题。和'我的名字是什么？' 如果您选择这样做，生成器将处理块的放置或加载到结构文件中。
### 创建一个功能
要创建基本功能，我们建议您创建一个扩展AbstractTempleFeature <DefaultFeatureConfig>的类。各种香草结构，如Shipwrecks，Igloos和Temples，使用AbstractTempleFeature作为基础。您必须覆盖以下方法：

* shouldStartAt：返回true用于测试目的。
* getName：结构的名称
* method_14021 [getRadius]：结构的半径，用于放置
* method_13774 [getSeed]：用于生成的种子，将0用于测试
您可以将DefaultFeatureConfig :: deserialize传递给构造函数以进行测试。

对于getStructureStartFactory，大多数vanilla结构构成了一个在其Feature类中扩展StructureStart的类：

```
public static class MyStructureStart extends StructureStart {
    public MyStructureStart (StructureFeature<?> structureFeature_1, int int_1, int int_2, Biome biome_1, MutableIntBoundingBox mutableIntBoundingBox_1, int int_3, long long_1) {
        super(structureFeature_1, int_1, int_2, biome_1, mutableIntBoundingBox_1, int_3, long_1);
    }
    @Override
    public void initialize(ChunkGenerator<?> chunkGenerator, StructureManager structureManager, int chunkX, int chunkZ, Biome biome) {
        DefaultFeatureConfig defaultFeatureConfig = chunkGenerator.getStructureConfig(biome, MyMainclass.myFeature);
        int x = chunkX * 16;
        int z = chunkZ * 16;
        BlockPos startingPos = new BlockPos(x, 0, z);
        Rotation rotation = Rotation.values()[this.random.nextInt(Rotation.values().length)];
        MyGenerator.addParts(structureManager, startingPos, rotation, this.children, this.random, defaultFeatureConfig);
        this.setBoundingBoxFromChildren();
    }
}
```

当世界试图在新结构中产生时，就会调用它，这是您的Feature和Generator之间的差距。主类中对变量的引用尚不存在，但我们将在最后创建它。您也可以将配置设置为等于新的DefaultFeatureConfig。您可以在getStructureStartFactory中返回此返回MyStructureStart :: new。

这是结构文件和直接从generate方法生成的部分。有两种方法可以解决这个问题：

* 如果需要，您可以简单地覆盖Feature类中的generate，并使用setBlockState直接在世界中放置块。这是一个有效的选项，并且在1.13之前很受欢迎。
* 使用结构文件和生成器。这些在这一点上相当强大，强烈推荐。
### 创建一个生成器
您可能已经注意到，我们需要创建一个生成器。我们将其命名为MyGenerator，并在StructureStart类的initialize方法中引用它。它不需要覆盖任何东西，但确实需要以下内容：

* 指向结构文件的标识符; 如果你需要一个例子，请使用“igloo / top”。
* 某种安装方法 - addParts是个好名字：

```
public  static  void addParts （ StructureManager structureManager_1，BlockPos blockPos_1，Rotation rotation_1，
    List < StructurePiece > list_1，Random random_1，DefaultFeatureConfig featureConfig ）
 
}
```

在addParts方法中，您可以选择将哪些结构块添加到生成过程中。你可以添加这样一块：
`list_1.add(new MyGenerator.Piece(structureManager_1, identifier, blockPos, rotation_1));`

标识符是我们最近创建的路径。

我们现在要创建我们刚引用的那篇文章; 创建一个名为Piece的类，它在您的生成器类中扩展SimpleStructurePiece 。

覆盖所需的方法，并添加一个构造函数，该构造函数接受StructureManager，Identifier，BlockPos和Rotation。toNbt不是必需的，但如果您需要它可用。我们还使用不同的参数实现我们自己的setStructureData，因此它不是覆盖。我们还有2个构造函数：1个用于我们自己的部分，1个用于注册表。一个基本模板是：

```
public static class Piece extends SimpleStructurePiece {
    private Rotation rotation;
    private Identifier template;
 
    public Piece(StructureManager structureManager_1, Identifier identifier_1, BlockPos blockPos_1, Rotation rotation_1) {
        super(MyModClass.myStructurePieceType, 0);
 
        this.pos = blockPos_1;
        this.rotation = rotation_1;
        this.template = identifier_1;
 
        this.setStructureData(structureManager_1);
    }
 
    public Piece(StructureManager structureManager_1, CompoundTag compoundTag_1) {
        super(MyModClass.myStructurePieceType, compoundTag_1);
        this.identifier = new Identifier(compoundTag_1.getString("Template"));
        this.rotation = Rotation.valueOf(compoundTag_1.getString("Rot"));
        this.setStructureData(structureManager_1);
    }
 
    @Override
    protected void toNbt(CompoundTag compoundTag_1) {
        super.toNbt(compoundTag_1);
        compoundTag_1.putString("Template", this.template.toString());
        compoundTag_1.putString("Rot", this.rotation.name());
    }
 
    public void setStructureData(StructureManager structureManager) {
        Structure structure_1 = structureManager.getStructureOrBlank(this.identifier);
        StructurePlacementData structurePlacementData_1 = (new StructurePlacementData()).setRotation(this.rotation).setMirrored(Mirror.NONE).setPosition(pos).addProcessor(BlockIgnoreStructureProcessor.IGNORE_STRUCTURE_BLOCKS);
        this.setStructureData(structure_1, this.pos, structurePlacementData_1);
    }
 
    @Override
    protected void handleMetadata(String string_1, BlockPos blockPos_1, IWorld iWorld_1, Random random_1, MutableIntBoundingBox mutableIntBoundingBox_1) {
 
    }
 
    @Override
    public boolean generate(IWorld iWorld_1, Random random_1, MutableIntBoundingBox mutableIntBoundingBox_1, ChunkPos chunkPos_1) {
 
    }
}
```

handleMetadata是您查看结构中的数据块并根据您找到的内容执行任务的地方。在香草结构中，数据块被放置在箱子上方，因此在这种方法中可以用战利品填充它们。

我们将StructurePieceType设置为MyModClass.myStructurePiece类型; 这是保存已注册结构件的变量。完成generate函数之后我们将处理它，它设置结构的位置并生成它：


```
@Override
public boolean generate(IWorld iWorld_1, Random random_1, MutableIntBoundingBox mutableIntBoundingBox_1, ChunkPos chunkPos_1) {
    int yHeight = iWorld_1.getTop(Heightmap.Type.WORLD_SURFACE_WG, this.pos.getX() + 8, this.pos.getZ() + 8);
    this.pos = this.pos.add(0, yHeight - 1, 0);
    return super.generate(iWorld_1, random_1, mutableIntBoundingBox_1, chunkPos_1);
}
```

在这种情况下，我们只需在块的中间获得最高块的y位置，并在该位置生成结构。

注册功能
最后一步是注册我们的功能。我们需要注册：

* StructurePieceType
* StructureFeature <DefaultFeatureConfig>
* StructureFeature <？>
我们还需要将结构添加到STRUCTURES列表中，并将其作为特征和生成步骤添加到每个生物群系中。

注册件类型：

`public static final StructurePieceType myStructurePieceType = Registry.register(Registry.STRUCTURE_PIECE, "my_piece", MyGenerator.Piece::new);`

注册功能：

public static final StructureFeature<DefaultFeatureConfig> myFeature = Registry.register(Registry.FEATURE, "my_feature", new MyFeature());
Registering structure:

注册结构：

public static final StructureFeature<?> myStructure = Registry.register(Registry.STRUCTURE_FEATURE, "my_structure", myFeature);

要将您的功能放入功能列表中，您可以使用：

Feature.STRUCTURES.put("My Awesome Feature", myFeature);

对于测试，最好将您的功能注册到每个生物群系并将产卵率设置为100％，这样您就可以确定它的产生和工作。你可能不希望你的结构漂浮在水中，所以我们也会过滤掉它。通过迭代生物群系列表并将其添加为特征和生成步骤，将其添加到每个生物群系：
```
for(Biome biome : Registry.BIOME) {
    if(biome.getCategory() != Biome.Category.OCEAN && biome.getCategory() != Biome.Category.RIVER) {
        biome.addStructureFeature(myFeature, new DefaultFeatureConfig());
        biome.addFeature(GenerationStep.Feature.SURFACE_STRUCTURES, Biome.configureFeature(myFeature, new DefaultFeatureConfig(), Decorator.CHANCE_PASSTHROUGH, new ChanceDecoratorConfig(0)));
    }
}
```

ChanceDecoratorConfig的论点基本上是在生成之前会跳过多少块。0是每个块，1是彼此，100是每100。

您需要将您的结构添加为一个功能，以便您的生物群系知道它存在，然后作为生成步骤，以便实际生成它。

加载到你的世界，如果一切顺利，你应该遇到很多冰屋。