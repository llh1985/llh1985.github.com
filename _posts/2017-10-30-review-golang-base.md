---
layout: post
title: 复习-golang-基础
categories: Other
description: 复习-golang-基础
keywords: go, glang, 基础, 复习
---

2017-10-30-review-golang-base.md
    
```
package main

import "fmt"
import "runtime"
//import "log"
import "math"

import (
	"unsafe"
	"strings"
	"io"
	"net/http"
	"log"
)

 //一行代表一个语句结束。每个语句不需要像 C 家族中的其它语言一样以分号


	//1,变量定义
	var a int = 10   //直接赋值,声明变量类型
	var b = 5	//自动判断类型
	/*  c := 10
	//使用:=省略var 该方式不能用于赋值已声明变量,否则会编译错误
	// := 只能用在方法体中,否则报错
	*/
	//a,b=b,a         //交换变量的值

	var aa = "w3cschool菜鸟教程"
	var bb string = "w3cschool.cc"
	var cc bool   //不赋值,使用默认值

	//2,多变量声明
	var  x,y,z int   //普通非全局变量
	var  d,e int = 1,2 //带类型的声明
	var d1,e1 =  123,"hello`"  //不带类型,自动判断类型
	//f,g := 123,"hello" 	//只能用在函数体中
	//x,y,z=1,2,3	//多变量赋值

	//3.全局变量声明
	var (
		x1 int
		y1 bool
	    )

	//空白标识符 _  实际上是一个只写变量，你不能得到它的值,  也被用于抛弃值
	//_, b = 5, 7  // 5被抛弃
	//val, err = Func1(var1)


	//4,局部变量必须被使用!
	//如果你声明了一个局部变量却没有在相同的代码块中使用它，同样会得到编译错误,a declared and not used


	//值类型和引用类型
	//所有像 int、float、bool 和 string 这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值
	//当使用等号 = 将一个变量的值赋值给另一个变量时，如：j = i，实际上是在内存中将 i 的值进行了拷贝,也就是产生两个变量的内存,内存中存的都是对应相同的值(而不是地址指针)

	//更复杂的数据通常会需要使用多个字，这些数据一般使用引用类型保存。^
	//一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置。 这个内存地址为称之为指针，这个指针实际上也被存在另外的某一个字中。
	//当使用赋值语句 r2 = r1 时，只有引用（地址）被复制。
	//如果 r1 的值被改变了，那么这个值的所有引用都会指向被修改后的内容，在这个例子中，r2 也会受到影响。



