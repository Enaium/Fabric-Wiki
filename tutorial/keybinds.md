# 自定义键绑定
## 键盘:直接来自键盘

Minecraft使用键绑定来处理来自外围设备（例如键盘和鼠标）的用户输入.按W时,角色向前移动,当您按E时,您的角色将打开.每个键绑定也可以使用设置菜单进行配置,因此如果您愿意,可以使用箭头键而不是WASD来移动播放器.

添加键绑定很容易.你需要:
* 创建一个FabricKeyBinding对象
* 注册你的钥匙
* 对按下的键做出反应

## 创建您的Keybind
Fabric API有一个FabricKeyBinding对象,可以更容易地创建自己的KeyBinding.在您偏好的区域中声明其中一个:

`private  static FabricKeyBinding keyBinding;`

FabricKeyBinding有一个Builder用于初始化.它接收Identifier,InputUtil.Type,键代码和绑定类别:
```
keyBinding = FabricKeyBinding.Builder.create(
    new Identifier("wiki-keybinds", "spook"),
    InputUtil.Type.KEY_KEYBOARD,
    GLFW.GLFW_KEY_R,
    "Wiki Keybinds"
).build();
```
GLFW.GLFW_KEY_R可以替换为您希望绑定到默认值的任何键.该类别与键绑定在设置页面中的分组方式有关.

## 注册您的Keybind
要注册键绑定,请使用KeybindingRegistry:

`KeyBindingRegistry.INSTANCE.register(keyBinding);`

如果您现在登录游戏,您将在设置页面中看到您的键绑定.

## 回应你的Keybind
不幸的是,没有明确的方法来回应键绑定.大多数人会同意最好的方法是挂钩客户端tick事件:
```
ClientTickCallback.EVENT.register(e ->
{
    if(keyBinding.isPressed()) System.out.println("was pressed!");
});
```
请注意,这完全是客户端的.要让服务器响应keybind,您需要发送自定义数据包并让服务器单独处理它.