# Swift 编码规范


## 说明

[API Design Guidelines](https://swift.org/documentation/api-design-guidelines) 是苹果专门针对 API 的一个规范，本规范涉及到一些相关的内容，大都是保持和苹果的规范一致。

本编码规范基于 [The Official raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)。

苹果公司官方的文档里也有提到一些原理和指南： 

* [Cocoa 基本原理指南](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Cocoa 编码指南](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS 应用编程指南](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)


## 目录

* [正确性](#正确性)
* [命名](#命名)
  * [文章](#文章)
  * [代理](#代理)
  * [使用类型推断上下文](#使用类型推断上下文)
  * [泛型](#泛型)
  * [类前缀](#类前缀)
  * [语言](#语言)
* [代码组织](#代码组织)
  * [协议一致性](#协议一致性)
  * [无用的代码](#无用的代码)
  * [最少引入](#最少引入)
* [间隔](#间隔)
* [注释](#注释)
* [类和结构体](#类和结构体)
  * [应该使用哪一个](#应该使用哪一个)
  * [类定义](#类定义)
  * [self 的使用](#self-的使用)
  * [计算属性](#计算属性)
  * [Final](#final)
* [函数声明](#函数声明)
* [闭包表达式](#闭包表达式)
* [类型](#类型)
  * [常量](#常量)
  * [静态方法和变量类型属性](#静态方法和变量类型属性)
  * [可选类型](#可选类型)
  * [结构体构造器](#结构体构造器)
  * [延迟初始化](#延迟初始化)
  * [类型推断](#类型推断)
  * [语法糖](#语法糖)
* [函数 VS 方法](#函数-vs-方法)
* [内存管理](#内存管理)
  * [延长对象生命周期](#延长对象生命周期)
* [访问控制](#访问控制)
* [控制流程](#控制流程)
* [黄金路径](#黄金路径)
  * [失败防护](#失败防护)
* [分号](#分号)
* [圆括号](#圆括号)
* [组织和应用的标识符](#组织和应用的标识符)
* [参考](#参考)


## 正确性

把警告当做错误处理（打开 `Build Settings` 下 `Treat Warnings as Errors`），
这条规则从根本禁止了一些文法使用，例如推荐使用 `#selector` 文而不是用字符串。
并尽可能多的启用其他警告：[Additional Warnings](http://boredzo.org/blog/archives/2009-11-07/warnings)，
如果你需要忽略特定的警告，请使用[Clang 的编译特性](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)。

## 命名

使用具有描述性的名称，并结合驼峰式命名规则给类方法和变量等命名。
类别名称（类，结构体，枚举和协议）首字母大写，而方法或者变量的首字母小写。

**首选：**

```swift
private let maximumWidgetCount = 100
class WidgetContainer {
  var widgetButton: UIButton
  let  widgetHeightPercentage = 0.85
}
```

**不推荐：**

```swift
let MAX_WIDGET_COUNT = 100
class app_widgetContainer {
  var wBut: UIButton
  let  wHeightPct = 0.85
}
```

缩写和简写应该要尽量避免，遵守苹果命名规范，缩写和简写中的所有字符大小写要一致。

**首选：**

```swift
let urlString:  URLString
let userID:  UserID
```

**不推荐：**

```swift
let uRLString:  UrlString
let userId:  UserId
```

对于函数和初始化方法，建议给所有参数命名，除非上下文非常清晰。 如果结合外部参数命名可以让函数调用更易读，要结合起来。

```swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(column column:Int, row:Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, performAction: SKAction) ->SKAction!

// 调用上述函数时如下：
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)
```

方法命名的时候要考虑方法体中的第一个参数的名称。

```swift
class Counter {
  //combineWith + otherCounter
  func combineWith(otherCounter:  Counter, options: Dictionary?) { ... }
  //incrementBy + amount
  func incrementBy(amount: Int) { ... }
}
```

按照苹果的接口设计指导准则，枚举值使用小写开头的驼峰命名法，也就是 lowerCamelCase。

```swift
enum Shape {
  case rectangle
  case square
  case rightTriangle
  case equilateralTriangle
}
```

### 文章

当提及散文中的方法时，无歧义是至关重要的。要引用方法名称，请使用最简单的形式。

1. 编写没有参数的方法名称。**示例：** 接下来，您需要调用 `addTarget` 方法。
2. 用参数标签写入方法名称。**示例：** 接下来，您需要调用 `addTarget(_:action:)` 方法.
3. 用参数标签和类型写入完整的方法名称。**示例：** 接下来，您需要调用 `addTarget(_: Any?, action: Selector?)` 方法.

对于上述示例使用 UIGestureRecognizer，1是明确的和首选的。

**提示：** 您可以使用 Xcode 的跳转条来查找参数标签的方法。


### 代理

根据苹果接口设计指导文档，如果协议描述的是协议做的事应该命名为名词（如Collection），
如果描述的是行为，需添加后缀 -able 或 -ing（如Equatable 和 Resizing）。 如果上述两者都不能满足需求，可以添加 Protocol 作为后缀。

创建自定义代理方法时，未命名的第一个参数应该是委托源。（UIKit 有许多这样的例子）

**首选：**

```swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

**不推荐：**

```swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```


### 使用类型推断上下文

使用编译器提供的上下文推断，可使代码更简洁，清晰。（另请参阅[类型推断](#类型推断)。）

**首选：**

```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```

**不推荐：**

```swift
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
```

### 泛型

通用类型（泛型）参数应该是描述性，遵守 "大骆驼命名法"。
如果一个参数名没有具体的含义，可以使用传统单大写字符，如 `T`，`U` 或 `V` 等。

**首选：**

```swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

**不推荐：**

```swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```


### 类前缀

Swift 类型由包含它们的模块自动命名，不应该添加类似 `RW` 这样的类前缀。
如果来自两个不同模块的名称相冲突，您可以通过在类型名称前添加模块名称作来区分。
注意不要滥用模块名称，仅在有可能发生冲突或疑惑的场景下使用。

Swift 类型自动被模块名设置了名称空间，所以你不需要加一个类的前缀。
如果两个来自不同模块的命名冲突了，你可以附加一个模块名到类型命名的前面来消除冲突。

在 Swift 里，每一个模块都是一个命名空间。
而在 Objective-C 里没有命名空间的概念，只是在每个类名前面添加前缀，比如 NSArray。

当不同的模块有同名的类名时，需要指明模块名称。


```swift
import SomeModule

let myClass = MyModule.UsefulClass()
```


### 语言

使用美式英语，不使用英式英语。

**首选：**

```swift
let color = "red"
```

**不推荐：**

```swift
let colour = "red"
```


## 代码组织

使用`extension`将您的代码组织成逻辑功能块。 每个`extension`都用`// MARK：-`注释来分隔开。


### 协议一致性

当一个类遵守一个协议时，使用 `extension` 来组织代码。

**首选：**

```swift
class MyViewController: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**不推荐：**

```swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```


### 无用的代码

删除无用的代码，包括 Xcode 生成的模板代码和占位符注释应该删除。

一些方法内只是简单地调用了父类里面的方法也需要删除，包括 UIApplicationDelegate 内的空方法和无用方法。

**首选：**

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return Database.contacts.count
}
```

**不推荐：**

```swift
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
  // #warning Incomplete implementation, return the number of sections
  return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // #warning Incomplete implementation, return the number of rows
  return Database.contacts.count
}
```


### 最少引入

减少不必要的引入，例如引入 Foundation 能满足的情况下不用引入 UIKit。


## 间隔

缩进使用 **2** 个空格，在 Xcode 的偏好设置和项目设置都可以配置。

方法体的大括号和其他大括号（`if`/`else`/`switch`/`while`等）加入一个空格后在行尾开启，在新一行关闭（Xcode 默认）。

提示：⌘A 选中代码后使用 Control-I（或者菜单 `Editor` \ `Structure` \ `Re-Indent`）来调整缩进。

**首选：**

```swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

**不推荐：**

```swift
if user.isHappy
{
  // Do something
}
else {
  // Do something else
}
```

方法体内最多只有一个空行，这样可以保持整齐和简洁，如果需要多个空行，说明代码需要重构。

`:` 左面不要空格，而右面需要保留空格，三元操作符 `? :` 和空数组 `[:]`除外。

**首选：**

```swift
class TestDatabase: Database {
  var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```

**不推荐：**

```swift
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

每行长度应保持在**80**个字符左右，最长不应超过**120**个字符。

确保每个文件结尾都有空白行。

确保每行都不以空白字符作为结尾（`Xcode`->`Preferences`->`Text Editing`->`Automatically trim trailing whitespace` + `Including whitespace-only lines`）。


## 注释

如果需要注释，它只用来解释为什么这段代码要这么写，而不是解释代码的逻辑。注释必须保持最新，过期的注释要及时删除。

避免代码中出现注释块，代码本身应尽量起到注释的作用。为了生成文档的注释除外。


## 类和结构体


### 应该使用哪一个

结构体是值类型。结构体在使用中没有标识。
一个数组包含`[a, b, c]`和另外一个数组包含`[a, b, c]`是完全一样的，
它们完全可以互相替换，使用第一个还是使用第二个都一样，
因为它们代表的是同一个东西，这就是为什么数组是结构体。

类是引用类型。类使用的场景是需要一个标识或者需要一个特定的生命周期。
假设你需要对人抽象为一个类，因为两个人，是两个不同的东西。
即使两个人有同样的名字和生日，也不能确定这两个人是一样的。
但是人的生日是一个结构体，因为日期 1950/03/03 和另外一个日期 1950/03/03 是相同的，日期是结构体没有唯一标示。

有时一些事物应该定义为结构体，但是还需要兼容 `AnyObject` 或者已经在以前的历史版本中定义为类（`NSDate`、`NSSet`）， 所以尽可能的注意类和结构体之间的区别。


### 类定义

下面是一个设计良好的类定义：

```swift
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }

  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }

  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }

  override func area() -> Double {
    return Double.pi * radius * radius
  }
}

extension Circle: CustomStringConvertible {
  var description: String {
    return "center = \(centerString) area = \(area())"
  }
  private var centerString: String {
    return "(\(x),\(y))"
  }
}
```

上面的例子展示了下面的设计准则：

* 属性、变量、常量和参数等在声明定义时，其中：符号后有空格，而符号前没有空格，比如 x: Int 和Circle: Shape。
* 如果定义多个变量/数据结构时出于相同的目的和上下文，可以定义在同一行。
* 缩进 getter，setter 的定义和属性观察器的定义。
* 不需要添加 internal 这样的默认的修饰符，也不需要在重写一个方法时添加访问修饰符。
* 使用 extensions 组织扩展功能（例如打印）。
* 隐藏非共享的实现细节，如`centerString`在扩展中使用`private`访问控制。


### self 的使用

为了保持简洁，可以避免使用 self 关键词，因为 Swift 在访问对象属性和调用对象方法不需要使用 self。

不过当在构造器中需要区分属性名和参数名时需要使用 self，还有当在在闭包表达式中引用属性值。

```swift
class BoardLocation  {
	let row: Int, column: Int
	init(row: Int, column: Int)  {
		self.row = row
		self.column = column
		let closure = {
			print(self.row)
		}
	}
}
```


### 计算属性

为了保持简洁，如果一个属性是只读的，请忽略掉 get 语句。
因为只有在需要定义 set 语句的时候才需要 get 语句。

**首选：**

```swift
var diameter: Double {
  return radius * 2
}
```

**不推荐：**

```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```


### Final

给那些不打算被继承的类使用 final 修饰符，例如：

```swift
// Turn any generic type into a reference type using this Box class.
final class Box<T> {
  let value: T
  init(_ value: T) {
    self.value = value
  }
}
```


## 函数声明

在声明较短函数时保持在一行，一行内包括前括号：

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

对于声明较长的函数时，在适当的位置换行并在第二行多添加一个缩进：

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```

## 闭包表达式

仅在闭包表达式参数在参数列表中最后一个时，使用尾随闭包表达式，闭包参数命名要有描述性。

**首选：**

```swift
UIView.animate(withDuration: 1.0) {
  self.myView.alpha = 0
}

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}, completion: { finished in
  self.myView.removeFromSuperview()
})
```

**不推荐：**

```swift
UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
})

UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}) { f in
  self.myView.removeFromSuperview()
}
```

当单个闭包表达式上下文清晰时，使用隐式的返回值：

```swift
attendeeList.sort { a, b in
  a > b
}
```

链式方法使用尾随闭包会更清晰易读，至于如何使用空格，换行，还是使用命名和匿名参数不做具体要求。例如：

```swift
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
  .map {$0 * 2}
  .filter {$0 > 50}
  .map {$0 + 10}
```


## 类型

优先使用 Swift 原生类型，可以根据需要使用 Objective-C 提供的方法，因为 Swift 提供了到 Objective-C 的桥接。

**首选：**

```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**不推荐：**

```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```
在 Sprite Kit 代码中，使用 CGFloat 可以避免数据多次转换，让代码更简洁。


### 常量

定义常量使用 let 关键字，定义变量使用 var 关键字，如果变量的值未来不会发生变化要使用常量。

**建议：**
> 一个好的技巧是：使用 let 定义任何东西，只有在编译器提出警告需要改变的时候才修改为 var 定义。

你可以使用类型属性来定义类型常量而不是实例常量，使用 static let 可以定义类型属性常量。 
这样方式定义类别属性整体上优于全局常量，因为更容易区分于实例属性。

**首选：**

```swift
enum Math {
  static let e = 2.718281828459045235360287
  static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2
```

**Note：**使用枚举的好处是变量不会被无意初始化，且全局有效。

**不推荐：**

```swift
let e = 2.718281828459045235360287  // pollutes global namespace
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // what is root2?
```


### 静态方法和变量类型属性

静态方法和类别属性的工作原理类似于全局方法和全局属性，应该节制使用。
它们的使用场景在于如果某些功能局限于特别的类型或和 Objective-C 互相调用。

### 可选类型

可以变量和函数返回值声明为可选类型（?），如果 nil 值可以接受。

当你确认实例变量会稍晚在使用前初始化，可以在声明时使用 ! 来隐式的拆包类型，比如在 `viewDidLoad()` 中会初始化的子视图。

当你访问一个可选类型值时，如果只需要访问一次或者在可选值链中有多个可选类型值时，请使用可选类型值链：

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

如果可以方便的一次性拆包或者执行多次性操作时，使用可选类型绑定：

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

当我们命名一个可选值变量和属性时，避免使用如 optionalString 或 maybeView 名称，因为可选值的已经体现在类型定义中。

在可选值绑定时，直接映射原始的命名比使用诸如 unwrappedView 或 actualLabel 好。

**首选：**

```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}
```

**不推荐：**

```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```


### 结构体构造器

使用 Swift 原生的结构体构造器而不是传统的的 CGGeometry 构造器。

**首选：**

```swift
let bounds = CGRect(x: 40.0, y: 20.0, width: 120.0, height: 80.0)
let centerPoint = CGPoint(x: 96.0, y: 42.0)
```

**不推荐：**

```swift
let bounds = CGRectMake(40.0, 20.0, 120.0, 80.0)
let centerPoint = CGPointMake(96.0, 42.0)
```

推荐使用结构体内部的常量 CGRect.infiniteRect, CGRect.nullRect 等，
来替代全局常量CGRectInfinite, CGRectNull 等。对于已经存在的变量值，可以简写成 .zero。


### 延迟初始化

延迟初始化用来细致地控制对象的生命周期，
这对于想实现延迟加载视图的 UIViewController 特别有用，你可以使用即时被调用闭包或私有构造方法：

```swift
lazy var locationManager: CLLocationManager = self.makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
  let manager = CLLocationManager()
  manager.desiredAccuracy = kCLLocationAccuracyBest
  manager.delegate = self
  manager.requestAlwaysAuthorization()
  return manager
}
```

**注意：**

 - `[unowned self]` 在这里不是必须的，并没有互相引用。
 - 定位管理的负面效果会弹出对话框要求用户提供权限，这是做延时加载的原因。


### 类型推断

推荐使用更加紧凑的代码，让编译器能够推断出每个常量和变量的类型。
类型推断也适用于小的数组和字典，如果需要可以指定特定的类型，如 CGFloat 或 Int16。

**首选：**

```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumWidth: CGFloat = 106.5
```

**不推荐：**

```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
let names = [String]()
```

#### 类型注解对空的数组和字典

对空的数据和字典，使用类型注解。

**首选：**

```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**不推荐：**

```swift
var names = [String]()
var lookup = [String: Int]()
```

**注意：**这意味着选择描述性名称更加重要。


### 语法糖

使用简洁的类型定义语法，而不是全称语法。

**首选：**

```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**不推荐：**

```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

## 函数 VS 方法

自由函数不依附于任何类或类型，应该节制地使用。
如果可能优先使用方法而不是自由函数，这有助于代码的可读性和易发现性。

自由函数使用的场景是当不确定它和具体的类别或实例相关。

**首选：**

```swift
let sorted = items.mergeSorted()  // easily discoverable
rocket.launch()  // acts on the model
```

**不推荐：**

```swift
let sorted = mergeSort(items)  // hard to discover
launch(&rocket)
```

**自由函数实例：**

```swift
let tuples = zip(a, b)  // feels natural as a free function (symmetry)
let value = max(x, y, z)  // another free function that feels natural
```


## 内存管理

代码应避免指针循环引用，分析对象图谱，使用弱引用和未知引用避免强引用循环。
另外使用值类型（结构体和枚举）可以避免循环引用。


### 延长对象生命周期

延长对象生命周期习惯上使用 `[weak self]` 和 `guard let strongSelf = self else { return }`。

`[weak self]` 优于 `[unowned self]`，因为前者更更能明显地 self 生命周期长于闭包块，显式延长生命周期优先于可选性拆包。

**首选：**

```swift
resource.request().onComplete { [weak self] response in
  guard let strongSelf = self else {
    return
  }
  let model = strongSelf.updateModel(response)
  strongSelf.updateUI(model)
}
```

**不推荐：**

```swift
// might crash if self is released before response returns
resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}
```

**不推荐：**

```swift
// deallocate could happen between updating the model and updating UI
resource.request().onComplete { [weak self] response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```


## 访问控制

合理的使用 private 和  fileprivate，推荐使用 private, 在使用 extension 时可使用 fileprivate。

当您需要完整的访问控制规范时，才明确地使用`open`，`public`和`internal`。

访问控制符一般放在属性修饰符的最前面，除非需要使用 static 修饰符，@IBAction, @IBOutlet 或 @discardableResult。

**首选：**

```swift
private let message = "Great Scott!"

class TimeMachine {
  fileprivate dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

**不推荐：**

```swift
fileprivate let message = "Great Scott!"

class TimeMachine {
  lazy dynamic fileprivate var fluxCapacitor = FluxCapacitor()
}
```


## 控制流程

循环使用 for-in 表达式，而不使用 while 表达式。

**首选：**

```swift
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```

**不推荐：**

```swift
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}


var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```


## 黄金路径

当编码遇到条件判断时，左边的距离是黄金路径或幸福路径，因为路径越短，速度越快。
不要嵌套 `if` 循环，多个返回语句是可以的。`guard` 就为此而生的。

**首选：**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }

  // use context and input to compute the frequencies
  return frequencies
}
```

**不推荐：**

```swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the frequencies

      return frequencies
    } else {
      throw FFTError.noInputData
    }
  } else {
    throw FFTError.noContext
  }
}
```

当有多个条件需要用 `guard` 或 `if let` 解包，可用复合语句避免嵌套。例如：

**首选：**

```swift
guard let number1 = number1,
      let number2 = number2,
      let number3 = number3 else {
  fatalError("impossible")
}
// do something with numbers
```

**不推荐：**

```swift
if let number1 = number1 {
  if let number2 = number2 {
    if let number3 = number3 {
      // do something with numbers
    } else {
      fatalError("impossible")
    }
  } else {
    fatalError("impossible")
  }
} else {
  fatalError("impossible")
}
```

### 失败防护

防护语句的退出有很多方式，一般都是单行语句，如 `return`, `throw`, `break`, `continue` 和`fatalError()` 等。 避免出现大的代码块，如果清理代码需要多个退出点，可以用 defer 模块避免重复清理代码。


##  分号

Swift 不强制每条语句有分号，当一行内写多个语句的时候是必须的。不要在一行里写多个语句。

在构造 for 递增循环时需要使用分号，但构造 for 循环时推荐使用 for-in 结构。

**首选：**

```swift
let swift = "not a scripting language"
```

**不推荐：**

```swift
let swift = "not a scripting language";
```


## 圆括号

条件判断时圆括号不是必须的，建议省略。

**首选：**

```swift
if name == "Hello" {
  print("World")
}
```

**不推荐：**

```swift
if (name == "Hello") {
  print("World")
}
```

在更复杂的表达式中，有时可选括号可以使代码更清晰地读取。

**首选：**

```swift
let playerMark = (player == current ? "X" : "O")
```


## 组织和应用的标识符

在涉及 Xcode 项目的地方，`Organization` 应设置为组织名称，并将 `Bundle Identifier` 设置为 `com.组织缩写.项目名称`。


## 参考

* [Swift API 设计指南](https://swift.org/documentation/api-design-guidelines/)
* [Swift 编程语言](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/index.html)
* [Cocoa 下使用 Swift 和 Objective-C](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/index.html)
* [Swift 标准库参考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/SwiftStandardLibraryReference/index.html)

