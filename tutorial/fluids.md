# 添加流体
在这里，我们将介绍自定义流体的创建。如果计划创建多个流体，建议创建一个抽象的基本流体类，在其中设置必要的默认值，这些默认值将在其子类中共享。我们还将使其像湖泊一样在世界上产生。
## 使用abstract 流体
Vanilla 流体扩展了net.minecraft.fluid.BaseFluid类，抽象流体也将扩展。可能是这样的：
```
public abstract class BasicFluid extends BaseFluid
{
    /**
     * @return does it produce infinite fluid (like water)?
     */
    @Override
    protected boolean isInfinite()
    {
        return false;
    }
 
    // make it transparent
    @Override
    protected BlockRenderLayer getRenderLayer()
    {
        return BlockRenderLayer.TRANSLUCENT;
    }
 
    /**
     *
     * @return an associated item that "holds" this fluid
     */
    @Override
    public abstract Item getBucketItem();
 
    /**
     *
     * @return a blockstate of the associated {@linkplain net.minecraft.block.FluidBlock} with {@linkplain net.minecraft.block.FluidBlock#LEVEL}
     */
    @Override
    protected abstract BlockState toBlockState(FluidState var1);
 
    /**
     *
     * @return flowing static instance of this fluid
     */
    @Override
    public abstract Fluid getFlowing();
 
    /**
     *
     * @return still static instance of this fluid
     */
    @Override
    public abstract Fluid getStill();
 
    // how much does the height of the fluid block decreases
    @Override
    protected int getLevelDecreasePerBlock(ViewableWorld world)
    {
        return 1;
    }
 
    /**
     * 
     * @return update rate
     */
    @Override
    public int getTickRate(ViewableWorld world)
    {
        return 5;
    }
 
    @Override
    protected float getBlastResistance()
    {
        return 100;
    }
 
    // this seems to determine fluid's spread speed (higher value means faster)
    @Override
    protected int method_15733(ViewableWorld world)
    {
        return 4;
    }
 
    // I don't know what this does, but it's present in the water fluid
    @Override
    protected void beforeBreakingBlock(IWorld world, BlockPos blockPos, BlockState blockState) {
        BlockEntity blockEntity = blockState.getBlock().hasBlockEntity() ? world.getBlockEntity(blockPos) : null;
        Block.dropStacks(blockState, world.getWorld(), blockPos, blockEntity);
    }
 
    // also don't know what it does
    public boolean method_15777(FluidState fluidState, BlockView blockView, BlockPos blockPos, Fluid fluid, Direction direction) {
        return direction == Direction.DOWN;
    }
 
    /**
     *
     * @return is given fluid instance of this fluid?
     */
    @Override
    public abstract boolean matchesType(Fluid fluid);
 
}
```
实作
现在让我们进行实际的研究。它将具有静止和流动的变体；将其命名为“酸(Acid)”：

```
public abstract class Acid extends BasicFluid
{
    @Override
    public Item getBucketItem()
    {
        return null;
    }
    @Override
    protected BlockState toBlockState(FluidState var1)
    {
        return null;
    }
 
    @Override
    public Fluid getFlowing()
    {
        return null;
    }
 
    @Override
    public Fluid getStill()
    {
        return null;
    }
 
    @Override
    public boolean matchesType(Fluid fluid)
    {
        return false;
    }
 
    // still acid
    public static class Still extends Acid
    {
 
        @Override
        public boolean isStill(FluidState fluidState)
        {
            return true;
        }
 
        /**
         * @return height of the fluid block
         */
        @Override
        public int getLevel(FluidState fluidState)
        {
            return 8;
        }
    }
 
    // flowing acid
    public static class Flowing extends  Acid
    {
 
        @Override
        public boolean isStill(FluidState fluidState)
        {
            return false;
        }
 
        /**
         * @return height of the fluid block
         */
        @Override
        public int getLevel(FluidState fluidState)
        {
            return fluidState.get(LEVEL);
        }
 
        @Override
        protected void appendProperties(StateFactory.Builder<Fluid, FluidState> stateFactoryBuilder)
        {
            super.appendProperties(stateFactoryBuilder);
            stateFactoryBuilder.add(LEVEL);
        }
    }
}
```

接下来，我们将制作静态和动态酸变体的静态实例，以及一个酸桶。在您的ModInitializer中：

