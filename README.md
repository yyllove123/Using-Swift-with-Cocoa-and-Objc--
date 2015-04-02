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
