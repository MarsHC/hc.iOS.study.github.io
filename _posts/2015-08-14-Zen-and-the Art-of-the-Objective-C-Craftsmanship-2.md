---
layout: default
title: 禅与Objective-C编程艺术（Zen and the Art of the Objective-C Craftsmanship）学习总结（二）
---
{{ page.title }}
=============
{{ page.date | date: "%Y-%m-%d" }}

https://github.com/oa414/objc-zen-book-cn#%E5%B0%A4%E8%BE%BE%E8%A1%A8%E8%BE%BE%E5%BC%8F

一、类
-------------

1.类名

类名应该以三个大写字母作为前缀（双字母前缀为Apple的类预留）。尽管这个规范看起来有些古怪，但是这样做可以减少Objective-C没有命名空间所带来的问题。

一些开发者在定义模型对象时并不遵循这个规范（对于Core Data对象，我们更应该遵循这个规范）。我们建议在定义Core Data对象时严格遵循这个约定，因为最终你可能需要把你的Managed Object Model（托管对象模型）与其他（第三方库）的MOMs（Managed Object Model）合并。

你可能注意到了，这本书里类的前缀（不仅仅是类，也包括公开的常量、Protocol等的前缀）是ZOC。

另一个好的类的命名规范：当你创建一个子类的时候，你应该把说明性的部分放在前缀和父类名的在中间。

举个例子：如果你有一个 ZOCNetworkClient 类，子类的名字会是ZOCTwitterNetworkClient (注意 "Twitter" 在 "ZOC" 和 "NetworkClient" 之间); 按照这个约定， 一个UIViewController 的子类会是 ZOCTimelineViewController.

2.Initializer和dealloc

推荐的代码组织方式是将dealloc方法放在实现文件的最前面（直接在 @synthesize 以及 @dynamic 之后），init应该跟在dealloc方法后面。

如果有多个初始化方法，指定初始化方法 (designated initializer) 应该放在最前面，间接初始化方法 (secondary initializer) 跟在后面，这样更有逻辑性。

如今有了ARC，dealloc方法几乎不需要实现，不过把init和dealloc放在一起可以从视觉上强调它们是一对的。通常，在init方法中做的事情需要在dealloc方法中撤销。

init 方法应该是这样的结构：

	- (instancetype)init
	{
	    self = [super init]; // call the designated initializer
	    if (self) {
	        // Custom initialization
	    }
	    return self;
	}

为什么设置 self 为 [super init] 的返回值，以及中间发生了什么呢？这是一个十分有趣的话题。

我们退一步讲：我们常常写 [[NSObject alloc] init] 这样的代码，从而淡化了 alloc 和 init 的区别。Objective-C 的这个特性叫做 两步创建 。 这意味着申请分配内存和初始化被分离成两步，alloc 和 init。

*   alloc 负责创建对象，这个过程包括分配足够的内存来保存对象，写入 isa 指针，初始化引用计数，以及重置所有实例变量。
init 负责初始化对象，这意味着使对象处于可用状态。这通常意味着为对象的实例变量赋予合理有用的值。

*   alloc 方法将返回一个有效的未初始化的对象实例。每一个对这个实例发送的消息会被转换成一次 objc_msgSend() 函数的调用，形参 self 的实参是 alloc 返回的指针；这样 self 在所有方法的作用域内都能够被访问。

按照惯例，为了完成两步创建，新创建的实例第一个被调用的方法将是 init 方法。注意，NSObject 在实现 init 时，只是简单的返回了 self。

关于 init 的约定还有一个重要部分：这个方法可以（并且应该）通过返回 nil 来告诉调用者，初始化失败了；初始化可能会因为各种原因失败，比如一个输入的格式错误了，或者另一个需要的对象初始化失败了。

这样我们就能理解为什么总是需要调用 self = [super init]。如果你的父类说初始化自己的时候失败了，那么你必须假定你正处于一个不稳定的状态，因此在你的实现里不要继续你自己的初始化并且也返回 nil。如果不这样做，你可能会操作一个不可用的对象，它的行为是不可预测的，最终可能会导致你的程序崩溃。

init 方法在被调用的时候可以通过重新给 self 重新赋值来返回另一个实例，而非调用的那个实例。例如类簇，还有一些 Cocoa 类为相等的（不可变的）对象返回同一个实例。

①Designated和Secondary初始化方法

Objective-C有指定初始化方法(designated initializer)和间接(secondary initializer)初始化方法的观念。*designated初始化方法是提供所有的参数*，*secondary 初始化方法是一个或多个*，并且提供一个或者更多的默认参数来调用designated初始化的初始化方法。

	@implementation ZOCEvent

    //designated initializer
	- (instancetype)initWithTitle:(NSString *)title
	                         date:(NSDate *)date
	                     location:(CLLocation *)location
	{
	    self = [super init];
	    if (self) {
	        _title    = title;
	        _date     = date;
	        _location = location;
	    }
	    return self;
	}

    //secondary initializer
	- (instancetype)initWithTitle:(NSString *)title
	                         date:(NSDate *)date
	{
	    return [self initWithTitle:title date:date location:nil];
	}

    //secondary initializer
	- (instancetype)initWithTitle:(NSString *)title
	{
	    return [self initWithTitle:title date:[NSDate date] location:nil];
	}

	@end