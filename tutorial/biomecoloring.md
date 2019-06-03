# 方块群戏颜色

在本教程中，我们将介绍为新块添加依赖于生物群落的颜色。首先，您需要一个具有占tintindex的模型的块。要查看此示例，请查看基础叶或grass_block模型文件。

请记住保持与客户端视觉相关的逻辑（onInitializeClient），否则它将在服务器上崩溃。要注册自定义块着色，请使用ColorProviderRegistry.BLOCK.register，对于项目，请使用ColorProviderRegistry.ITEM.register。在本教程中，将使用草生物群落颜色。通过传入块替换最终参数。
```
public class ExampleModClient implements ClientModInitializer {
    @Override
    public void onInitializeClient() {
        ColorProviderRegistry.BLOCK.register((block, pos, world, layer) -> {
            BlockColorProvider provider = ColorProviderRegistry.BLOCK.get(Blocks.GRASS);
            return provider == null ? -1 : provider.getColor(block, pos, world, layer);
        }, YOUR_BLOCK_INSTANCE);
    }
}
```

那么，这里发生了什么？寄存器方法需要返回一种颜色，在这种情况下，该颜色是从草块中获取的。着色项目非常相似。与块一样，返回的颜色可以是任意颜色，并且还记得用项目实例替换最终参数。
```
public class ExampleModClient implements ClientModInitializer {
    @Override
    public void onInitializeClient() {
        ColorProviderRegistry.ITEM.register((item, layer) -> {
            // These values are represented as temperature and humidity, and used as coordinates for the color map
            double temperature = 0.5D; // a double value between 0 and 1
            double humidity = 1.0D; // a double value between 0 and 1
            return GrassColorHandler.getColor(temperature, humidity);
        }, YOUR_ITEM_INSTANCE);
    }
}
```
完了！