func main() {

	a,b=b,a		//交换变量的值
	fmt.Println(a,b)
	println(aa,bb,cc)
	x,y,z=1,2,3	//赋值
	f,g := 123,"hello"
	println(x,y,z,d,e,f,g,d1,e1);
	println(x1,y1)

	//类型转换
	var sum int = 17
	var count int = 5
	var mean float32

	mean = float32(sum)/float32(count)
	fmt.Printf("mean 的值为: %f\n",mean)

	fmt.Println("//5,常量")
	//常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。
	//定义格式:const identifier [type] = value
	//简写 const c_name1, c_name2 = value1, value2
	const LENGTH int = 10
	const WIDTH int = 5
	const a, b, c = 1, false, "str" //多重赋值
	//常量可以用len(), cap(), unsafe.Sizeof()常量计算表达式的值
	const (
			a5 = "abc"
			b5 = len(a5)
			c5 = unsafe.Sizeof(a5)
	      )

	area := LENGTH * WIDTH
	fmt.Println("面积为 : %d", area)


	fmt.Println("//6,iota  特殊常量，可以认为是一个可以被编译器修改的常量。")
	//iota在const关键字出现时将被重置为0(const内部的第一行之前)，const中每新增一行常量声明将使iota计数一次(iota可理解为const语句块中的行索引)。
	//1、iota只能在常量的表达式const()中使用。
	//2、每次 const 出现时，都会让 iota 初始化为0.
	//3.常用于定义枚举
	//4.常与[隐形重复最后一个非空表达式]/位移表达式/数量级定义结合
	//5.可以用_接收表示跳过值
	//http://www.runoob.com/go/go-constants.html
	const (
			ca = 1//iota   //0
			cb =1         //1
			cc =iota        //2
			cd = "ha"   //独立值，iota += 1
			ce          //"ha"   iota += 1
			cf = 100    //iota +=1
			cg          //100  iota +=1
			ch = iota   //7,恢复计数
			_ 	    //8被抛弃,即跳过该值
			ci          //9
	      )
	fmt.Println(ca,cb,cc,cd,ce,cf,cg,ch,ci)


	//7,条件语句
	testIf()
	testSwitch()
	testSelect()

	//8.循环语句
	testFor()

	//9,函数
	testFunc()

	//10,变量作用域

	//11,数组
	testArray()

	//12,指针
	testPointers()

	//13,结构体
	testStructures()

	//13.2 结构体方法
	testStructMethods()

	//13.5,接口
	testInterfaces()

	//14,切片(Slice)
	testSlice()

	//15,范围(Range)
	testRange()

	//16,集合(Map)
	testMap()

	//17,递归
	testRecursion()

	//18,错误处理(Error)
	testError()

	//nowPoint

	/* 这是我的第一个简单的程序 */
	fmt.Println("Hello, World!")
	/*  方法名首字母大写表示Public  首字母小写表示protected
	    当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 protected ）。*/


}
func printLog(){

	pc,file,line,ok := runtime.Caller(1)
		_,_,_ =file,line,ok
		//log.Println(pc)
		//log.Println(file)
		//log.Println(line)
		//log.Println(ok)
		f := runtime.FuncForPC(pc)
		//log.Println(f.Name())
		println("")
		fmt.Println(f.Name())
}

func testIf(){
	printLog()
		/* 定义局部变量 */
		var a int = 10

		/* 使用 if 语句判断布尔表达式 */
		if a < 20 {
			/* 如果条件为 true 则执行以下语句 */
			fmt.Printf("a 小于 20\n" )
		}
	fmt.Printf("a 的值为 : %d\n", a)
}


func testSwitch(){
	printLog()

		var grade string = "B"
		var marks int = 90

		switch marks {
			case 90: grade = "A"
			case 80: grade = "B"
			case 50,60,70 : grade = "C"
			default: grade = "D"
		}

	switch {
		case grade == "A" :
			fmt.Printf("优秀!\n" )
		case grade == "B", grade == "C" :
				fmt.Printf("良好\n" )
		case grade == "D" :
					fmt.Printf("及格\n" )
		case grade == "F":
						fmt.Printf("不及格\n" )
		default:
							fmt.Printf("差\n" );
	}
	fmt.Printf("你的等级是 %s\n", grade );

	//判断变量类型
	var x interface{}

	switch i := x.(type) {
		case nil:
			fmt.Printf(" x 的类型 :%T",i)
		case int:
				fmt.Printf("x 是 int 型")
		case float64:
					fmt.Printf("x 是 float64 型")
		case func(int) float64:
						fmt.Printf("x 是 func(int) 型")
		case bool, string:
							fmt.Printf("x 是 bool 或 string 型" )
		default:
								fmt.Printf("未知型")
	}

}

func testSelect(){
	printLog()

		println("select是Go中的一个控制结构，类似于用于通信的switch语句。每个case必须是一个通信操作，要么是发送要么是接收。")
		println("select随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。一个默认的子句应该总是可运行的。")


		println("*********************  select 用法待补充   ***********")
		//http://www.runoob.com/go/go-select-statement.html

}

