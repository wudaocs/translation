# [为Android开发者提供的Flutter文档](https://flutter.dev/docs/get-started/flutter-for/android-devs)

本文档适用于期望使用现有Android相关知识的Android开发者使用flutter构建移动应用程序。如果您了解Android框架的基础知识，那么您可以将此文档用作Flutter开发的快速入门。

使用Flutter构建时，您的Android知识和技能组合非常有价值，因为Flutter依赖移动操作系统来实现众多功能和配置。Flutter是一种为移动设备构建UI的新方法，但它有一个插件系统，可以与Android（和iOS）进行非UI任务的通信。如果您是Android的专家，则无需重新学习使用Flutter的所有内容。

本文档与食谱相似，通过翻看和查询可以找到与您的需求最相关的问题。

## [视图](https://flutter.dev/docs/get-started/flutter-for/android-devs#views)
### 在Flutter中什么相当与一个`View`(视图)？
在Android中，`View`(视图)是屏幕上显示的所有内容的基础。按钮，工具栏和输入，一切都是视图。在Flutter中，与View粗略等价的就是`widget`(窗口小组件)。`Widget`s并不完全映射到Android视图，但是当您熟悉Flutter的工作方式时，您可以将它们视为“==声明和构建UI的方式==”。

但是，这些与View有一些区别。首先，widgets具有不同的生命周期：它们是不可变的，只有在需要改变之前才存在。每当widgets或其state发生变化时，Flutter的框架都会创建一个新的widget树。相比之下，Android视图被绘制一次，并且在调用invalidate之前不会重绘。


Flutter 的组件很轻量级，部分原因就是因为他们的不可变性。因为他们本身不是视图，也没有直接绘制任何东西，而是对UI及其语义的描述，这些描述语义被`加载`到引擎之下的实际视图对象中。

