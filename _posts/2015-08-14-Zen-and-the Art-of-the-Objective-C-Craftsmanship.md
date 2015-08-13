---
layout: default
title: 禅与Objective-C编程艺术（Zen and the Art of the Objective-C Craftsmanship）学习总结
---
{{ page.title }}
=============
{{ page.date | date: "%Y-%m-%d" }}

https://github.com/oa414/objc-zen-book-cn#%E5%B0%A4%E8%BE%BE%E8%A1%A8%E8%BE%BE%E5%BC%8F

1.不要使用尤达表达式

	if ([@42 isEqual:myValue]) { ... Never do this.

	if ([myValue isEqual:@42]) { ...  Do like this.

2.nil 和 BOOL 检查

不推荐:

	if (someObject == YES) { ... // Wrong.
	if (myRawValue == YES) { ... // Never do this.
	if ([someObject boolValue] == NO) { ...

推荐:

	if (someObject) { ...
	if (![someObject boolValue]) { ...
	if (!someObject) { ...

3.重要的方法不要嵌套在分支里面，使用多个return可以避免增加循环的复杂度，并提高代码的可读性。

不推荐:

	- (void)someMethod {
        if ([someOther boolValue]) {
            //Do something important
        }
	}

推荐:

	- (void)someMethod {
        if (![someOther boolValue]) {
          return;
        }

        //Do something important
	}

4.当你有一个复杂的 if 子句的时候，你应该把它们提取出来赋给一个 BOOL 变量，这样可以让逻辑更清楚，而且让每个子句的意义体现出来。

	BOOL nameContainsSwift  = [sessionName containsString:@"Swift"];
	BOOL isCurrentYear      = [sessionDateCompontents year] == 2014;
	BOOL isSwiftSession     = nameContainsSwift && isCurrentYear;

	if (isSwiftSession) {
	    // Do something very cool
	}

5.三元运算符

三元运算符 ? 应该只用在它能让代码更加清楚的地方。 一个条件语句的所有的变量应该是已经被求值了的。类似 if 语句，计算多个条件子句通常会让语句更加难以理解。或者可以把它们重构到实例变量里面。

推荐:

	result = a > b ? x : y;

不推荐:

	result = a > b ? x = c > d ? c : d : y;

当三元运算符的第二个参数（if 分支）返回和条件语句中已经检查的对象一样的对象的时候，下面的表达方式更灵巧：

推荐:

	result = object ? : [self createObject];

不推荐:

	result = object ? object : [self createObject];

6.错误处理
有些方法通通过参数返回error的引用，使用这样的方法时应当检查方法的返回值，而非error的引用。

推荐:

	NSError *error = nil;
	if (![self trySomethingWithError:&error]) {
        // Handle Error
    }

7.Case语句

① case 语句里面是不必要的。但是当一个 case 包含了多行语句的时候，需要加上括号。

	switch (condition) {
    case 1:
        // ...
        break;
    case 2: {
        // ...
        // Multi-line example using braces
        break;
       }
    case 3:
        // ...
        break;
    default: 
        // ...
        break;
	}

②使用fall-through在不同的case里面执行同一段代码。一个fall-through是指移除case语句的“break”然后让下面的 case 继续执行。

	switch (condition) {
    case 1:
    case 2:
        // code executed for values 1 and 2
        break;
    default: 
        // ...
        break;
	}

③当在switch语句里面使用一个可枚举的变量的时候，default 是不必要的。
	switch (menuType) {
    case ZOCEnumNone:
        // ...
        break;
    case ZOCEnumValue1:
        // ...
        break;
    case ZOCEnumValue2:
        // ...
        break;
	}

8.枚举类型

像这样定义enum：

	typedef NS_ENUM(NSUInteger, ZOCMachineState) {
	    ZOCMachineStateNone,
	    ZOCMachineStateIdle,
	    ZOCMachineStateRunning,
	    ZOCMachineStatePaused
	};

9.命名

推荐使用长的、描述性的方法和变量名。

推荐:

	UIButton *settingsButton;

不推荐:

	UIButton *setBut;

10.常量

常量应该以驼峰法命名，并以相关类名作为前缀。

推荐:

	static const NSTimeInterval ZOCSignInViewControllerFadeOutAnimationDuration = 0.4;

不推荐:

	static const NSTimeInterval fadeOutTime = 0.4;

常量应该用 static 声明为静态常量，而不要用 #define，除非它明确的作为一个宏来使用。

推荐:

	static NSString * const ZOCCacheControllerDidClearCacheNotification = @"ZOCCacheControllerDidClearCacheNotification";
    static const CGFloat ZOCImageThumbnailHeight = 50.0f;

不推荐:
	
	#define CompanyName @"Apple Inc."
    #define magicNumber 42

常量应该在头文件中以这样的形式暴露给外部：

	extern NSString *const ZOCCacheControllerDidClearCacheNotification;

11.方法

方法名与方法类型 (-/+ 符号)之间应该以空格间隔。方法段之间也应该以空格间隔（以符合 Apple 风格）。参数前应该总是有一个描述性的关键词。

尽可能少用 "and" 这个词。它不应该用来阐明有多个参数，比如下面的 initWithWidth:height: 这个例子：

推荐:

	- (void)setExampleText:(NSString *)text image:(UIImage *)image;
    - (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
    - (id)viewWithTag:(NSInteger)tag;
    - (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;

不推荐:
	
	- (void)setT:(NSString *)text i:(UIImage *)image;
	- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
	- (id)taggedView:(NSInteger)tag;
	- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
	- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
