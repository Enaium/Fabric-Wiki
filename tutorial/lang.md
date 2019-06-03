# 名字翻译
注意你的项目有一个奇怪的显示名称，例如item.modid.my_item？这是因为您的项目名称没有游戏所选语言的翻译。翻译用于支持单个字符串的多种不同语言。

## 创建一个lang文件

您可以使用lang文件为游戏中的可翻译字符串提供翻译。您需要为您的语言创建一个具有适当文件名的文件 - 以查找您的语言代码，请访问此链接。英语是en_us。获得语言代码后，在resources / assets / modid / lang /创建一个JSON文件; 英语翻译文件的完整示例是resources / assets / wikitut / lang / en_us.json。

## 添加翻译
创建lang文件后，您可以使用此基本模板添加翻译：

`resources/assets/wikitut/lang/en_us.json`

```
{
  "item.modid.my_item": "My Item",
  "item.modid.my_awesome.item": "My Awesome Item",
  [...]
}
```

`resources/assets/wikitut/lang/zh_cn.json`

```
{
  "item.modid.my_item": "我的项目",
  "item.modid.my_awesome.item": "我的项目真棒",
  [...]
}
```





其中第一个字符串是任何可翻译的字符串（例如项目名称或TranslatableTextComponent）。如果您正在wiki教程中关注，请记住将modid更改为wikitut，或者您选择的任何modid。

---
说到Awesome 就想到某人的开箱.....