```
  public static Acid stillAcid;
    public static Acid flowingAcid;
 
    public static BucketItem acidBucket;
 
    @Override
    public void onInitialize()
    {
 
        stillAcid = Registry.register(Registry.FLUID, new Identifier(MODID,"acid_still"), new Acid.Still());
        flowingAcid = Registry.register(Registry.FLUID, new Identifier(MODID,"acid_flowing"), new Acid.Flowing());
 
        acidBucket = new BucketItem(stillAcid, new Item.Settings().maxCount(1));
        Registry.register(Registry.ITEM, new Identifier(MODID,"acid_bucket"), acidBucket);
    }    
```
为了使自定义流体表现得像水或熔岩，您必须将其添加到相应的流体标签中：制作一个文件“ data / minecraft / tags / fluids / water.json”，并在其中写入流体的标识符：
```
{
  "replace": false,
  "values": [
    "modid:acid_still",
    "modid:acid_flowing"
  ]
}
```
制作流体块
接下来，我们需要创建一个代表世界酸的图块。net.minecraft.block.FluidBlock是我们需要使用的类，但是出于“mojang”的原因，其构造函数受到了保护。该解决方案是众所周知的-创建它的子类并更改构造函数的可见性：
```
public class BaseFluidBlock extends FluidBlock
{
    public BaseFluidBlock(BaseFluid fluid, Settings settings)
    {
        super(fluid, settings);
    }
}
```
现在创建一个静态块实例：
```
    ...
 
    public static FluidBlock acid;
 
    @Override
    public void onInitialize()
    {
 
        ...
 
        acid = new BaseFluidBlock(stillAcid, FabricBlockSettings.of(Material.WATER).dropsNothing().build());
        Registry.register(Registry.BLOCK, new Identifier(MODID, "acid_block"), acid);
    }    
```
现在，当我们有了这些静态对象时，我们回到Acid类并完成重写的方法：
```
public abstract class Acid extends BasicFluid
{
    @Override
    public Item getBucketItem()
    {
        return Mod.acidBucket;
    }
 
    @Override
    protected BlockState toBlockState(FluidState fluidState)
    {
        //don't ask me what **method_15741** does...
        return Mod.acid.getDefaultState().with(FluidBlock.LEVEL, method_15741(fluidState));
    }
 
    @Override
    public Fluid getFlowing()
    {
        return Mod.flowingAcid;
    }
 
    @Override
    public Fluid getStill()
    {
        return Mod.stillAcid;
    }
 
    @Override
    public boolean matchesType(Fluid fluid_1)
    {
        return fluid_1==Mod.flowingAcid || fluid_1==Mod.stillAcid;
    }
}    
```
现在我们可以断言Acid类已完成。

## 渲染设置
是时候做客户端的事情了。在ClientModInitializer中，您需要为流体指定精灵的位置并定义其渲染。我将重复使用水纹理，只是更改应用于它们的颜色。
```
 @Override
    public void onInitializeClient()
    {
 
        // adding the sprites to the block texture atlas
        ClientSpriteRegistryCallback.event(SpriteAtlasTexture.BLOCK_ATLAS_TEX).register((spriteAtlasTexture, registry) -> {
 
            Identifier stillSpriteLocation = new Identifier("block/water_still");
            Identifier dynamicSpriteLocation = new Identifier("block/water_flow");
            // here I tell to use only 16x16 area of the water texture
            FabricSprite stillAcidSprite = new FabricSprite(stillSpriteLocation, 16, 16);
            // same, but 32
            FabricSprite dynamicAcidSprite = new FabricSprite(dynamicSpriteLocation, 32, 32);
 
            registry.register(stillAcidSprite);
            registry.register(dynamicAcidSprite);
 
 
            // this renderer is responsible for drawing fluids in a world
            FluidRenderHandler acidRenderHandler = new FluidRenderHandler()
            {
                // return the sprites: still sprite goes first into the array, flowing/dynamic goes last
                @Override
                public Sprite[] getFluidSprites(ExtendedBlockView extendedBlockView, BlockPos blockPos, FluidState fluidState)
                {
                    return new Sprite[] {stillAcidSprite, dynamicAcidSprite};
                }
 
                // apply light green color
                @Override
                public int getFluidColor(ExtendedBlockView view, BlockPos pos, FluidState state)
                {
                    return 0x4cc248;
                }
            };
 
            // registering the same renderer for both fluid variants is intentional
 
            FluidRenderHandlerRegistry.INSTANCE.register(Mod.stillAcid, acidRenderHandler);
            FluidRenderHandlerRegistry.INSTANCE.register(Mod.flowingAcid, acidRenderHandler);
        });
```
然后剩下要做的就是创建必要的Json文件和纹理，但是您现在应该知道该怎么做。
## 世界
要在世界上产生酸湖，可以使用在ModInitializer中创建的net.minecraft.world.gen.feature.LakeFeature：
```
LakeFeature acidFeature = Registry.register(Registry.FEATURE, new Identifier(MODID,"acid_lake"), new LakeFeature(dynamic -> new LakeFeatureConfig(acid.getDefaultState())));
```
然后将其放入所需的生物群系中以生成：
```
 // 我告诉它像水湖一样生成，稀有度为40（数字越高，生成机会越少）
        Biomes.FOREST.addFeature(GenerationStep.Feature.LOCAL_MODIFICATIONS, Biome.configureFeature(acidFeature, new LakeFeatureConfig(acid.getDefaultState()), Decorator.WATER_LAKE, new LakeDecoratorConfig(40)));
```

本教程到此结束。