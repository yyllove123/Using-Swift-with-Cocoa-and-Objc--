# Using-Swift-with-Cocoa-and-Objc--
对Using Swift With Cocoa and Objective-C 的学习与翻译。


##互用性Interoperability

###与Objective-C API的互用

互用性是一种能力，可以让你在同一个文件中用Swift语言和Objective-C语言写代码。如果你想引入Swift到你的开发项目中，你最好还是先了解Cocoa APP项目的定义、导入、提高的方式。

一个重要的互用性是，可以在Swift代码中引入Objective-C的API。一旦引入，你就可以用Swift的语法来实例化Objective-C的类。

####初始化

用Swift语法实例化一个OC的类。OC给Swift提供了一些init方法。“init”前缀
如果是init的前缀直接可以这样初始化

	let view: UIView = UIView()
	
OC的初始化方法前缀如果是“initWith”,则with后面的参数一个一个的接着传入，如：

	“let myTableView: UITableView = UITableView(frame: CGRectZero, style: .Grouped)”

其它的的初始化方法用with后的参数来订，如：

	“let color = UIColor(red: 0.5, green: 0.0, blue: 0.5, alpha: 1.0)”

####属性

属性使用是一般的点符号。

	myTextField.text = "Hello world"
	
####方法使用

调用使用 dot语法。

要传多个参数的时候，第一个参数名需要写在括号外边，括号里面的第一个参数直接传参数值没有参数名，其它参数在括号内传参数名和参数值。

	myTableView.insertSubview(mySubview, atIndex: 2)
	
如果没有参数,则这样调用

	myTableView.layoutIfNeeded()
	
	
#### id参数

Swift提供了而一个对象“AnyObject”来代表任何类型的对象，就像是OC中的id类型一样。

在Swift中可以用此种对象来接收OC传过来的id对象。

	var myObject: AnyObject = UITableViewCell()

你可以用此对象调用任何的OC方法，但如果对象的真正没有这个方法，会在运行时报错。crash

不过swift中提供了一个语法，如果没有这个方法，将会返回一个nil，就不会crash
	
	let myCount = myObject.count?
	let myChar = myObject.characterAtIndex?(5)
	if let fifthCharacter = myObject.characterAtIndex?(5) {
	    println("Found \(fifthCharacter) at index 5")
	}
	
#### nil对象

在Swift中,不存在nil对象，所以在调用OC方法的时候，必须要确认不会return一个为nil的对象。

####扩展-类别 Extensions

Extensions 可以扩展现有行为，类、结构体、枚举

下面是一个将UIBezierPath扩展，直接生成等边三角形的代码。

	extension UIBezierPath {
	    convenience init(triangleSideLength: Float, origin: CGPoint) {
	        self.init()
	        let squareRoot = Float(sqrt(3.0))
	        let altitude = (squareRoot * triangleSideLength) / 2
	        moveToPoint(origin)
	        addLineToPoint(CGPoint(x: triangleSideLength, y: origin.x))
	        addLineToPoint(CGPoint(x: triangleSideLength / 2, y: altitude))
	        closePath()
	    }
	}

你可以给一个类通过Extension添加属性，但是这些属性必须是计算类的，Extension不能添加对象属性。
	
下面是给CGRect添加了一个 area属性：

	extension CGRect {
	    var area: CGFloat {
	        return width * height
	    }
	}
	let rect = CGRect(x: 0.0, y: 0.0, width: 10.0, height: 50.0)
	let area = rect.area
	// area: CGFloat = 500.0

	
不能添加对象已经存在的属性。

####Closures 与block

Swift中用Closures代替了block。你可以向OC方法中需要传block的地方传closures代码。也可以直接在Swift中直接用closures来接收OC的block参数。

OC的写法为：

	void (^completionBlock)(NSData *, NSError *) = ^(NSData *data, NSError *error) {/* ... */}

Swift的写法为：

	let completionBlock: (NSData, NSError) -> Void = {data, error in /* ... */}
	
Closures和block是可以并存的，所以你可以向
	
####对象比较

在Swift中有两种方式来比较对象是否相等。
第一种是（==）号 内容比较。
第二种是（===）号 内容和类型比较。

在Swift把NSObject的isEquel:方法删除了，如果您需要把Swift对象传给OC代码，并需要调用isEquel:方法，则您需要重新实现isequel方法。
	
在重新实现相等的时候，必须确保实现hash属性。这样如果将来你用你的类作为keys在dictionary中，这也符合Hashable协议实现hashvalue属性。

####Swift类型兼容

如果你创建了一个Swift类，继承OC对象，则它将自动拥有OC对象的所有属性方法。

一些情况下，你可能需要更详细的管理Swift API如何暴露给OC。
你可以使用`@objc`	关键字来表示对OC提供的属性，或者你想在对OC提供接口时改变名字符号。

可以使用关键字`dynamic`来设置某个成员是动态的。

####Swift向OC提供API接口

如果你的Swift类是继承的OC对象，那所有的事情你都不需要担心，在编译的时候，系统会帮你处理得很好。
如果你的Swift类不是继承的OC对象，并你想在OC中使用它，则可以通过`@objc`来描述。

