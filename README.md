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


	
	
	
	
	
	