func testFor(){
	printLog()
		var b int = 5
		var a int

		numbers := [6]int{1, 2, 3, 5}

	/* 普通 for 循环 */
	for a := 0; a < 5; a++ {
		fmt.Printf("a 的值为: %d\n", a)
	}

	fmt.Printf("a= %d , b= %d\n",a,b)
		//for condition { }
		for a < b {
			a++
				fmt.Printf("a 的值为: %d\n", a)
		}

	//for对 slice、map、数组、字符串等进行迭代循环
	for i,x:= range numbers {
		fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
	}

	//嵌套循环
	var i, j int
		for i=2; i < 100; i++ {
			for j=2; j <= (i/j); j++ {
				if(i%j==0) {
					break; // 如果发现因子，则不是素数
				}
			}
			if(j > (i/j)) {
				fmt.Printf("%d  是素数\n", i);
			}
		}

	/** 循环控制语句
	  break 语句	经常用于中断当前 for 循环或跳出 switch 语句
	  continue 语句	跳过当前循环的剩余语句，然后继续进行下一轮循环。
	  goto 语句	将控制转移到被标记的语句。
	 */


	/* goto语句!!!!! */
LOOP: for a < 20 {
	      if a == 15 {
		      /* 跳过迭代 */
		      a = a + 1
			      goto LOOP
	      }
	      fmt.Printf("a的值为 : %d\n", a)
		      a++
      }

      //无限循环~~~~~~~~~
      /*
	 for true  {
	 fmt.Printf("这是无限循环。\n");
	 }
       */
}

func testFunc(){
	printLog()

		var a int = 100
		var b int = 200
		var ret int

		/* 调用函数并返回最大值 */
		ret = max(a, b)
		fmt.Printf( "最大值是 : %d\n", ret )

		//函数返回多个值
		a1, b1 := swap("Mahesh", "Kumar")
		fmt.Println(a1, b1)

		/*
		   调用函数，可以通过两种方式来传递参数：
		   值传递	值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
		   引用传递	引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。
		 */

		/**  引用传递的使用
		  函数定义  func swap(x *int, y *int) {}
		  函数使用 swap(&a, &b)
		 */

		/** 函数作为值!!!!
		  Go 语言可以很灵活的创建函数，并作为值使用。*/
		getSquareRoot := func(x float64) float64 {
			return math.Sqrt(x)
		}

	//******* 函数也是值 ***   他们可以像其他值一样传递，比如，函数值可以作为函数的参数或者返回值。
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))
	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))

	/* 使用函数 */
	fmt.Println(getSquareRoot(9))	//输出3

		/** 函数闭包 闭包是匿名函数，可在动态编程中使用  */
		test_func_closures()

		//方法    方法就是一个包含了接受者的函数
		test_func_method()


}

func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func test_func_closures(){
	printLog()
	println("*********** 闭包回头还得再看 **************")

	/** 函数闭包
	  Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。
	  匿名函数的优越性在于可以直接使用函数内的变量，不必申明。
	 */
	/* nextNumber 为一个函数，函数 i 为 0 */
	nextNumber := getSequence()

	/* 调用 nextNumber 函数，i 变量自增 1 并返回 */
	fmt.Println(nextNumber())
	fmt.Println(nextNumber())
	fmt.Println(nextNumber())

	/* 创建新的函数 nextNumber1，并查看结果 */
	nextNumber1 := getSequence()
	fmt.Println(nextNumber1())
	fmt.Println(nextNumber1())


	/**
	  闭包是一个函数值，它引用了函数体之外的变量。
	  这个函数可以对这个引用的变量进行访问和赋值；
	  !!!  换句话说这个函数被“绑定”在这个变量上。  !!!
	  *******/
	//例如，函数 adder 返回一个闭包。每个返回的闭包都被绑定到其各自的 sum 变量上。
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}

	//匿名函数    ("ping")是参数传入!
	var messages chan string = make(chan string)
	go func(message string) {
		messages <- message // 存消息
	}("Ping!")
	fmt.Println(<-messages) // 取消息


}
func getSequence() func() int {
i:=0
	  return func() int {
		  i+=1
			  return i
	  }
}

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func max(num1, num2 int) int {
	/* 定义局部变量 */
	var result int

		if (num1 > num2) {
			result = num1
		} else {
			result = num2
		}
	return result
}

func swap(x, y string) (string, string) {
	return y, x
}

//使用方法
func test_func_method(){
	printLog()
		var c1 Circle
		c1.radius = 10.00
		fmt.Println("Area of Circle(c1) = ", c1.getArea())
}

