---
layout: default
title: Learning Swift(一)
---
{{ page.title }}
=============
{{ page.date | date: "%Y-%m-%d" }}

在Xcode中新建一个Command Line Tool项目，不使用Playground。

一、基础

1.不需要导入某个标准库，不需要放在main函数里面，不需要在结尾使用分号，这行代码就可以在控制台顺利输出“Hello, World!”。

	println("Hello, World!")

2.Swift可以嵌套注释：

	/*
	    /*这是一个多行注释
	    这是一个多行注释*/
	    这是一个多行注释
	*/

这种嵌套注释在OC里面是要报错的。

3.使用var来声明一个变量，使用let来声明一个变量。

	var count: Int = 0
	let str: String

使用变量之前必须初始化这个变量，可以在声明的同时设置其初始值，或者随后设置，但必须在读取这个变量之前设置。

也可以这样：

	var count = 0

等价于var count: Int = 0，因为Swift会根据0自动推断出count的类型为Int。最好还是写出类型比较好，避免你期望的类型和编译器推断出的类型不同情况发生。

4.Swift中所有类型都是由三种数据结构中的一种实现的，这三种数据结构分别是enum、struct和class，其中枚举和结构体的传递是值传递，也就是副本的改变不会影响原始对象，而类的传递是引用传递，修改了副本，原始变量也会被修改，修改了原始变量，副本也会改变，因为本质上它们指向的是同一块内存空间。

5.Swift是强类型的，任何一种类型都不能*隐式地*转换为另一种类型，不能将一个Int和Float值进行相加操作。

	var one: Int = 10
	var two: Float = 2.5
	var result : Int = one + two

编译器会报Binary operator '+' cannot be applied to operands of type 'Int' and 'Float'的错，OC里面会把float隐式转换为int，最后的结果是12。

	int one = 10;
    float two = 2.5;
    //result is 12.
    int reslut = one + two;

简单来说，以前这种可能引起意想不到后果的操作，Swift会直接给你报错，这样可以避免产生一些bug。

6.字符串插值

	var str = "World"
	println("Hello, \(str)!")


7.if

与OC中if有3点区别：

*   检测表达式可以不需要括号。
*   条件执行语句必须加括号，即使只有一行代码。
*   检测表达式的结果是true或false，不能再用0代表false，非0代表true。

8.switch

与OC中switch的2点区别：

*   必须要覆盖每一种可能出现的情况，可以用default覆盖其它所有情况。
*   不需要break。

9. 0..<10表示[0, 10)，1...10表示[1, 10]。

10.Swift可以给循环语句添加一个Label，然后在内部循环中指定希望break或continue到哪个循环。

	outer: for var i = 0; i < 10; i++ {
	    println("i = \(i)")
	    for j in 0 ..< 10 {
	        println("j = \(j)")
	        
	        if j == 9 {
	            break outer
	        }
	    }
	}

result:

	i = 0
	j = 0
	j = 1
	j = 2
	j = 3
	j = 4
	j = 5
	j = 6
	j = 7
	j = 8
	j = 9

11.Int

*   Int在32位设备中表现为32位整型，在64位设备中表现为64位整型。
*   当需要特定长度整型时，可以使用Int8、Int16、Int32、Int64，对应的无符号版本为UInt8、UInt16、UInt32、UInt64。
*   Swift中不允许在代码中混合使用无符号值和有符号值，不允许不同长度的类型混合使用，也不允许将某种类型赋值给另外一种类型。
*   Apple建议使用Int类型，除非需要使用特定长度类型或必须使用无符号值。

12.Float和Double

*   Float是32位，Double是64位，与设备架构无关。
*   编译器推断小数的类型总是Double，而不是Float，所以如果不需要64位的高精度，需要显示声明为Float。