Flutter 包含[Material Components](https://material.io/develop/flutter)库，这些是实现 [Material Design guidelines](https://material.io/design) （材料设计准则）的小部件。[Material Design`材料设计`]([https://baike.baidu.com/item/Material%20Design](https://baike.baidu.com/item/Material%20Design) ) 是一个灵活的设计系统，适用于所有平台，包括iOS。

但Flutter具有足够的灵活性和表现力，可以实现任何设计语言。例如，在iOS上，您可以使用[Cupertino小部件](https://flutter.dev/docs/development/ui/widgets/cupertino) 来生成看起来像[Apple的iOS设计语言](https://developer.apple.com/design/resources/)的界面 。

### 我们应该如何更新`Widget`s?
在Android中，您可以通过直接更改视图来更新视图。但是，在Flutter中，`Widget`s是不可变的并且不会直接更新，而是必须使用窗口小部件的状态。

这就是`Stateful` 和 `Stateless` 小部件的概念来源。A `StatelessWidget`听起来就像是一个没有状态信息的小部件。

当您描述的用户界面部分不依赖于对象中的配置信息以外的任何内容时，`StatelessWidgets`非常有用。

例如，在Android中，这类似于放置一个`ImageView`作为你的logo。这个logo在运行时不会进行改变，所以此时在Flutter中我们需要使用`StatelessWidget`。

如果你想基于进行的HTTP呼叫或用户交互后收到的数据动态更改UI，那么你必须使用`StatefulWidget`并且告诉Flutter框架该窗口小部件`State` 已更新，以便它可以更新该窗口小部件。

这里需要注意的重要一点是，无状态和有状态小部件的行为都是相同的。它们重建每一帧，不同之处在于它 `StatefulWidget`有一个`State`对象，它跨帧存储状态数据并恢复它。

如果您有疑问，那么请始终记住此规则：如果窗口小部件发生更改（例如，由于用户交互），则它是有状态的。但是，如果窗口小部件对更改作出反应，而包含的父窗口小部件本身不会对更改作出反应，则它仍然可以是无状态的。

以下示例显示如何使用a `StatelessWidget`。一个常见的 `StatelessWidget`是`Text`小部件。如果你看一下`Text`小部件的实现，你会发现它是子类`StatelessWidget`。

```java
Text(
  'I like Flutter!',
  style: TextStyle(fontWeight: FontWeight.bold),
);
```
正如您所看到的，`Text`Widget没有与之关联的状态信息，它呈现了在其构造函数中传递的内容，仅此而已。
但是，如果你想让“I Like Flutter”动态变化，例如点击一个`FloatingActionButton`？

要实现此目的，请将`Text`Widget包装在一个`StatefulWidget`并在用户单击按钮时更新它。
例如：

```java
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  // 此小部件是应用程序的根。
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // 默认占位符文本
  String textToShow = "I Like Flutter";

  void _updateText() {
    setState(() {
      // 更新文本
      textToShow = "Flutter is Awesome!";
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(child: Text(textToShow)),
      floatingActionButton: FloatingActionButton(
        onPressed: _updateText,
        tooltip: 'Update Text',
        child: Icon(Icons.update),
      ),
    );
  }
}
```

### 我们如何布置我的小部件？我们的XML布局文件在哪里？
在Android中，您使用`XML`编写布局，但在Flutter中，您可以使用窗口小部件树`(widget tree)`编写布局。

以下示例显示如何显示一个简单的带有内间距`（padding）`的小部件:

```java
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      title: Text("Sample App"),
    ),
    body: Center(
      child: MaterialButton(
        onPressed: () {},
        child: Text('Hello'),
        // 设置左右内间距
        padding: EdgeInsets.only(left: 10.0, right: 10.0),
      ),
    ),
  );
}
```

您可以在[窗口小部件目录中](https://flutter.dev/docs/development/ui/widgets/layout)查看Flutter必须提供的布局。
### 如何在布局中添加或删除组件？
在Android中，您可以调用父项`addChild()`或`removeChild()`在父项上动态添加或删除子视图。在Flutter中，因为小部件是不可变的，所以没有直接的等价物`addChild()`。相反，您可以将函数传递给返回窗口小部件的父级，并使用布尔标志控制该子级的创建。

例如，以下是单击以下内容时如何在两个小部件之间切换 `FloatingActionButton`：

```java
import 'package:flutter/material.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: SampleAppPage(),
    );
  }
}

class SampleAppPage extends StatefulWidget {
  SampleAppPage({Key key}) : super(key: key);

  @override
  _SampleAppPageState createState() => _SampleAppPageState();
}

class _SampleAppPageState extends State<SampleAppPage> {
  // 切换的默认值
  bool toggle = true;
  void _toggle() {
    setState(() {
      toggle = !toggle;
    });
  }

  // 获取
  _getToggleChild() {
    if (toggle) {
      return Text('Toggle One');
    } else {
      return MaterialButton(onPressed: () {}, child: Text('Toggle Two'));
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Sample App"),
      ),
      body: Center(
        child: _getToggleChild(),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _toggle,
        tooltip: 'Update Text',
        child: Icon(Icons.update),
      ),
    );
  }
}
```

### 如何为小部件设置动画？

在Android中，您可以使用XML创建动画，也可以`animate()` 在视图上调用方法。在Flutter中，动画小部件通过将小部件包装在动画部件内并使用动画库实现。

在Flutter中，使用`AnimationController`，它是一个`Animation<double>`，可以暂停，搜索，停止和反转动画。它需要一个`Ticker`，当vsync发生时发出信号，并在每个帧运行时产生0到1之间的线性插值。然后创建一个或多个 `Animation`s并将它们附加到控制器。

例如，您可以使用`CurvedAnimation`沿插值曲线实现动画。从这个意义上说，控制器是动画进度的“主”源，并且`CurvedAnimation`计算取代控制器默认线性运动的曲线。像小部件一样，Flutter中的动画可组合使用。

构建窗口小部件树时，将动画分配给窗口小部件的动画属性，比如不透明度`FadeTransition`,然后告诉控制器启动动画。

以下示例显示了如何编写一个`FadeTransition`动画，当你按下`FloatingActionButton`时，淡化小部件成一个logo：

```java
import 'package:flutter/material.dart';

void main() {
  runApp(FadeAppTest());
}

class FadeAppTest extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Fade Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyFadeTest(title: 'Fade Demo'),
    );
  }
}

class MyFadeTest extends StatefulWidget {
  MyFadeTest({Key key, this.title}) : super(key: key);
  final String title;
  @override
  _MyFadeTest createState() => _MyFadeTest();
}

class _MyFadeTest extends State<MyFadeTest> with TickerProviderStateMixin {
  AnimationController controller;
  CurvedAnimation curve;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(duration: const Duration(milliseconds: 2000), vsync: this);
    curve = CurvedAnimation(parent: controller, curve: Curves.easeIn);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
          child: Container(
              child: FadeTransition(
                  opacity: curve,
                  child: FlutterLogo(
                    size: 100.0,
                  )))),
      floatingActionButton: FloatingActionButton(
        tooltip: 'Fade',
        child: Icon(Icons.brush),
        onPressed: () {
          controller.forward();
        },
      ),
    );
  }
}
```