/* 定义类型  ( 似乎就是class)  */
type Circle struct {
	radius float64
}
//该 method 属于 Circle 类型对象中的方法 (似乎就是定义类的方法)
func (c Circle) getArea() float64 {
	//c.radius 即为 Circle 类型对象中的属性
	return 3.14 * c.radius * c.radius
}

func testArray(){
	printLog()

		//数组是具有相同唯一类型的一组已编号且长度固定的数据项序列，这种类型可以是任意的原始类型例如整形、字符串或者自定义类型。
		//声明 var variable_name [SIZE] variable_type

		//var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
		//numbers := []int{0,1,2,3,4,5,6,7,8}   切片的定义与数组的差别只是[]里没有数字

}

func testPointers(){
	printLog()

		//Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。
		//一个指针变量可以指向任何一个值的内存地址它指向那个值的内存地址。
		//声明 var var_name *var-type   例如  var ip *int
		/*********** !!!!!!!!!!
		  可以理解为指针的内存地址为指向变量,
		  而变量的内存地址指向其真实内存地址(如果为结构体即其第一个属性的内存地址)
		 ************/

		var a int= 20   /* 声明实际变量 */
		var ip *int        /* 声明指针变量 */
		ip = &a  /* 指针变量的存储地址 */
		fmt.Printf("a 变量的地址是: %x\n", &a  )
		/* 指针变量的存储地址 */
		fmt.Printf("ip 变量储存的指针地址: %x\n", ip )
		/* 使用指针访问值 */
		fmt.Printf("*ip 变量的值: %d\n", *ip )

		//空指针
		/** 当一个指针被定义后没有分配到任何变量时，它的值为 nil。
		  nil 指针也称为空指针。
		  nil在概念上和其它语言的null、None、nil、NULL一样，都指代零值或空值。
		  一个指针变量通常缩写为 ptr。*/
		var  ptr *int
		fmt.Printf("ptr 的值为 : %x\n", ptr  )
		if(ptr == nil){
			println("ptr 是空指针")
		}

	//指针数组
	println("//指针数组")
		a1 := []int{10,100,200}
	var i int
		var ptr1 [3]*int;
	for  i = 0; i <3  ; i++ {
		ptr1[i] = &a1[i] /* 整数地址赋值给指针数组 */
	}
	for  i = 0; i < 3; i++ {
		fmt.Printf("a[%d] = %d\n", i,*ptr1[i] )
	}

	//指针作为参数
	//Go 语言允许向函数传递指针，只需要在函数定义的参数上设置为指针类型即可。
	//定义 func swap(x *int, y *int) {...}
	//调用  swap(&a, &b)

}

func testStructures(){
	printLog()

	//结构体是由一系列具有相同类型或不同类型的数据构成的数据集合。
	//比如保存图书馆的书籍记录，每本书有以下属性：Title：标题  Author：作者  Subject：学科  ID：书籍ID
	/** 定义
	  type struct_variable_type struct {
	  member definition;
	  member definition;
	  ...
	  member definition;
	  }
	 */
	/** 使用 (声明变量)
	  variable_name := structure_variable_type {value1, value2...valuen}
	 */
	/** 访问结构体成员
	  如果要访问结构体成员，需要使用点号 (.) 操作符，格式为："结构体.成员名"。
	 */

	//结构体常作为函数参数使用
	var Book1 Books        /* 声明 Book1 为 Books 类型 */
	var Book2 Books        /* 声明 Book2 为 Books 类型 */

	/* book 1 描述 */
	Book1.title = "Go 语言"
	Book1.author = "www.runoob.com"
	Book1.subject = "Go 语言教程"
	Book1.book_id = 6495407

	/* book 2 描述 */
	Book2.title = "Python 教程"
	Book2.author = "www.runoob.com"
	Book2.subject = "Python 语言教程"
	Book2.book_id = 6495700

	println("Book1:%s",&Book1)
	println("Book1:%s",&Book1.title)
	/* 打印 Book1 信息 */
	printBook(&Book1)

	/* 打印 Book2 信息 */
	printBook(&Book2)

	//结构体文法   通过字段名来赋值(与顺序无关)
	var (
		v1 = Vertex{1, 2}  // 类型为 Vertex
		v2 = Vertex{X: 1}  // Y:0 被省略
		v3 = Vertex{}      // X:0 和 Y:0
		p  = &Vertex{1, 2} // 类型为 *Vertex
	)
	fmt.Println(v1, p, v2, v3)

}
type Books struct {
	title string
		author string
		subject string
		book_id int
}
type Vertex struct {
	X, Y float64
}

