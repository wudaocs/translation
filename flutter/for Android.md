# 为Android开发者提供的Flutter文档

本文档适用于期望使用现有Android相关知识的Android开发者使用flutter构建移动应用程序。如果您了解Android框架的基础知识，那么您可以将此文档用作Flutter开发的快速入门。

使用Flutter构建时，您的Android知识和技能组合非常有价值，因为Flutter依赖移动操作系统来实现众多功能和配置。Flutter是一种为移动设备构建UI的新方法，但它有一个插件系统，可以与Android（和iOS）进行非UI任务的通信。如果您是Android的专家，则无需重新学习使用Flutter的所有内容。

本文档与食谱相似，通过翻看和查询可以找到与您的需求最相关的问题。

## 视图
### 在Flutter中什么相当与一个`View`(视图)？
在Android中，`View`(视图)是屏幕上显示的所有内容的基础。按钮，工具栏和输入，一切都是视图。在Flutter中，与View粗略等价的就是`widget`(窗口小组件)。`Widgets`并不完全映射到Android视图，但是当您熟悉Flutter的工作方式时，您可以将它们视为“==声明和构建UI的方式==”。

但是，这些与View有一些区别。首先，widgets具有不同的生命周期：它们是不可变的，只有在需要改变之前才存在。每当widgets或其state发生变化时，Flutter的框架都会创建一个新的widget树。相比之下，Android视图被绘制一次，并且在调用invalidate之前不会重绘。


Flutter 的组件很轻量级，部分原因就是因为他们的不可变性。因为他们本身不是视图，也没有直接绘制任何东西，而是对UI及其语义的描述，这些描述语义被`加载`到引擎之下的实际视图对象中。

Flutter 包含[Material Components](https://material.io/develop/flutter)库，这些是实现 [Material Design guidelines](https://material.io/design) （材料设计准则）的小部件。[Material Design`材料设计`]([https://baike.baidu.com/item/Material%20Design](https://baike.baidu.com/item/Material%20Design) ) 是一个灵活的设计系统，适用于所有平台，包括iOS。

但Flutter具有足够的灵活性和表现力，可以实现任何设计语言。例如，在iOS上，您可以使用[Cupertino小部件](https://flutter.dev/docs/development/ui/widgets/cupertino) 来生成看起来像[Apple的iOS设计语言](https://developer.apple.com/design/resources/)的界面 。