`@objc`可以使你的Swift API能在OC中使用。你可以将此关键字用在你想在OC中使用的属性、方法前。如果你的类继承OC对象，则编译器帮你插入了这些代码。

当你在OC中使用Swift API时，编译器做了一个转换，比如：Swift API是 *func playSong(name: String)*,会被转换为*- (void)playSong:(NSString )name*,可是这里有个异常：当你在使用初始化方法的时候，编译器会把*"initWith"*加到开始方法之前，如：*init (songName: String, artist: String)* 就是 *-(instancetype)initWithSongName:(NSString)songName artist:(NSString)artist*

Swift还提供了一个变量给`@objc`，让类在OC中有个特殊别名，可以调用。如下图，可以使用Squirrel来代替Белка这个特殊字符。

	@objc(Squirrel)
	class Белка {
	    @objc(initWithName:)
	    init (имя: String) { /*...*/ }
	    @objc(hideNuts:inTree:)
	    func прячьОрехи(Int, вДереве: Дерево) { /*...*/ }
	}

引用-[Swift和Objective-C如何兼顾？且看@objc和Dynamic](http://mobile.51cto.com/hot-465065.htm)-开始
		
	Objective-C 和 Swift 在底层使用的是两套完全不同的机制，Cocoa 中的 Objective-C 对象是基于运行时的，它从骨子里遵循了 KVC (Key-Value Coding，通过类似字典的方式存储对象信息) 以及动态派发 (Dynamic Dispatch，在运行调用时再决定实际调用的具体实现)。而 Swift 为了追求性能，如果没有特殊需要的话，是不会在运行时再来决定这些的。也就是说，Swift 类型的成员或者方法在编译时就已经决定，而运行时便不再需要经过一次查找，而可以直接使用。
	
引用结束---

动态派发一般是不是必要的，可是，你必须用**dynamic**来修饰你知道的动态派发的地方。


####OC Selectors

OC的selector作为一个方法的类型，在Swift中用Selector结构体代替，你能创建一个selector像这样：*let mySelector: Selector = "tappedButton:”*

例子：

	import UIKit
	class MyViewController: UIViewController {
	    let myButton = UIButton(frame: CGRect(x: 0, y: 0, width: 100, height: 50))
	    
	    init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: NSBundle?) {
	        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
	        myButton.addTarget(self, action: "tappedButton:", forControlEvents: .TouchUpInside)
	    }
	    
	    func tappedButton(sender: UIButton!) {
	        println("tapped button")
	    }
	}

如果你的方法集成OC类，所有的方法都可以直接使用selector，不然，你需要使用*@objc*属性标记。


###用OC的方式写Swift类

####集成OC类

在Swift中，你可以在Swift类名后添加（：+ OC类名）来集成OC的某个类。

	import UIKit
	 
	class MySwiftViewController: UIViewController {
	    // define the class
	}
这样你能获取到所有的OC方法。如果你像重写某个方法，使用*override*关键字。

####使用OC类的协议

在父类后面直接使用OC协议

	class MySwiftViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
	    // define the class
	}
OC的协议相当于Swift的协议，如果你想在Swift里使用*UITableViewDelegate*协议，就直接使用*UITableViewDelegate*

因为命名空间的类和协议已经映射到Swift里。

####初始化器和释放器

在Swift中没有给你实现初始化器，你需要实现初始化方法。

###集成Interface Builder

在Swift编译器中包括了Interface Builder的功能，你可以使用 outlets，actions和live显示。

####Outlets 和 Actions
	
outlets和actions可以将你的代码和Interface Builder连接起来。在属性或方法前插入*@IBOutlet*或*@IBAction*实现。你能使用*@IBOutlet*来连接一个集合，比如一个特殊的数组。

	class MyViewController: UIViewController {
	    @IBOutlet weak var button: UIButton!
	    @IBOutlet var textFields: [UITextField]!
	    @IBAction func buttonTapped(AnyObject) {
	        println("button tapped!")
	    }
	}

#### Live Rendering（渲染？）
你可以使用连个不同的属性*IBDesignable*和*@IBInspectable*来使一个自定义view展现在Interface Builder上。
你可以在一个继承自UIView或NSView的类前加入*@IBDesignable*属性，然后可将此view加入IB中，IB将显示你的view。

你可以在属性变量前使用*@IBInspectable*，这样你能在检查器里编辑这些属性。

	@IBDesignable
	class MyCustomView: UIView {
	    @IBInspectable var textColor: UIColor
	    @IBInspectable var iconHeight: CGFloat
	    /* ... */
	}	

####Property属性说明

Swift的属性默认是strong，用*weak*来表示属性为week。

####Read/Write和只读
在Swift中没有readwrite和readonly属性。
一个对象属性，使用*let*表示只读，使用*var*表示可以读写。
一个计算属性，实现一个get方法来提供只读。

####Copy语义

在Swift中，copy属性就是*@NSCopying*协议，实现协议来实现copy。

###使用Core Data 
Core Data提供

###OC中使用Swift类名


###使用Cocoa数据类型

####字符串

####本地化

####数值
	
	
	
	
	
	
	
	
	
	
	
	
	
	