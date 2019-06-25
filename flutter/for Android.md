# [（译）为Android开发者提供的Flutter文档：](https://flutter.dev/docs/get-started/flutter-for/android-devs)

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
有关更多信息，请参阅 [动画和动作小部件](https://flutter.dev/docs/development/ui/widgets/animation)，[动画教程](https://flutter.dev/docs/development/ui/animations/tutorial)和[动画概述](https://flutter.dev/docs/development/ui/animations)。

### 我如何使用`Canvas`绘画/绘画？
在Android中，您可以使用`Canvas`和`Drawable`s在屏幕上绘制图像和形状。Flutter也有类似的`Canvas`API，因为它基于相同的低级渲染引擎Skia。因此，对于Android开发人员来说，在Flutter中绘制到画布是一项非常熟悉的任务。

Flutter有两个类可以帮助你绘制到画布：`CustomPaint` 并且`CustomPainter`，后者实现了绘制到画布的算法。

要学习如何在Flutter中实现`签名画家`，请参阅Collin在[StackOverflow](https://stackoverflow.com/questions/46241071/create-signature-area-for-mobile-app-in-dart-flutter)上的答案 。

```java
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(home: DemoApp()));

class DemoApp extends StatelessWidget {
  Widget build(BuildContext context) => Scaffold(body: Signature());
}

class Signature extends StatefulWidget {
  SignatureState createState() => SignatureState();
}

class SignatureState extends State<Signature> {
  List<Offset> _points = <Offset>[];
  Widget build(BuildContext context) {
    return GestureDetector(
      onPanUpdate: (DragUpdateDetails details) {
        setState(() {
          RenderBox referenceBox = context.findRenderObject();
          Offset localPosition =
          referenceBox.globalToLocal(details.globalPosition);
          _points = List.from(_points)..add(localPosition);
        });
      },
      onPanEnd: (DragEndDetails details) => _points.add(null),
      child: CustomPaint(painter: SignaturePainter(_points), size: Size.infinite),
    );
  }
}

class SignaturePainter extends CustomPainter {
  SignaturePainter(this.points);
  final List<Offset> points;
  void paint(Canvas canvas, Size size) {
    var paint = Paint()
      ..color = Colors.black
      ..strokeCap = StrokeCap.round
      ..strokeWidth = 5.0;
    for (int i = 0; i < points.length - 1; i++) {
      if (points[i] != null && points[i + 1] != null)
        canvas.drawLine(points[i], points[i + 1], paint);
    }
  }
  bool shouldRepaint(SignaturePainter other) => other.points != points;
}
```

### 如何构建自定义小部件？
在Android中，您通常将View子类化，或使用预先存在的视图来覆盖和实现实现所需行为的方法。在Flutter中，通过[组合](https://flutter.dev/docs/resources/technical-overview#everythings-a-widget)较小的小部件（而不是扩展它们）来构建自定义小部件。它有点类似于在Android中实现自定义ViewGroup，其中所有构建块都已存在，但您提供了不同的行为 - 例如，自定义布局逻辑。

例如，你如何构建一个在构造函数中带有标签的`CustomButton`?创建一个CustomButton，用于组成带标签的`RaisedButton`而不是通过扩展`RaisedButton`:

```java
class CustomButton extends StatelessWidget {
  final String label;

  CustomButton(this.label);

  @override
  Widget build(BuildContext context) {
    return RaisedButton(onPressed: () {}, child: Text(label));
  }
}
```
然后使用`CustomButton`，就像使用任何其他Flutter小部件一样：

```java
@override
Widget build(BuildContext context) {
  return Center(
    child: CustomButton("Hello"),
  );
}
```

## [意图](https://flutter.dev/docs/get-started/flutter-for/android-devs#what-is-the-equivalent-of-an-intent-in-flutter)
## Flutter 中什么等价于Intent？
在Android中，`Intent`s 有两个主要用例：在Activities之间导航，以及与组件通信。另一方面，另一方面，Flutter没有意图的概念，尽管你仍然可以通过原生集成（使用[插件](https://pub.dev/packages/android_intent)）启动意图。

Flutter 实际上没有直接相当于activities 和 fragments，相反，在Flutter中，您可以使用导航器和路径在屏幕之间导航，所有这些都在同一个`Activity`中。

`Route(路由)` 是应用程序的“屏幕”或“页面”的抽象化，`Navigator（导航）`是管理路径的小部件。一个路径粗略映射到一个`Activity`, 但它没有等同的含义。导航器可以推送和弹出路径用以在屏幕之间移动。导航器的工作方式类似于堆栈，您可以``push()`您想要导航到的新路线，并且当您想要“返回”时可以从中 `pop()`路线。

在Android中，您在应用程序内声明您的activities `AndroidManifest.xml`。

在Flutter中，您可以在页面之间导航：
* 指定`Map`路径名称。（MaterialApp）
* 直接导航到路线。（WidgetApp）

以下示例构建一个Map。

```java
void main() {
  runApp(MaterialApp(
    home: MyAppHome(), // 用 '/' 变成路由名称
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => MyPage(title: 'page A'),
      '/b': (BuildContext context) => MyPage(title: 'page B'),
      '/c': (BuildContext context) => MyPage(title: 'page C'),
    },
  ));
}
```

通过`push`（推送）一个路由名称到`Navigator`导航器导航一个路径。

```java
Navigator.of(context).pushNamed('/b');
```

`Intents`的另一个关键用例是调用外部组件，如`Camera`或`File picker`。因此，您需要创一个混合原生平台（或使用[现有插件](https://pub.dev/flutter/)）

要了解如何构建混合原生平台，请参阅 [开发包和插件](https://flutter.dev/docs/development/packages-and-plugins/developing-packages)。

### 如何在Flutter中处理来自外部应用程序的传入意图？
通过直接与Android层交互并请求共享的数据，Flutter可以处理来自Android的传入意图。

以下示例在运行我们的Flutter代码的原生页面上注册文本共享意图过滤器，所以其他应用可以与我们的Flutter应用分享文字。

基本流程意味着我们首先处理Android原生端的共享文本数据（在我们的`Activity`中），然后等到Flutter请求数据使用`MethodChannel`提供它。

首先，在`AndroidManifest.xml`中注册所有意图的intent过滤器：

```java
<activity
  android:name=".MainActivity"
  android:launchMode="singleTop"
  android:theme="@style/LaunchTheme"
  android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection"
  android:hardwareAccelerated="true"
  android:windowSoftInputMode="adjustResize">
  <!-- ... -->
  <intent-filter>
    <action android:name="android.intent.action.SEND" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="text/plain" />
  </intent-filter>
</activity>
```

然后在MainActivity中处理intent，提取从intent共享的文本，并保持它。当Flutter准备好处理时，它会使用平台通道请求数据，并从原生端发送：

```java
package com.example.shared;

import android.content.Intent;
import android.os.Bundle;

import java.nio.ByteBuffer;

import io.flutter.app.FlutterActivity;
import io.flutter.plugin.common.ActivityLifecycleListener;
import io.flutter.plugin.common.MethodCall;
import io.flutter.plugin.common.MethodChannel;
import io.flutter.plugin.common.MethodChannel.MethodCallHandler;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class MainActivity extends FlutterActivity {

  private String sharedText;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    GeneratedPluginRegistrant.registerWith(this);
    Intent intent = getIntent();
    String action = intent.getAction();
    String type = intent.getType();

    if (Intent.ACTION_SEND.equals(action) && type != null) {
      if ("text/plain".equals(type)) {
        handleSendText(intent); //处理正在发送的文本
      }
    }

    new MethodChannel(getFlutterView(), "app.channel.shared.data").setMethodCallHandler(
      new MethodCallHandler() {
        @Override
        public void onMethodCall(MethodCall call, MethodChannel.Result result) {
          if (call.method.contentEquals("getSharedText")) {
            result.success(sharedText);
            sharedText = null;
          }
        }
      });
  }

  void handleSendText(Intent intent) {
    sharedText = intent.getStringExtra(Intent.EXTRA_TEXT);
  }
}
```

之后，在渲染窗口小部件时请求Flutter端的数据：

```java
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(SampleApp());
}

class SampleApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sample Shared App Handler',
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
  static const platform = const MethodChannel('app.channel.shared.data');
  String dataShared = "No data";

  @override
  void initState() {
    super.initState();
    getSharedText();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(body: Center(child: Text(dataShared)));
  }

  getSharedText() async {
    var sharedData = await platform.invokeMethod("getSharedText");
    if (sharedData != null) {
      setState(() {
        dataShared = sharedData;
      });
    }
  }
}
```

### Flutter中什么相当与`startActivityForResult()`?
`Navigator`类处理Flutter中的路由并用于从您在堆栈上推送的路径返回结果。这是通过`push()` 后等待未来（`Future`）的返回来完成的。

例如，要启动允许用户选择其位置的位置路线，您可以执行以下操作：
```java
Map coordinates = await Navigator.of(context).pushNamed('/location');
```

然后，在您的位置路线内，一旦用户选择了他们的位置，您就可以`pop(弹出)`结果堆信息：
```java
Navigator.of(context).pop({"lat":43.821757,"long":-79.226392});
```