func printBook( book *Books ) {
	fmt.Printf( "Book title : %s\n", book.title);
	fmt.Printf( "Book author : %s\n", book.author);
	fmt.Printf( "Book subject : %s\n", book.subject);
	fmt.Printf( "Book book_id : %d\n", book.book_id);
	println("adds:%s",&book)
	println("adds:%s",&book.title)
	println("adds:%s",&book.author)
}

func testStructMethods(){
	printLog()

	/** Go 没有类。然而，仍然可以在结构体类型上定义方法。
	方法接收者 出现在 func 关键字和方法名之间的参数中	 */
	v := &Vertex{3, 4}
	fmt.Println(v.Abs())

	/****
	你可以对包中的 任意 类型定义任意方法，而不仅仅是针对结构体。
	!!! 但是，不能对来自其他包的类型或基础类型定义方法。
	 */
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())

	/**
	参数为指针的方法    例如 *Vertex 的 Abs()
	首先,避免在每个方法调用中拷贝值（如果值类型是大的结构体的话会更有效率）。
	其次，方法可以修改接收者指向的值。
	 */

}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

type MyFloat float64
func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func testInterfaces(){
	printLog()

	/**
	  Go 语言提供了另外一种数据类型即接口，
	  它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。
	*/

	var phone Phone

	phone = new(NokiaPhone)
	phone.call()

	phone = new(IPhone)
	phone.call()

	/**	Stringers 一个普遍存在的接口是 fmt 包中定义的 Stringer。
			type Stringer interface {
				String() string
			}
		Stringer 是一个可以用字符串描述自己的类型。`fmt`包 （还有许多其他包）使用这个来进行输出。
		!!!	其实就是其他语言中的toString方法的定义接口
	 */
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)

	//Stringer接口
	//error接口

	//io.Reader接口
	/**	io 包指定了 io.Reader 接口， 它表示从数据流结尾读取。
		Go 标准库包含了这个接口的许多实现， 包括文件、网络连接、压缩、加密等等。
		io.Reader 接口有一个 Read 方法：
			func (T) Read(b []byte) (n int, err error)
		Read 用数据填充指定的字节 slice，并且返回填充的字节数和错误信息。 在遇到数据流结尾时，返回 io.EOF 错误。
	 */
	r := strings.NewReader("Hello, Reader!")
	b := make([]byte, 8)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}

	//ServeHTTP接口 (web服务器)
	/**
		包 http 通过任何实现了 http.Handler 的值来响应 HTTP 请求：
		package http
		type Handler interface {
			ServeHTTP(w ResponseWriter, r *Request)
		}
	 */
	/**
		//示例 1
		var h Hello
		err := http.ListenAndServe("localhost:4000", h)
		if err != nil {
			log.Fatal(err)
		}

		//示例2
		http.Handle("/string", String("I'm a frayed knot."))	// http://localhost:4000/string
		http.Handle("/struct", &Struct{"Hello", ":", "Gophers!"})	// http://localhost:4000/struct.
		log.Fatal(http.ListenAndServe("localhost:4000", nil))
	 */

	/** //示例3
		httpDemo()
	 */


}

//interface
type Phone interface {
	    call()
}

//type nokia
type NokiaPhone struct {}
//nokia.call()
func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

//type iphone
type IPhone struct {}
//iphone.call
func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}


type Person struct {
	Name string
	Age  int
}
func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

type Hello struct{}
func (h Hello) ServeHTTP( w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, "Hello!")
}


type String string
func (s String) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, s)
}

type Struct struct {
	Greeting string
	Punct    string
	Who      string
}
func (s Struct) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, s)
}

