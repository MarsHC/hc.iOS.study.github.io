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

