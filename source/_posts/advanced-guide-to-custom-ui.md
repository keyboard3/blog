---
title: 自定义UI的高级指南
top: false
cover: false
toc: true
mathjax: true
date: 2022-05-09 15:03:23
password:
summary:
tags: [tModLoader, terraria, 翻译,c#]
categories:
---
[Advanced guide to custom UI](https://github.com/tModLoader/tModLoader/wiki/Advanced-guide-to-custom-UI)

在本指南中，您将了解 vanilla 中存在的各种 UI 类，您可以使用它们来修改自己的 UI。在遵循本指南之前，您应该了解以下主题：
- 类层次结构和继承
- Abstract / virtual and override
- 面向对象编程（与类继承有关）

# 交待
Terraria 中的 UI 由各种类组成，最值得注意的是：UIElement、UIState 和 UserInterface。我们将讨论每个类及其在创建 UI 过程中的重要性。

# 对象关系
自定义 UI 只不过是一个自定义 UserInterface，它的状态设置为自定义 UIState。一般来说，一个 UserInterface 可以有一个活动的 UIState（但是它可以保存状态历史，直到上限，您可以返回到该状态），然后该状态当前显示在该 UserInterface 中。

# UserInterface
没有理由创建派生自 UserInterface 的自定义类。 （虽然你可以，因为类不是密封的）在大多数情况下，只需在你的 mod 类（或专用的 UI 类）中声明一个 UserInterface 类型的新字段。我们稍后再讨论这个问题。

# UIState
UIState 代表界面所处的状态。类本身非常简单：它实际上是一个跨越整个屏幕宽度和高度的 UIElement，允许您在屏幕上的任何位置添加元素。

# UIElement
UIElement 表示一个类，它是可以成为接口一部分的任何类型的 UIElement。普通示例是 UIPanel、UIImage 和 UIImageButton 类。每个都有特定的功能。 （分别显示背景面板、显示图像或显示可点击按钮图像）您可以通过从 UIElement 派生类来制作自己的自定义元素。我们将回到这一点。

# 初始设置
所以，你需要一个 UserInterface 和 UIState。首先在您的 Mod 类中定义这些新字段：
```csharp
internal UserInterface MyInterface;
```
对于您的 UIState，您应该创建一个新的自定义类：
```csharp
class TheUI : UIState { }
```
然后在你的 Mod 类中为它创建一个字段：
```csharp
internal TheUI MyUI;
```

# 初始化用户界面
因为 UI 是只有玩家才能看到的东西，所以你不应该在服务器上初始化 UI。这会浪费资源，服务器不玩游戏也看不到任何图形。仅在客户端上初始化。在您的 Mod.Load() 中，初始化您的 UI：
```csharp
if (!Main.dedServ) {
	MyInterface = new UserInterface();
    
	MyUI = new TheUI();
	MyUI.Activate(); // 如果未初始化，Activate 在 UIState 上调用 Initialize()，然后调用 OnActivate，然后在每个子元素上调用 Activate
}
```
在您的 Mod.Unload() 中，您可以在 UI 上调用您可能需要的任何卸载操作，然后将其设置为 null：
```csharp
MyUI?.SomeKindOfUnload(); // 如果你持有需要卸载的数据，在OO-fashion中调用它
MyUI = null;
```
不建议在 UI 上卸载如上所示，因为通常让 UI 负责数据是不好的做法。但是，如果您的 UI 包含诸如纹理之类的静态引用，这将很有用。

# 更新和绘制 UI
设置界面的状态很好，但 UI 不会被神奇地调用来更新或绘制自身。为此，您必须覆盖 Mod.UpdateUI(Gametime gameTime) 和 Mod.ModifyInterfaceLayers(List<GameInterfaceLayer> 层)：
```csharp
private GameTime _lastUpdateUiGameTime;

public override void UpdateUI(GameTime gameTime) {
  _lastUpdateUiGameTime = gameTime;
  if (MyInterface?.CurrentState != null) {
  	MyInterface.Update(gameTime);
  }
}
```
上面的代码片段将在您的界面上调用 .Update 并将其传播到其状态和底层元素。
```csharp
public override void ModifyInterfaceLayers(List<GameInterfaceLayer> layers) {
  int mouseTextIndex = layers.FindIndex(layer => layer.Name.Equals("Vanilla: Mouse Text"));
  if (mouseTextIndex != -1) {
    layers.Insert(mouseTextIndex, new LegacyGameInterfaceLayer(
    	"MyMod: MyInterface",
    	delegate
   	 	{
    		if ( _lastUpdateUiGameTime != null && MyInterface?.CurrentState != null) {
    			MyInterface.Draw(Main.spriteBatch, _lastUpdateUiGameTime);
    		}
   			return true;
    	},
   		InterfaceScaleType.UI));
  }
}
```
上面的代码片段将一个自定义层添加到普通层列表中，如果它有状态，它将在你的界面上调用 .Draw。这将使您的 UI 真正绘制并显示在屏幕上。将 InterfaceScaleType 设置为 UI 以进行适当的 UI 缩放。

# 显示 UI
出于测试目的，建议添加热键或其他易于访问的方式来切换 UI。要显示您的 UI，您需要将界面的状态设置为您的 UI 实例。你应该通过访问你的 mod 的实例并调用 .SetState 来做到这一点：
```csharp
MyMod.Instance.MyInterface.SetState(MyMod.Instance.MyUI)
```
此代码段将为您的 UI 设置状态，使其显示。如果要隐藏 UI，只需将 null 传递给方法调用。

您可以看到为什么为您的 UI 使用专用类很有用。为此制作辅助方法很有用，例如：
```csharp
internal void ShowMyUI() {
	MyInterface?.SetState(MyUI);
}

internal void HideMyUI() {
	MyInterface?.SetState(null);
}
```
然后简单地调用它们：
```csharp
MyMod.Instance.ShowMyUI();
MyMod.Instance.HideMyUI();
```

## State history
该接口跟踪状态历史。如果你想回到之前的状态，你可以调用 MyInterface.GoBack()，如果有的话，它会激活历史中之前的状态。请记住，它将从历史记录中删除激活状态，因此您需要调用 .SetState 或 .AddToHistory 将其恢复。历史记录最多可保存 32 个状态，如果您在历史记录中添加新状态（如果已有 32 个状态），则会删除最旧的 4 个状态。如果您的界面状态不断变化，例如如果是渐进式 UI（例如分页或选项卡），则状态历史记录很有用

# 添加元素
如果您现在激活您的 UI，您可能会感到困惑，因为没有任何显示。这是因为您的 UI 是空的。你可以向 UIState 添加任何你想要的元素，然后它们就会显示出来。

在您的 MyUI 类中，重写 OnInitialize 方法。例如添加一个新的 UIPanel 到状态：
```csharp
public override void OnInitialize() { // 1
  UIPanel panel = new UIPanel(); // 2
  panel.Width.Set(300, 0); // 3
  panel.Height.Set(300, 0); // 3
  Append(panel); // 4
}
```

- 重写 OnInitialize 方法。当我们在加载我们的模组时初始化 UI 时调用它。
- 创建一个新的 UIPanel 实例。这是一个香草类，将在此元素上绘制香草风格的背景。
- 将宽度和高度设置为更大的值，以便我们可以看到它。
- 将面板附加到我们的 UIState。 Append 是 UIElement 类的一个方法，它允许您向该元素添加子元素。子元素相对于该元素放置。由于 UIState 覆盖了整个屏幕，我们的 UIPanel 将显示在屏幕的左上角。

## 添加一些文本
让我们通过向我们的 UIPanel 添加一些文本来增加趣味性。我们可以用同样的方式做到这一点，但是这次将元素附加到我们的面板变量中：
```csharp
public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  Append(panel);

  UIText text = new UIText("Hello world!"); // 1
  panel.Append(text); // 2
}
```

- 初始化 UIText
- UIText 元素现在是 UIPanel 元素的子元素。文本应显示在 UIPanel 中。

## 中心元素
您的大部分时间将花在完善 UI 中元素的位置上。您将弄乱 UIElement 类上的各种字段：
```csharp
public StyleDimension Top;
public StyleDimension Left;
public StyleDimension Width;
public StyleDimension Height;
public StyleDimension MaxWidth = StyleDimension.Fill;
public StyleDimension MaxHeight = StyleDimension.Fill;
public StyleDimension MinWidth = StyleDimension.Empty;
public StyleDimension MinHeight = StyleDimension.Empty;
public bool OverflowHidden;
public float PaddingTop;
public float PaddingLeft;
public float PaddingRight;
public float PaddingBottom;
public float MarginTop;
public float MarginLeft;
public float MarginRight;
public float MarginBottom;
public float HAlign;
public float VAlign;
```
使元素居中是修改器的常见用例。相对于其父元素居中元素很容易。为了使 UIPanel 中的文本居中，我们可以使用 HAlign 和 VAlign，并将两者都设置为 0.5f：
```csharp
public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  Append(panel);

  UIText text = new UIText("Hello world!");
  text.HAlign = 0.5f; // 1
  text.VAlign = 0.5f; // 1
  panel.Append(text);
}
```
这基本上是将我们的水平和垂直对齐设置为 50%，使我们的元素居中。

### 类似标题的文本
如果需要，我们可以使用这些对齐技巧来像标题一样完美地对齐文本。我们可以将 HAlign 设置为 50% 以使文本水平居中，我们可以通过设置 Top 位置将 VAlign 设置为较低的值或固定的绝对值。推荐后者：
```csharp
public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  Append(panel);

  UIText header = new UIText("My UI Header");
  header.HAlign = 0.5f; // 1
  header.Top.Set(15, 0); // 2
  panel.Append(header);
}
```

- 1. 将水平对齐设置为 50%
- 2. 将顶部位置设置为 15 像素。文本应该相对于 UIPanel 向下 15 像素。请记住，您可以为此使用 VAlign，但它会随着您的 UIPanel 更改大小而缩放。当您更改 UIPanel 的大小时，这可能会搞砸事情。

### 居中 UIPanel
您的 UI 可能仍显示在 UIState 的左上角。大多数模组制作者都希望他们的 UI 显示在屏幕中央。因为 UIState 跨越整个屏幕大小，我们可以使用 HAlign 和 VAlign 技巧来使我们的 UIPanel 居中：
```csharp
public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  panel.HAlign = panel.VAlign = 0.5f; // 1
  Append(panel);
}
```
- 这是将两个字段设置为相同值的巧妙技巧。

但是，如果您更改 UIState 跨越屏幕的方式，或者如果您的 UIPanel 未附加到 UIState 本身，则此技巧将不起作用。当心你的 UIPanel 的父级并相应地对齐它。

# 与 UIElement 的交互
模组制作者想要的最常见的交互是在单击某个项目时执行某些操作，例如按钮。让我们通过交互向 UI 添加一个按钮：
```csharp
public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  Append(panel);

  UIText header = new UIText("My UI Header");
  header.HAlign = 0.5f;
  header.Top.Set(15, 0);
  panel.Append(header);

  UIPanel button = new UIPanel(); // 1
  button.Width.Set(100, 0);  
  button.Height.Set(50, 0);
  button.HAlign = 0.5f;
  button.Top.Set(25, 0); // 2
  button.OnClick += OnButtonClick; // 3
  panel.Append(button);
  
  UIText text = new UIText("Click me!");
  text.HAlign = text.VAlign = 0.5f; // 4
  button.Append(text); // 5
}

private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
  // We can do stuff in here!
}
```

- 1. 初始化一个新的 UIPanel。因为没有 UIButton 类（只有 UIImageButton），我们将为此使用 UIPanel 和 UIText。
- 2. 将顶部位置设置为略低于我们的标题。
- 3. 添加新的 OnClick 事件。重要的是要注意事件将通过父子链传播。这意味着如果我们点击 UIText，点击事件也将在我们的 UIPanel（按钮）中结束，因为它是父级。出于这个原因，我们只需要将处理程序添加到 UIPanel。
- 4. 设置文本对齐方式，使其在我们的按钮面板中居中。
- 5. 将文本附加到我们的按钮。该按钮被附加到我们的背景面板。

OnButtonClick 目前什么都不做。让我们改变我们的文本。为了做到这一点，我们必须使我们的按钮文本成为类级别的字段，而不是仅限于 OnInitialize 方法：
```csharp
private UIText text; // Init later

public override void OnInitialize() {
	// ... code
    text = new UIText("Click me!");
	// ... other code
}
```
现在我们可以在 OnButtonClick 方法中访问文本：
```csharp
private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
	text.SetText("I was clicked!");
}
```

### 事件
有很多像 OnClick 这样的事件，这里是它们的列表：
```csharp
public event UIElement.MouseEvent OnMouseDown;
public event UIElement.MouseEvent OnMouseUp;
public event UIElement.MouseEvent OnClick;
public event UIElement.MouseEvent OnMouseOver;
public event UIElement.MouseEvent OnMouseOut;
public event UIElement.MouseEvent OnDoubleClick;
public event UIElement.MouseEvent OnRightMouseDown;
public event UIElement.MouseEvent OnRightMouseUp;
public event UIElement.MouseEvent OnRightClick;
public event UIElement.MouseEvent OnRightDoubleClick;
public event UIElement.MouseEvent OnMiddleMouseDown;
public event UIElement.MouseEvent OnMiddleMouseUp;
public event UIElement.MouseEvent OnMiddleClick;
public event UIElement.MouseEvent OnMiddleDoubleClick;
public event UIElement.MouseEvent OnXButton1MouseDown;
public event UIElement.MouseEvent OnXButton1MouseUp;
public event UIElement.MouseEvent OnXButton1Click;
public event UIElement.MouseEvent OnXButton1DoubleClick;
public event UIElement.MouseEvent OnXButton2MouseDown;
public event UIElement.MouseEvent OnXButton2MouseUp;
public event UIElement.MouseEvent OnXButton2Click;
public event UIElement.MouseEvent OnXButton2DoubleClick;
public event UIElement.ScrollWheelEvent OnScrollWheel;
```

# 悬停工具提示(Tooltip)
另一个有用的交互修改器通常希望在悬停元素时显示工具提示。这最好通过重写 OnUpdate 方法并检查鼠标是否悬停在元素上，然后更改 Main.hoverItemName 来完成。如果我们悬停按钮，让我们显示此工具提示：（您需要将按钮设置为类级别的字段，就像我们对文本所做的那样）
```csharp
public override void Update(GameTime gameTime) {
	if (text.IsMouseHovering || button.isMouseHovering) {
    	Main.hoverItemName = "Click to see what happens";
    }
}
```

# UI 显示或隐藏时的交互
覆盖方法 .OnActivate() 和 .OnDeactivate() 以分别在您的 UI 激活或停用时执行操作。例如，激活可用于检索最新数据以填充 UI，停用可重置存储此数据的变量。使用 deactivate 使您的 UI 在下次激活时保持新鲜，因此它在不使用时在内存中保存的项目更少。 （理想情况下将事物设置为 null 以取消分配内存）

# 自定义 UIElement
制作自己的元素很容易。您必须创建一个继承 UIElement 类的自定义类。 OnInitialize、OnActivate、OnDeactivate、DrawSelf 和 Update 方法将是要重写的主要方法。

一个简单的例子是制作我们自己的自定义按钮类来方便我们在上面做的事情。让我们从基础开始：首先定义我们的类是什么以及应该做什么。按钮类必须显示一些文本并且是可点击的，点击时可以执行指定的操作。很好，现在我们知道了该类的用例，我们可以对其进行建模：
```csharp
public class UIClickableButton : UIElement {

	// 1
	private object _text;
	private UIElement.MouseEvent _clickAction;
	private UIPanel _uiPanel;
	private UIText _uiText;
	
	// 2
	public string Text 
	{
		get => _uiText?.Text ?? string.Empty; // 3
		set => _text = value;
	}

	public UIClickableButton(object text, UIElement.MouseEvent clickAction) : base() { // 4
		_text = text?.toString() ?? string.Empty;
		_clickAction = clickAction;
	}

	public override void OnInitialize() { 
		_uiPanel = new UIPanel(); // 5
		_uiPanel.Width = StyleDimension.Fill; // 5
		_uiPanel.Height = StyleDimension.Fill; // 5
		Append(_uiPanel);
		
		_uiText = new UIText(""); // 6
		_uiText.VAlign = _uiText.HAlign = 0.5f; // 6
		_uiPanel.Append(_uiText);	
		
		_uiPanel.OnClick += _clickAction; // 7
	}
	
	public override void Update(GameTime gameTime) {
		if (_text != null) { // 8
			_uiText.SetText(_text.ToString());
			_text = null;
			Recalculate(); // 9
            base.MinWidth = _uiText.MinWidth; // 9
            base.MinHeight = _uiText.MinHeight; // 9
		}
	}
}
```
有很多事情发生，让我们看看：
- 1. 我们定义自定义按钮所需的变量。
- 2. 一个公共属性，我们可以使用它来获取和设置文本支持字段。第 8 点对此进行了更多说明。
- 3. 只需返回我们的 _uiText 的文本，如果它为 null，则返回空字符串。 （尚未初始化）
- 4. 我们的构造函数。我们必须传递一个文本和点击动作。文本是对象类型，仿照 UIText 类。
- 5. 创建一个新的 UIPanel 作为基础背景。我们将大小设置为Fill，相当于调用Set(0, 1f)； 1f 代表 100%，所以在这种情况下，我们将拉伸 UIPanel 与我们制作这个元素一样大。
- 6. 创建一个新的 UIText，我们在 UIPanel 中居中对齐。
- 7. 将点击操作注册到 UIPanel。请记住，子项上的单击操作通过父子链传播，因此单击事件将最终出现在我们的 UIPanel OnClick 处理程序上。
- 8. 通过在更新期间更新 UIText 的文本，我们可以使我们的文本更改线程安全并避免在绘制文本时编辑文本时出现错误。
- 9. 重新计算强制此元素及其子元素重新计算大小、填充等。如果内容发生更改，例如本例中的文本，则应执行此操作。在这种情况下，我们可以在 Recalculate() 期间复制从 UIText 类计算的最小宽度和最小高度。

现在我们可以使用这个类，而不是我们之前所做的：
```csharp
private UIClickableButton _button;

public override void OnInitialize() {
  UIPanel panel = new UIPanel();
  panel.Width.Set(300, 0);
  panel.Height.Set(300, 0);
  Append(panel);

  UIText header = new UIText("My UI Header");
  header.HAlign = 0.5f;
  header.Top.Set(15, 0);
  panel.Append(header);

  _button = new UIClickableButton("Click me!", OnButtonClick);
  _button.Width.Set(100, 0);  
  _button.Height.Set(50, 0);
  _button.HAlign = 0.5f;
  _button.Top.Set(25, 0);
   panel.Append(_button);
}

private void OnButtonClick(UIMouseEvent evt, UIElement listeningElement) {
	_button.Text = "I was clicked!";
}
```