func httpDemo(){
	http.HandleFunc("/", sayhelloName) //设置访问的路由
	err := http.ListenAndServe(":9090", nil) //设置监听的端口
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
func sayhelloName(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()  //解析参数，默认是不会解析的
	fmt.Println(r.Form)  //这些信息是输出到服务器端的打印信息
	fmt.Println("path", r.URL.Path)
	fmt.Println("scheme", r.URL.Scheme)
	fmt.Println(r.Form["url_long"])
	for k, v := range r.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(w, "Hello astaxie!") //这个写入到w的是输出到客户端的
}


func testSlice(){
	printLog()

	/** 切片是对数组的抽象。
	  提供了一种灵活，功能强悍的内置类型切片("动态数组"),与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。
	定义: var identifier []type
	  	或者用make(): var slice1 []type = make([]type, len, capacity)   //len为长度,capacity为容量
		简写:	slice1 := make([]type, len)
		  或者用数组: s := arr[startIndex:endIndex]
		//用数组arr中索引从startIndex到endIndex的元素新建一个切片,二者皆可省略
		或者用切片: s :=make([]int,len,cap)
	例子:
		s :=[] int {1,2,3 } //[]表示是切片类型，{1,2,3}是初始化的值
		s := arr[:]    //用数组arr创建一个切片(startIndex为空表示从第一个开始,endIndex为空表示到最后一个元素为止)
	函数:
		切片是可索引的，并且可以由 len() 方法获取长度。
		切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。
		copy()
		append() 和 copy() 函数
		如果想增加切片的容量，我们需要创建一个新的更大的切片并把原分片的内容都拷贝[copy()]过来。
		像一个切片内append()一个元素,如果切片容量已满,则会自动slice增加一倍,并尾部追加该元素

	 */

	//空(nil)切片   一个切片在未初始化之前默认为 nil，长度为 0
	var numbers []int
	printSlice(numbers)
	if(numbers == nil){
		fmt.Printf("切片是空的")
	}

	numbers = make([]int,3,5)
	printSlice(numbers)   //len=3 cap=5 slice=[0 0 0]

	numbers = []int{0,1,2,3,4,5,6,7,8}
	printSlice(numbers)	//len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
	fmt.Println("numbers ==", numbers)	//numbers == [0 1 2 3 4 5 6 7 8]
	/* 打印子切片从索引1(包含) 到索引4(不包含)*/
	fmt.Println("numbers[1:4] ==", numbers[1:4])	//numbers[1:4] == [1 2 3]

	numbers1 := make([]int,0,5)
	printSlice(numbers1)	//len=0 cap=5 slice=[]

	println("append")
	numbers = make([]int,4,4)
	printSlice(numbers)	//len=4 cap=4 slice=[0 0 0 0]
	numbers = append(numbers, 2,3,4)
	printSlice(numbers)	//len=7 cap=8 slice=[0 0 0 0 2 3 4]

	numbers = []int{0,1,2,3,4,5,6,7,8}
	numbers1 = numbers[1:4]		//用切片(或数组)赋值切片,只新建指针,并申请空间和复制数据!!!
	numbers1[0] = 9		//number1指向numeber的内存一部分所以对其修改同样改变了number
	numbers1 = append(numbers1, 9,9)//!!!!同理,append时候会沿着内存向后覆盖number的数据!!!!
	printSlice(numbers)	//len=9 cap=9 slice=[0 9 2 3 9 9 6 7 8]
	printSlice(numbers1)	//len=5 cap=8 slice=[9 2 3 9 9]
}

func printSlice(x []int){
	fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}

func testRange(){
	printLog()

		/** range 关键字用于for循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。
		  在数组和切片中它返回元素的索引值，在集合中返回 key-value 对的 key 值。  */

		nums := []int{2, 3, 4}

	for i, num := range nums {	//如果不需要index可以:for _, num := range nums{}
		if num == 3 {
			fmt.Println("index:", i)
		}
	}

	//range也可以用在map的键值对上。
kvs := map[string]string{"a": "apple", "b": "banana"}
     for k, v := range kvs {
	     fmt.Printf("%s -> %s\n", k, v)
     }
     //range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
     for i, c := range "go" {
	     fmt.Println(i, c)
     }
}

func testMap(){
	printLog()

		/** Map 是一种无序的键值对的集合。
		  Map 最重要的一点是通过 key 来快速检索数据，key 类似于索引，指向数据的值。
		  Map 是一种集合，所以我们可以像迭代数组和切片那样迭代它。
		  不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 Map 是使用 hash 表来实现的。
		  声明:
		  默认方式:	var map_variable map[key_data_type]value_data_type
		  使用make函数	map_variable = make(map[key_data_type]value_data_type)
		  函数:
		  delete() 函数用于删除集合的元素, 参数为 map 和其对应的 key
		 */

		var countryCapitalMap map[string]string
		//countryCapitalMap := map[string] string {"France":"Paris","Italy":"Rome","Japan":"Tokyo","India":"New Delhi"}
		/* 创建集合 */
		countryCapitalMap = make(map[string]string)

		/* map 插入 key-value 对，各个国家对应的首都 */
		countryCapitalMap["France"] = "Paris"
		countryCapitalMap["Italy"] = "Rome"
		countryCapitalMap["Japan"] = "Tokyo"
		countryCapitalMap["India"] = "New Delhi"

		/* 删除元素 */
		delete(countryCapitalMap,"France");
	fmt.Println("Entry for France is deleted")

		/* 使用 key 输出 map 值 */
		for country := range countryCapitalMap {
			fmt.Println("Capital of",country,"is",countryCapitalMap[country])
		}

	/* 查看元素在集合中是否存在 */
	captial, ok := countryCapitalMap["United States"]
		/* 如果 ok 是 true, 则存在，否则不存在 */
		if(ok){
			fmt.Println("Capital of United States is", captial)
		}else {
			fmt.Println("Capital of United States is not present")
		}
}

func testRecursion(){
	printLog()

		/**  递归，就是在运行的过程中调用自己。*/
		var i int = 15
		//计算阶乘
		fmt.Printf("%d 的阶乘是 %d\n", i, Factorial(i))

		//打印斐波那契数列
		for i = 0; i < 10; i++ {
			fmt.Printf("%d\t", fibonaci(i))
		}
}

func Factorial(x int) (result int) {
	if x == 0 {
		result = 1;
	} else {
		result = x * Factorial(x - 1);
	}
	return;
}

func fibonaci(n int) int {
	if n < 2 {
		return n
	}
	return fibonaci(n-2) + fibonaci(n-1)
}


func testError(){
	printLog()

	/** 先凑合理解吧
	  */

	// 正常情况
	if result, errorMsg := Divide(100, 10); errorMsg == "" {
		fmt.Println("100/10 = ", result)
	}
	// 当被除数为零的时候会返回错误信息
	if _, errorMsg := Divide(100, 0); errorMsg != "" {
		fmt.Println("errorMsg is: ", errorMsg)
	}

	/**		与 fmt.Stringer 类似， error 类型是一个内建接口：
		type error interface {
			Error() string
		}
		（与 fmt.Stringer 类似，fmt 包在输出时也会试图匹配 error。）


		通常函数会返回一个 error 值，调用的它的代码应当判断这个错误是否等于 nil， 来进行错误处理。
		i, err := strconv.Atoi("42")
		if err != nil {
			fmt.Printf("couldn't convert number: %v\n", err)
			return
		}
		fmt.Println("Converted integer:", i)
		error 为 nil 时表示成功；非 nil 的 error 表示错误。
	 */

}

// 定义一个 DivideError 结构
type DivideError struct {
	dividee int
	divider int
}

// 实现 	`error` 接口
func (de *DivideError) Error() string {
strFormat := `
	   Cannot proceed, the divider is zero.
	   dividee: %d
	   divider: 0
	   `
	return fmt.Sprintf(strFormat, de.dividee)
}

// 定义 `int` 类型除法运算的函数
func Divide(varDividee int, varDivider int) (result int, errorMsg string) {
	if varDivider == 0 {
dData := DivideError{
dividee: varDividee,
		 divider: varDivider,
       }
       errorMsg = dData.Error()
	       return
	} else {
		return varDividee / varDivider, ""
	}

}


```
  
  
