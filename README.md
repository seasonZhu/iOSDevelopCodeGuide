# iOS移动开发规范

## 感谢

本文的整理与扩充非常感谢CodeStar的博客文章：[Swift 开发规范](https://coder-star.github.io/iOS/%E8%A7%84%E8%8C%83/Swift%E5%BC%80%E5%8F%91%E8%A7%84%E8%8C%83/)。

我在原作者CodeStar的基础上，补充了示例代码，和自己的一点开发经验，调整了部分格式。

## 引言

>正所谓万事开头难，所以在起草这份iOS移动开发规范的时候，我查阅了大量资料与参考文档，阅读了知名第三库代码与结合自己的开发经验，希望可以从更多维度去思考哪些规则值得说明。

>同时，规范是死的，人是活的，移动端开发的编程语言也一直在向前演进，当规范和当前的业务有冲突的时候，我们不能在规范上一蹴而就，保持向上与探索的心，才是让技术更上一层楼的动力。

## Swift编码规范

### 一、命名规约

- 【强制】使用美式英语拼音符合Apple API的标准；

  ```
  正例：let color = "red"
  反例：let colour = "red"
  ```

- 【强制】代码中的命名严禁使用拼音及英文混合的方式，更不允许直接出现中文的方式，最好也不要使用下划线或者美元符号开头；

  ```
  正例：name / firstName / student / getPointByName()
  反例：_name $name / 学生 / getPingfenByName()
  ```

- 【强制】文件名、class、struct、enum、protocol 命名统一使用 UpperCamelCase 风格；

  ```
  正例：class LoginName { } / enum SexType { }
  反例：class loginName { } / enum SexTYPE { }
  ```

- 【强制】方法名、参数名、成员变量、局部变量、枚举成员统一使用 lowerCamelCase 风格

  ```
  正例：localValue / getMessageInfo()
  反例：LocalValue / GetMessageInfo()
  ```

- 【强制】命名中出现缩略词时，缩略词要么全部大写，要么全部小写，以首字母大小写为准，通用缩略词包括 `JSON`、`URL`、`ID` 等；

  ```
  正例：class IDUtil {} /  func idToString()
  反例：class IdUtils {} / func iDToString()
  ```

- 【强制】不要使用不规范的缩写，力求语义表达完整清楚，能直观的表达意图，不怕名称长；

  ```
  正例：class RoundAnimatingButton: UIButton {}
  反例：class CustomButton: UIButton {}  / AbstractClass 缩写成 AbsClass
  ```

- 【推荐】全局常量命名使用 k 前缀 + UpperCamelCase 命名；
  说明：本质上是不推荐使用全局常量的，主要原因是会散落到代码各处，不方便管理；有些文档说最好不要使用匈牙利式的命名规则，但是考虑到从OC保持下来的习惯，k前缀能够表明是全局常量，更好的进行区分；

  ```
  正例：kMaxLocaolStoreCount
  反例：maxLocaolStoreCount
  ```

- 【推荐】扩展文件，用`原始类型名＋扩展名`作为扩展文件名，其中原始类型名及扩展名也使用 UpperCamelCase 风格，如果扩展文件中功能不属于同一类，也可使用`原生类型名 +Extensions`的形式，`+`这种方式是从OC遗留下来的习惯，而UpperCamelCase则是一些Swift开源项目的例子，例如SwifterSwift中的扩展分类，就属于这种；

  ```
  正例：UIView+Frame.swift / MessageViewController+Request.swift / UIViewExtensions.swift
  ```

- 【推荐】工程中文件夹或者 Group 统一使用 UpperCamelCase 风格，一律使用单数形式；

  ```
  正例：Resource / Util
  ```

- 【推荐】文件名如果有复数含义，文件名应使用复数形式，如一些工具类；

  ```
  正例：MessageUtils.swift
  ```

- 【推荐】布尔类型属性使用 `is` 作为属性名前缀，返回值为布尔型类型的方法名使用 `is` 作为方法名作为前缀；

  说明：关于这一点，Apple的代码中就有很多例子，比如UIView的隐藏属性；

  ```
  let view = UIView()
  view.isHidden = true
  ```


### 二、定义、修饰规约

- 【强制】不要使用魔法值（即未经定义的常量）；

  ```
  正例：
  let maxDisplayCount = 5
  if index == maxDisplayCount {}
  反例：
  /// 5的含义不明确
  if index == 5 {}
  ```

- 【强制】能用 `let` 修饰的时候，不要使用 `var`；

  说明：一开始编码可以都使用let，后面编译器报错，再改为var；

  ```
  struct Person {
  	let name: String
  	let age: Int
  }
  ```

  

- 【强制】`extension` 上不用加任何修饰符，修饰符加在 `extension` 内的变量或方法上；
  说明：目的是当修改`extension`中某个方法的访问限制时，不需去考虑外部的`extension`访问限制，降低影响面；**需要注意的是，如果某个分类中的所有方法，会作为模块对外引用的时候，使用`public`修饰extension会比每个方法前都加上`public`更加简洁；**

  ```
  正例：
  extension UIView {
    public func removeAllSubView() {}
  }
  反例：
  public extension UIView {
    func removeAllSubView() {}
  }
  ```

- 【推荐】修饰符顺序按照 注解、访问限制、`static`、`final` 顺序；
  说明：注解是指起始于 @的关键字，如`@discardableResult、@objc`等；访问限制是指`public`、`private`等；

  ```
  正例：
  @objc
  public static final func getMessageInfo() {}
  ```

- 【推荐】考虑方法是否会被重写。如果不会，标记为 `final`；
  说明：Swift 在编译时会优化 `final` 修饰的方法，派发方式可能由函数表派发优化为直接派发；同时如果一个`class`如果不会被继承，也可以考虑使用`final`修饰；

  ```
  final aFunction() {}
  
  final class Student {}
  ```

- 【推荐】尽可能利用访问限制修饰符控制类、方法等的访问限制，遵循开闭原则；
  说明：如确定某方法或变量不应该被外部调用，就使用`private`进行修饰，编译程序阻止外部不合适的调用；**同时`private`在 Swift 中会被隐式加上`final`修饰，从而得到优化；**

- 【推荐】表示单例的静态属性，一般命名为 `shared` 或者 `default`，并切记将构造函数私有，否则单例毫无意义，同时单例的类最好使用`final`修饰，避免继承重写；

  ```
  正例：
  final class ApplicationServiceManager {
      public static let shared =  ApplicationServiceManager()
      private init {}
  }
  ```

### 三、格式规约

- 【强制】类、函数左大括号不另起一行，与名称之间留有空格；
- 【强制】代码中的空格出现地点
  - 注释符号与注释内容之间有空格；
  - 类继承，参数名和类型之间等，冒号前面不加空格，但后面跟空格；
  - 任何运算符前后有空格；
  - 表示返回值的 -> 两边；
  - 参数列表、数组、元祖、字典里的逗号后面有一个空格；
- 【强制】禁止使用无用分号；
- 【强制】方法之间空一行；
- 【强制】重载的声明放在一起，按照参数的多少从少到多向下排列；
- 【强制】每一行只声明一个常、变量；
- 【强制】如果大括号内为空，直接简写为{}，括号之间不需换行；
- 【强制】`if` 后面的 `else\else if`, 跟着上一个 `if\else if` 的右括号；
- 【强制】`switch` 中，`case` 跟 `switch` 左对齐；
- 【推荐】每行代码长度应小于 100 个字符，或者阅读时候不应该需要滚动屏幕，在正常范围内可以看到完整代码；
- 【推荐】解包时推荐使用原有名字，前提是解包后的名字与解包前的名字在作用域上不会形成冲突；
- 【推荐】实现每个协议时，在单独的 `extension` 里来实现；
- 【推荐】赋值数组、字典时每个元素分别占用一行时，最后一个选项后面也添加逗号；
  说明：这样未来如果有元素加入会更加方便；
- 【推荐】建议在一个`.swift`文件的引入文件为系统库->第三方库->私有库->工程模块，并且通过空一行进行区分；
- 【推荐】Swift中的关键词也是可以用于定义为变量的，只是需要通过``进行修饰；

#### 代码示例（代码不具有业务含义，只是简单的格式规约示例）

```
/// 系统库
import UIKit
import WebKit

/// 第三方库
import Alamofire

/// 私有库
import SSRequest

/// 工程模块
import MyModule

/**
 涉及规约
 1、类左大括号不另起一行；
 2、类继承后跟空格；
 */

/// 格式规约示例
class FormatSample: NSObject {
    /**
     涉及规约
     1、注释符号与注释内容之前有空格；
     2、每一行只声明一个变量；
     3、不使用分号；
     4、注释另起一行，不放在行尾；
     5、数组、元祖、字典里的逗号后面有一个空格；
     6、赋值数组、字典时每个元素分别占用一行时，最后一个选项后面也添加逗号，这样未来如果有元素加入会更加方便
     7、Swift中的关键词也是可以用于定义为变量的，只是需要通过``进行修饰
     */

    private var resultCode = ""
    private var resultArr = ["one", "two",]
    private var resultDic = ["key": "name", "value": "张三",]
    private var resultTuple = (key: "name", value: "张三")
    private var `switch`: Int?
}

/**
 涉及规约
 1、方法之间空一行；
 2、重载的声明放在一起，按照按照参数的多少从少到多排序；
 3、返回值 -> 两遍增加空格；
 4、参数名与类型之间空格；
 5、如果大括号内为空，则直接简写为{}，括号内不换行；
 6、if 后面的 else\else if, 跟着上一个 if\else if 的右括号；
 7、解包时推荐使用原有名字；
 */
 
/// 这是一个分类
extension FormatSample {
    private func logInfo(message: String) {
        logInfo(message: message, type: nil)
    }

    private func logInfo(message: String, type: String?) {
        if let type = type {
            print("\(type): \(message)")
        } else {
            print(message)
        }
    }

    private func canLog() -> Bool {
        #if DEBUG
            return true
        #else
            return false
        #endif
    }

    /**
     涉及规约
     1、switch 中, case 跟 switch 左对齐；
    */
    private func showSwitchStandardFormat() {
      let count = 10
      switch count {
      case 1:
        print(1)
      // 如case包含所有情况，可不加default，如遍历枚举类型时
      default:
        break
      }
    }
}
```

### 四、简略规约

- 【强制】Swift 会为结构体按照自身的成员自动生成一个非 public 的初始化方法，如果这个初始化方法刚好适合，不要自己再声明；

  ```
  /// 会自动生成 init(name: String) 这样的构造函数，如果符合使用，不要再手动添加该构造函数
  struct LoginInfo {
    var name: String
  }
  ```

- 【强制】类及结构体初始化方法不要直接调用`.init`，直接直接省略，使用 ()；

  ```
  正例：
  let loginView = UIView()
  反例：
  let loginView = UIView.init()
  ```

  说明：如果需要在高阶函数中使用构造函数，`init()`是不能进行省略的

  ```
  struct Season {
      let name: String
  }
  
  let strings = ["spring", "summmer", "autumn", "winter"]
  
  let seasons: [Season] = strings.map { .init(name: $0) }
  ```

- 【强制】如果只有一个 `get` 的计算属性，忽略 `get`；

  说明：在Swift5中，如果只有只读计算属性，连`return`都可以省略；

  ```
  正例：
  var info: String {
    return ""
  }
  
  /// 省略return
  var info: String { "info" }
  反例：
  var info: String {
    get {
      return ""
    }
  }
  ```

- 【强制】数据定义时，简单类型尽量使用字面量形式进行自动推断，如果上下文不足以推断字面量类型或者类型比较复杂时，需要声明赋值类型；

  说明：类型自动推断，目前主要在Swift与Kotlin中进行使用，Dart虽然也有类型自动推断，不过查阅大部分资料，建议编码时还是显式声明；

  ```
  正例：var info = ""
  反例：var info: String = ""
  ```

- 【强制】省略默认的访问权限（internal）；

  说明：访问权限从小到大：private/fileprivate/internal（默认）/public/open；

  ```
  正例：var info = ""
  反例：internal var info = ""
  ```

- 【强制】使用枚举属性时使用自动推断，进行缩写；

  ```
  enum Sex {
    case male
    case female
  }
  正例：let sex: Sex = .male
  反例：let sex: Sex = Sex.male
  ```

- 【强制】switch-case 里不用显式添加 `break`，需要注意；

  ```
  let count = 10
  switch count {
  case 1:
    print(1)
    /// 此处不用显式添加break，Swift中每个case都会默认break。
  }
  ```

- 【强制】访问实例成员或方法时不要使用 `self.`，特殊场景除外，如构造函数，闭包内；

  ```
  class LoginInfo {
    func log() {}
  
    func recordInfo() {
      /// 正例
      log()
  
      /// 反例
      self.log()
    }
  }
  ```

- 【强制】当方法无返回值时，不需添加 `Void`；

  ```
  正例：func getMessageInfo() {}
  反例：func getMessageInfo() -> Void {}
  ```

- 【强制】无用的代码及时删除；
  说明：可能有开发者觉得有些代码可能后续会用到，所以采取了注释的方式。但是这种方式很容易演变成代码会一直放在那，永远不会删掉。即使觉得后续会用到，也请及时删除掉，不然 Git 留着干什么用呢？

- 【推荐】在 `switch` 语句里的 `fallthrough`尽量不要使用；

  当 `switch` 里的多个 `case` 执行同样的语句时，这些 `case` 可以合并成一个范围或者逗号分隔的列表。声明多个 `case` 却不做任何事，只是 `fallthrough` 到后面的 `case` 是不允许的；

  ```
  正例：
  switch value {
  case 1: print("one")
  case 2...4: print("two to four")
  case 5, 7: print("five or seven")
  default: break
  }
  
  反例：
  switch value {
  case 1: print("one")
  case 2: fallthrough
  case 3: fallthrough
  case 4: print("two to four")
  case 5: fallthrough
  case 7: print("five or seven")
  default: break
  }
  ```

- 【推荐】使用闭包时，尽量使用最简写，如优先使用尾随闭包等；

  说明：一般编译器都会自动进行尾随闭包的使用；

  ```
  UIView.animate(withDuration: 1) {
  
  } completion: { _ in
  
  }
  ```

- 【推荐】过滤，转换等，优先使用 filter, map 等高阶函数简化代码，并尽量使用最简写；

  说明：高阶函数简化代码，可能在开发者之间阅读过程中会不易于理解，建议写清楚注释与说明；

  ```
  [1, 2, 3].map({ (i: Int) -> Int in return i * 2 })
  [1, 2, 3].map({ i in return i * 2 } )
  [1, 2, 3].map({ i in i * 2 })
  [1, 2, 3].map({ $0 * 2 })
  [1, 2, 3].map() { $0 * 2 }
  [1, 2, 3].map{ $0 * 2 }
  ```

- 【推荐】尽量使用各种语法糖；
  说明：语法糖一定程度上会降低代码的可度性，这个度根据项目组对于Swift的理解进行调整；

  ```
  /// 使用lazy懒加载
  lazy let label: UILabel = {
    let label = UILabel()
    label.textAlignment = .center
    label.textColor = .black
    label.text = "Hello, World!"
    return label
  }()
  ```

  同时可以考虑使用[then](https://github.com/devxoul/Then)这个库来进行糖语法封装；

  ```
  import UIKit
  
  import Then
  
  let label = UILabel().then {
    $0.textAlignment = .center
    $0.textColor = .black
    $0.text = "Hello, World!"
  }
  ```

- 【推荐】类似注解的修饰词单独占一行，如`@objc`，`@discardableResult` 等；

  ```
  @objc
  func buttonAction() {}
  
  @discardableResult
  func getSome() -> Self {
  	/// 业务逻辑
  	return self
  }
  ```

- 【推荐】如果 for 循环在函数体中只有一个 if 判断，使用 `for where` 进行替换；

  ```
  struct Person {
  	let age: Int
  	let name: String
  }
  
  /// 我组合成了一个Person数组
  let p1 = Person(age: 10, name: "season")
  let p2 = Person(age: 20, name: "soso")
  let p3 = Person(age: 30, name: "sola")
  
  let array = [p1, p2, p3]
  
  正例:
  for person in array where person.age == 20 {
      /// 做其他的业务处理
  }
  
  反例：
  for person in array {
      if person.age == 20 {
          /// 做其他的业务处理
      }
  }
  ```

### 五、注释、可读规约

- 【强制】文档（API）注释使用单行注释，即`///`，不使用多行注释，即`/** */`。 多行注释用于对某一代码段、设计或者复杂业务进行描述；

  说明：OC那一套注释说明规则已经完全过时，目前OC和Swift都是使用的Swift的注释规则；

- 【强制】对于公开的类、方法以及属性等必须加上文档（API）注释，方法需要加上对应的`Parameter（s）`、`Returns`、`Throws` 等标签，建议使用`⌥ ⌘ /`自动生成文档模板；

- 【强制】将注释放在代码上一行，而不是放在代码后；
  说明：放在代码后有两个弊端，一是当代码稍微长一点后，注释可能需要横向滚动后才能看全；另一个弊端是，当代码修改，极易将注释删除，或者由于后面有注释，前面的代码修改起来有些许不方便；

- 【推荐】在代码中灵活的使用一些地标注释，如`MARK`、`FIXME`、`TODO`，当同一文件中存在多种类型定义或者多种逻辑时，可以使用`//MARK: -`进行分组注释，方便通过`Xcode`顶部面包屑进行切换，这样会在代码之前有分割线，便于阅读和分类；

- 【推荐】实现每个协议时，尽量在单独的 extension 里来实现；

#### 代码示例

```
// MARK: - View子视图操作相关
extension UIView {
    /// 同时添加多个视图
    /// - Parameter subviews: 子View可变参数
    public func addSubviews(_ subviews: UIView...) {
        subviews.forEach(addSubview)
    }

    /// 移除所有子View
    public func removeAllSubview() {
        subviews.forEach {
          $0.removeFromSuperview()
        }
    }
}

class ViewController: UIViewController {

}

//MARK: - TableView的数据源
extension ViewController: UITableViewDataSource {
	/// 实现的数据源方法
}

//MARK: - TableView的代理
extension ViewController: UITableViewDelegate {
	/// 实现的代理方法
}
```

### 六、编译效率规约

该段是提高编译效率，减少编译时间总结提出的规约，对代码可读性及统一风格等影响不大，所以该部分只是作为推荐，不强制一定遵守。

- 【推荐】数组合并建议使用 append 方法而不是 + 号拼接；

  ```
  var resultArr = ["1", "2"]
  let extraArr = ["3", "4"]
  正例：
  resultArr.append(contentsOf: extraArr) 
  let newArray = [resultArr, extraArr].joined()
  反例：
  resultArr += extraArr 
  let newArray = resultArr + extraArr
  ```

- 【推荐】字符串合并避免使用 + 号而是多采用`"\(str1)\(str2)"`的形式；

  ```
  let code = "200"
  let message = "success"
  正例：let result = "\(code)\(message)"
  反例：let result = code + message
  ```

- 【推荐】if 的条件部分不要做过多运算

  说明：如果if后面的逻辑非常复杂，推荐先通过变量计算好，再来if的条件判断；

  ```
  正例：if count == 900 {}
  反例：if count == 60 * 60 / 2 / 2 {}
  ```

- 【推荐】不要让可选值使用`??` 赋默认值再嵌套其他运算；

- 【推荐】将长计算式代码拆分，最后组合计算；

- 【推荐】尽量不使用 `Storyboard` 或者 `Xib`，会增加编译时间；

  说明：不管是SwiftUI还是Flutter，未来的趋势是函数响应式编程进行UI编写，同时为了避免不必要的 `Storyboard` 或者 `Xib`迭代与维护成本，优先使用纯代码；

- 【推荐】减少三目运算符的使用；

### 七 、优化规约

- 【强制】函数参数数量最多不得超过 8 个；
  说明：寄存器数目问题，超过 8 个会影响效率；

- 【强制】避免强制解包以及强制类型映射，尽量使用`if let` 或 `guard let`进行解包，禁止`try！`形式处理异常，避免使用隐式解包；

  ```
  guard let model = try? JSONDecoder().decode(Person.self, from: data) else {
      return
  }
  
  if let model = try? JSONDecoder().decode(Person.self, from: data) {
      return
  }
  ```

- 【强制】避免判断语句嵌套层次太深，使用 guard 提前返回，如果有多个值需要`guard`，可以根据情况进行一次性`guard`，或者多段`guard`;

  ```
  /// 一次性写完
  guard let username = self?.usernameFiled.text,
        let password = self?.passwordField.text,
        let repassword = self?.repasswordField.text else {
      return
  }
  
  
  /// 分段写，便于分析与调试问题
  guard let username = self?.usernameFiled.text else {
      return
  }
  
  guard let password = self?.passwordField.text else {
  
  }
  
  guard let repassword = self?.repasswordField.text else {
      return
  }
  ```

- 【推荐】使用弱引用`weak`来修饰对象，来避免循环引用；

  说明：代码应避免指针循环引用，分析对象图谱，使用弱引用`weak`和未知引用`unowned`避免强引用循环。另外使用值类型(`struct`和`enum`)可以避免循环引用；延长对象生命周期习惯上使用`[weak self]` 和 `guard let strongSelf = self else { return }`。 `[weak self]`优于`[unowned self]`因为前者更能明显地`self` 生命周期长于闭包块。 显式延长生命周期优先于可选性拆包；

  ```
  正例：
  resource.request().onComplete { [weak self] response in
    guard let strongSelf = self else {
      return
    }
    let model = strongSelf.updateModel(response)
    strongSelf.updateUI(model)
  }
  
  反例：
  /// self可能在响应返回前被release，而导致崩溃 
  resource.request().onComplete { [unowned self] response in
    let model = self.updateModel(response)
    self.updateUI(model)
  }
  
  反例：
  /// 调用方法前，self已经被release，而导致方法无法被执行
  resource.request().onComplete { [weak self] response in
    let model = self?.updateModel(response)
    self?.updateUI(model)
  }
  ```

- 【推荐】如果对于类的weak-strong的跳跃使用难以理解或者维护，可以考虑使用第三方库[Delegate](https://github.com/onevcat/Delegate)进行处理；

  ```
  import Delegate
  
  class ClassA {
      let onDone = Delegate<(), Void>()
  
      func doSomething() {
          // ...
          onDone()
      }
  }
  
  class MyClass {
      func askClassAToDoSomething() {
          let a = ClassA()
          a.onDone.delegate(on: self) { (self, _) in
              self.jobDone()
          }
          a.doSomething()
      }
  
      private func jobDone() {
          print("🎉")
      }
  }
  ```

- 【推荐】使用委托和协议时，避免循环引用，定义属性的时候使用 `weak` 修饰；

  ```
  protocol ClassADelegate {
      func doSomethingIsDone()
  }
  
  class ClassA {
      weak var delegate: ClassADelegate?
  }
  ```

- 【推荐】能用`struct`解决的，尽量使用`struct`而不是`class`；
  说明：`struct` 属于值类型，使用其有很多好处：效率高、不需担心循环引用问题、线程安全等；

  ```
  /// 优先考虑设计struct类型的Dog，而不是class类型的Dog
  struct Dog {}
  class Dog {}
  ```

- 【推荐】编写类`class`时，如果可以不继承`NSObject`尽量不要继承；

  说明：很多从OC转Swift的开发人员，都喜欢在编写类的时候继承`NSObject`，如果没有必要，不需要继承`NSObject，也可以卸下历史包袱；

  ```
  正例：
  class Animal {}
  反例：
  class Animal: NSObject {}
  ```

- 【推荐】对 String 判空时优先采用 `isEmpty`
  说明：Swift 里面的 String 的 `index` 和 `count` 不是一一对应的（兼容 `Unicode`），所以 `stirng.count == 0` 的效率不如 `string.isEmpty`；[Why using isEmpty is faster than checking count == 0](https://www.hackingwithswift.com/articles/181/why-using-isempty-is-faster-than-checking-count-0)，同时所有的集合类型（Collection）都可以通用此规则；

  ```
  let string = ""
  let array = []
  正例：
  if string.isEmpty {
  
  }
  
  if array.isEmpyt {
  
  }
  反例：
  if string.count == 0 {
  
  }
  
  if array.count == 0 {
  
  }
  ```

- 【推荐】如果需要判断变量的类型，建议使用`is`，同时也可以使用`switch`来进行转换，`func isKind(of aClass: AnyClass) -> Bool`是针对继承NSObject的对象，在Swift中的使用并不是那么广泛；

  ```
  func getSomeObject(object：Any) {
    if object is UIImage {
    	print("object is a UIImage")
    }else if object is String {
    	print("object is a String")
    }else if object is URL {
    	print("object is a URL")
  	}
  }
  
  func getAnotherObject(object：Any) {
      switch object {
      case let dict as [String: Any]:
          print("object is a dict:\(dict)")
      case let array as [Any]:
          print("object is a array:\(array)")
      default:
          print("object is unknown")
      }
  }
  ```

- 【推荐】如果异步回调有返回Error的可能，建议使用Result类型去包装回调结果；

  ```
  正例：
  public typealias Completion = (_ result: Result<Moya.Response, MoyaError>) -> Void
  反例：
  public typealias Completion = (_ response: Moya.Response?, _ error: MoyaError?) -> Void
  ```

- 【推荐】合理的使用`defer`，可以让代码逻辑更加清晰与安全；

  ```
  /// 开启图片上下文
  UIGraphicsBeginImageContext(window.size)
  /// 通过defer，将容易忘记的关闭就写在BeginImage之后，这样避免出错
  defer {
  		/// 关闭图片上下文
      UIGraphicsEndImageContext()
  }
  ```

- 【推荐】如果协议只会被类使用，建议加上`AnyObject`限制，需要注意的是Swift5之后，协议后面加上class限制已经过期，统一加AnyObject；
  说明：编译器可以对此进行优化，其可以明确在处理一个类，继而进行引用计数，而不用考虑结构体以及其带来的额外的逻辑及结构。

  ```
  正例：
  protocol Pingable: AnyObject { 
  	func ping() -> Int 
  }
  ```

### 八、设计规约

- 【推荐】善用`func`嵌套；
  说明：比如一段逻辑需要在方法内复用，可以在方法中定义方法，比如闭包中以及正常方法体中需要执行同一段逻辑；

  ```
  func a() {
  	func b() {}
  	b()
  	b()
  }
  ```

- 【推荐】委托性质的协议，第一个参数应尽量保证为代理源，类似`UITableViewDatasoure`；

  说明：方法的第一个参数都是代理源，如果方法有多个参数，第一个参数用`_`修饰；

  ```
  func numberOfSections(in tableView: UITableView) -> Int {}
  
  func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {}
  
  func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {}
  ```

- 【推荐】在方法设计时，其需要返回值，但是大部分情况业务方不需要使用返回值，可使用`@discardableResult`来对该方法进行标注，防止出现大量 Warning，具体例子可以参考`Alamofifre.Request`;

- 【推荐】尽可能少的使用全局命名空间，如常量、变量、方法等；

- 【推荐】优先创建函数而不是自定义操作符；

  说明：操作符使得入参出参不易于理解，同时可能引起项目代码与模块自定义操作符同名；

- 【推荐】Swift 中的协议和泛型十分强大，设计时应优先考虑面向协议编程；

  说明：POP还是OOP，是一个高深莫测的技术；

### 其他

- 【强制】图形化的字面量，`#colorLiteral(...)`, `#imageLiteral(...)`只能用在 playground 当做自我练习使用，禁止在项目工程中使用，这会让维护者在后期维护时无法第一眼了解到颜色的 hex 值或者图片的名称。
  说明：图形化的字面量不仅不方便直观的查看其颜色值或者图片名字，也不利于颜色、图片统一配置、管理。

### 工具

[SwiftLint 工具](https://github.com/realm/SwiftLint) 提示格式错误

[SwiftFormat 工具](https://github.com/nicklockwood/SwiftFormat) 提示并修复格式错误

两者大部分格式规范都是一致的，少许规范不一致，两个工具之间使用不冲突，可以在项目中共存。我们通过配置文件可以控制启用或者关闭相应的规则，具体使用规则参照对应仓库的 REAMME.md 文件。

## 资源文件管理规范

- 【强制】使用`Assets.xcassets`进行App工程的图片资源管理；
- 【强制】图片必须包括@2x、@3x，且命名后缀清晰一致；
- 【强制】图片命名，必须使用英文命名规则，至于使用`lowerCamelCase`还是`snake_case`规则，根据项目情况而定，最好是UI在进行切图时，就对图片进行了规范命名，开发工程师直接拖入项目工程即可；
- 【推荐】为了便于区分不同的业务模块的图片，可以在Assets.xcassets中细化文件夹进行图片资格管理；
- 【推荐】使用[R.swift](https://github.com/mac-cain13/R.swift)第三库进行资源文件管理，类似安卓中的R函数，需要注意的是`R.swift`批处理的资源文件均是lowerCamelCase命名风格；
- 【推荐】考虑图片体积与App的包大小，可以使用[TinyPNG](https://tinypng.com/)进行批量压缩处理；

## 第三方库管理规范

- 【推荐】使用Cocopods统一进行第三库管理；

- 【推荐】无特殊原因，不要将第三方库直接拖入工程中，应通过Cocopods进行管理；

- 【推荐】第三库版本，应该结合项目适配的iOS版本，Swift语言版本，Xcode版本，尽量使用最新的版本；

- 【推荐】如果模块的复用性较高，考虑建立Cocopods私有库，进行封装与管理，也可以考虑使用SPM(Swift Package Manager)进行本地管理；

## 项目工程管理规范

- 【强制】项目开始之初，需要规定好Boundle Identifier；

- 【强制】配置好对应的证书，以便于打包，使用自动签名，减少不必要的流程；

- 【强制】通过环境变量，进行网络区分，切勿通过复制粘贴进行baseURL的切换；

- 【推荐】如果App需要做深色模式适配，最好在App开始之时就规划好，同时可以通过`Assets.xcassets`进行颜色管理，也可以编写专门的管理类进行处理；

- 【推荐】通过fastlane进行内部测试的打包与分发；

- 【推荐】通过Testflight渠道进行线上环境的分发与测试，同时也便于正式上架；

## 参考文档

- [CodeStar Swift 开发规范](https://coder-star.github.io/iOS/%E8%A7%84%E8%8C%83/Swift%E5%BC%80%E5%8F%91%E8%A7%84%E8%8C%83/)
- [Swift 官方 API 设计指南](https://swift.org/documentation/api-design-guidelines/)
- [Google 发布的 Swift 编码规范](https://google.github.io/swift/#apples-markup-format)
- [linkedin编码规范](https://github.com/linkedin/swift-style-guide)
- [raywenderlich编码规范](https://github.com/raywenderlich/swift-style-guide)
- [airbnb编码规范](https://github.com/airbnb/swift)
- [Google Swift Style Guide 中文版](https://pages.swift.gg/google-swift-style-guide-in-chinese/#%E5%9C%A8-switch-%E8%AF%AD%E5%8F%A5%E9%87%8C%E7%9A%84-fallthrough)
- [swift-code-specification](https://learnku.com/articles/37145/swift-code-specification)
- [swiftbian-ma-gui-fan](https://alexcode2.gitbook.io/ios-development-guidelines/swiftbian-ma-gui-fan)


