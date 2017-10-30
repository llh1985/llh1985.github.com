---
layout: post
title: 复习-golang-高级
categories: Other
description: 复习-golang-高级
keywords: go, glang, 高级, 复习
---

2017-10-30-review-golang-senior.md
    
```
package main

import (
	"./tree"
	"fmt"
	"time"
	"sync"
	"net/http"
	"log"
	"strings"
	"html/template"
	"database/sql"
	"io"
	"net/url"
	"encoding/xml"
	"os"
	"io/ioutil"
	"encoding/xml"
	"encoding/json"
	"regexp"
	"strconv"
	"net"
	"github.com/julienschmidt/httprouter"
	"net/rpc"
	"errors"
	"net/rpc/jsonrpc"
	"crypto/sha256"
	"crypto/sha1"
	"crypto/md5"
	"golang.org/x/crypto/scrypt"
	"crypto/aes"
	"crypto/cipher"
	"github.com/golang/appengine/datastore"
	"github.com/golang/appengine"
)

func main() {

	//1.defer关键字
	testDefer()

	//10.go关键字(goroutine)
	testGoroutine()

	//11.chan关键字
	testChan()

	//12.sync.Mutex 同步处理
	testSyncMutex()


	//20.http  web服务
	testHttp()

	//25.database  数据库服务
	testDatabase()

	//30,session and cookie  会话跟踪
	testSessionAndCookie()

	//40.xml处理    Unmarshal  Marshal
	testXML()

	//45.json处理    Unmarshal  Marshal
	testJSON()

	//50.regexp处理   正则
	testRegexp()

	//60.template使用   模板
	testTemplate()

	//65.file操作
	testFile()

	//70.string   字符串处理
	testStrings()

	//80.Socket编程
	testSocket()

	//85.webSocket通信
	testWebsocket()

	//90.csrf攻击处理
	testCsrf()

	//95. InputVerify 输入过滤
	testInputVerify()

	//100.xss攻击处理
	testXss()

	//105.SQL Injection  sql注入
	testSQLInjection()

	//110.密码保存
	testSavePassword()

	//115.crypto  加密解密/对称加密
	testCrypto()


	//120 i18n  国际化
	testI18n()

	//130.debug 错误处理和调试,测试
	testDebug()

	//135.test case 测试用例
	testCase()

	//140.log  应用日志
	//nowpoint
}


func testDefer(){
	/******  1. defer ******/
	println("/********* 1.  defer 关键字 ********/")
	println ("defer关键字用于资源的释放,会在函数返回之前进行调用,进行例如关闭文件资源等动作")
	/**  //示例
	f,err := os.Open(filename)
	if err != nil {
	    panic(err)
	    }
	    defer f.Close()
	  */
	println("如果有多个defer表达式，调用顺序类似于栈，越后面的defer表达式越先被调用。")
	println("“如果对defer的了解不够深入，使用起来可能会踩到一些坑，尤其是跟带命名的返回参数一起使用时")	//见<<深入解析go>>"defer使用时的坑"章节
	println("不建议如此使用defer")


	defer fmt.Println("world")
	fmt.Println("hello")	//这句hello先于world被输出

}

func testGoroutine(){

	/********  10. go关键字  goroutine   ******/
	println("/********* 10.go 关键字 ( goroutine )  ********/")
	println("goroutine 是由 Go 运行时环境管理的轻量级线程。")

	go say("world",1)
	say("hello",2)
}


func say(s string,t int) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Duration(t) * time.Millisecond)
		fmt.Println(s)
	}
}

func testChan(){

	/*******   11. chan 关键字 ( channel ) *********/
	println("/*******   11. chan 关键字 ( channel ) *********/")
	println(`channel 是有类型的管道，
		和 map 与 slice 一样，channel 使用前必须创建
			ch := make(chan int)
		可以用 channel 操作符 <- 对其发送或者接收值
			ch <- v    // 将 v 送入 channel ch。
			v := <-ch  // 从 ch 接收，并且赋值给 v。
		（“箭头”就是数据流的方向。）
		默认情况下，在另一端准备好之前，发送和接收都会阻塞。
		这使得 goroutine 可以在没有明确的锁或竞态变量的情况下进行同步。
		`)

	a := []int{7, 2, 8, -9, 4, 0}
	c := make(chan int)
	go sum(a[:len(a)/2], c)
	go sum(a[len(a)/2:], c)
	x, y := <-c, <-c // 从 c 中获取
	fmt.Println(x, y, x+y)

	println(`/* 缓冲channel
		channel 可以是 带缓冲的。为 make 提供第二个参数作为缓冲长度来初始化一个缓冲 channel：
			ch := make(chan int, 100)
		向带缓冲的 channel 发送数据的时候，只有在缓冲区满的时候才会阻塞。
		而当缓冲区为空的时候接收操作会阻塞。
		`)
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	fmt.Println(<-ch)
	fmt.Println(<-ch)

	println(`/** close()
		发送者可以 close 一个 channel 来表示再没有值会被发送了。
		接收者可以通过赋值语句的第二个参数来测试 channel 是否被关闭：
		当没有值可以接收并且 channel 已经被关闭，那么第二个参数会返回false

	 	** range ****
		循环 'for i := range c' 会不断从 channel 接收值，直到它被关闭。
		*注意：* 只有发送者才能关闭 channel，而不是接收者。向一个已经关闭的 channel 发送数据会引起 panic。
		*还要注意：* channel 与文件不同；通常情况下无需关闭它们。只有在需要告诉接收者没有更多的数据的时候才有必要进行关闭，
		例如中断一个 range。
		`)
	c = make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}

	println(`
		/****  注意!!
			!!!!!如果 range 一个channel 而这个通道没有写close() !! 那么将死锁报错!!! 所以一定记得close!!!!
			换句话说: range不等到信道关闭是不会结束读取的。
			或者说:   如果 缓冲信道干涸了，那么range就会阻塞当前goroutine, 所以死锁咯。
		 */
		`)
	count := 8
	quit := make(chan int) // 无缓冲
	for i := 0; i < count; i++ {
		go foo(i,quit)
	}
	for i := 0; i < count; i++ {
		println("qqqq",<- quit)
	}



	//练习
	chanExample()


}



func sum(a []int, c chan int) {
	sum := 0
	for _, v := range a {
	     sum += v
	}
	c <- sum // 将和送入 c
}

func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func foo(id int,quit chan int) {
	fmt.Println("tttt",id)
	quit <- 0 // ok, finished
}

func chanExample()  {
	/**
	  可以用多种不同的二叉树的叶子节点存储相同的数列值。例如，这里有两个二叉树保存了序列 1，1，2，3，5，8，13。

	  用于检查两个二叉树是否存储了相同的序列的函数在多数语言中都是相当复杂的。这里将使用 Go 的并发和 channel 来编写一个简单的解法。

	  这个例子使用了 tree 包，定义了类型：

	  type Tree struct {
	  Left *Tree
	  Value int
	  Right *Tree }
	  1. 实现 Walk 函数。
	  2. 测试 Walk 函数。

	  函数 tree.New(k) 构造了一个随机结构的二叉树，保存了值 k，2k，3k，…，10k。 创建一个新的 channel ch
	  并且对其进行步进：

	  go Walk(tree.New(1), ch) 然后从 channel 中读取并且打印 10 个值。应当是值 1，2，3，…，10。
	  3. 用 Walk 实现 Same 函数来检测是否 t1 和 t2 存储了相同的值。
	  4. 测试 Same 函数。

	  Same(tree.New(1), tree.New(1)) 应当返回 true，而 Same(tree.New(1),
	  tree.New(2)) 应当返回 false。
	 */

	//打印tree.New(1)的值
	var ch = make(chan int)
	go Walk(tree.New(1),ch)
	for v:= range ch {
		fmt.Println(v)
	}

	//比较两个tree
	println(Same(tree.New(1),tree.New(1)))
	println(Same(tree.New(1),tree.New(2)))

}

// Walk 步进 tree t 将所有的值从 tree 发送到 channel ch。
func Walk(t *tree.Tree, ch chan int){
	sendValue(t,ch)
	close(ch)
}

//  递归向channel传值
func sendValue(t *tree.Tree, ch chan int){
	if t != nil {
		sendValue(t.Left, ch)
		ch <- t.Value
		sendValue(t.Right, ch)
	}
}

// Same 检测树 t1 和 t2 是否含有相同的值。
func Same(t1, t2 *tree.Tree) bool{
ch1 := make(chan int)
	     ch2 := make(chan int)
	     go Walk(t1, ch1)
	     go Walk(t2, ch2)
	     for i := range ch1{
		     if i != <-ch2 {
			     return false
		     }
	     }
     return true
}

func testSyncMutex(){

	/*******   12. sync.Mutex 同步处理  *********/
	println("/*******   12. sync.Mutex 同步处理  *********/")
	println(` channel 用来在各个 goroutine 间进行通信是非常合适
		如果我们只是想保证在每个时刻，只有一个 goroutine 能访问一个共享的变量从而避免冲突？
		这里涉及的概念叫做 互斥，通常使用 _互斥锁_(mutex)_来提供这个限制。

		Go 标准库中提供了 sync.Mutex 类型及其两个方法：

		Lock
		Unlock
		我们可以通过在代码前调用 Lock 方法，在代码后调用 Unlock 方法来保证一段代码的互斥执行。 参见 Inc 方法。

		我们也可以用 defer 语句来保证互斥锁一定会被解锁。参见 Value 方法`)


   c := SafeCounter{v: make(map[string]int)}
   for i := 0; i < 1000; i++ {
	   go c.Inc("somekey")
   }

   time.Sleep(time.Second)
   fmt.Println(c.Value("somekey"))
}

// SafeCounter 的并发使用是安全的。
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc 增加给定 key 的计数器的值。
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	c.v[key]++
	c.mux.Unlock()
}

// Value 返回给定 key 的计数器的当前值。
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	defer c.mux.Unlock()
	return c.v[key]
}



func testHttp(){
	/*******   20. http  web服务  *********/
	println("/*******   20. http  web服务  *********/")


	//简单demo
	//httpDemo()


}


func httpDemo(){
	http.HandleFunc("/", sayhelloName)       //设置访问的路由
	http.HandleFunc("/login", login)         //设置访问的路由
	err := http.ListenAndServe(":9090", nil) //设置监听的端口
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
func login(w http.ResponseWriter, r *http.Request) {
	fmt.Println("method:", r.Method) //获取请求的方法
	if r.Method == "GET" {
		t, _ := template.ParseFiles("login.gtpl")
		log.Println(t.Execute(w, nil))
	} else {
		//请求的是登陆数据，那么执行登陆的逻辑判断
		fmt.Println("username:", r.Form["username"])
		fmt.Println("password:", r.Form["password"])
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

func testDatabase(){

	/*******   25. database  数据库服务  *********/
	println("/*******   25. database  数据库服务  *********/")

	println("go语言没有提供数据库驱动,但提供了数据库标准接口")
	println(`数据库接口的导入 import "database/sql"`)

	println(`Go中支持MySQL的驱动目前比较多，有如下几种，有些是支持database/sql标准，而有些是采用了自己的实现接口,常用的有如下几种:
				https://github.com/go-sql-driver/mysql 支持database/sql，全部采用go写。
				https://github.com/ziutek/mymysql 支持database/sql，也支持自定义的接口，全部采用go写。
				https://github.com/Philio/GoMySQL 不支持database/sql，自定义接口，全部采用go写。`)

	mysqlDemo()

	ormDemo()

	redisDemo()

	//mongoDB等
	//https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/05.6.md


}

func mysqlDemo(){

	db, err := sql.Open("mysql", "ffan_idms:ffan_idms@tcp(10.209.44.14:10044)/ffan_idms?charset=utf8")
	checkErr(err)
	fmt.Println(db)


	/*
		//插入数据
		stmt, err := db.Prepare("INSERT userinfo SET username=?,departname=?,created=?")
		checkErr(err)

		res, err := stmt.Exec("astaxie", "研发部门", "2012-12-09")
		checkErr(err)

		id, err := res.LastInsertId()
		checkErr(err)

		fmt.Println(id)
		//更新数据
		stmt, err = db.Prepare("update userinfo set username=? where uid=?")
		checkErr(err)

		res, err = stmt.Exec("astaxieupdate", id)
		checkErr(err)

		affect, err := res.RowsAffected()
		checkErr(err)

		fmt.Println(affect)*/

	//查询数据
	rows, err := db.Query("SELECT id, username, email, nickname FROM user")
	checkErr(err)

	for rows.Next() {
		var id int
		var username string
		var email string
		var nickname string
		err = rows.Scan(&id, &username, &email, &nickname)
		checkErr(err)
		fmt.Println(id)
		fmt.Println(username)
		fmt.Println(email)
		fmt.Println(nickname)
	}
	/*
		//删除数据
		stmt, err = db.Prepare("delete from userinfo where uid=?")
		checkErr(err)

		res, err = stmt.Exec(id)
		checkErr(err)

		affect, err = res.RowsAffected()
		checkErr(err)

		fmt.Println(affect)

		db.Close()*/

}

func checkErr(err error) {
	if err != nil {
		println(err)
		panic(err)
	}
}

func ormDemo()  {
	//see https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/05.5.md
}

func redisDemo()  {
	/**
		Go目前支持redis的驱动有如下

		https://github.com/garyburd/redigo (推荐)
		https://github.com/go-redis/redis
		https://github.com/hoisie/redis
		https://github.com/alphazero/Go-Redis
		https://github.com/simonz05/godis
	 */

	//see https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/05.6.md

}



//30,session and cookie  会话跟踪
func testSessionAndCookie(){

	println(
		`Go语言中通过net/http包中的SetCookie来设置：
		http.SetCookie(w ResponseWriter, cookie *Cookie)
		w表示需要写入的response，cookie是一个struct，让我们来看一下cookie对象是怎么样的

		type Cookie struct {
			Name       string
			Value      string
			Path       string
			Domain     string
			Expires    time.Time
			RawExpires string

		// MaxAge=0 means no 'Max-Age' attribute specified.
		// MaxAge<0 means delete cookie now, equivalently 'Max-Age: 0'
		// MaxAge>0 means Max-Age attribute present and given in seconds
			MaxAge   int
			Secure   bool
			HttpOnly bool
			Raw      string
			Unparsed []string // Raw text of unparsed attribute-value pairs
		}
	`)

	/*
	//http.SetCookie(w ResponseWriter, cookie *Cookie)
	expiration := time.Now()
	expiration = expiration.AddDate(1, 0, 0)
	cookie := http.Cookie{Name: "username", Value: "astaxie", Expires: expiration}
	http.SetCookie(w, &cookie)
	*/

	/*
	//Go读取cookie
	//上面的例子演示了如何设置cookie数据，我们这里来演示一下如何读取cookie
		cookie, _ := r.Cookie("username")
		fmt.Fprint(w, cookie)
	////还有另外一种读取方式
		for _, cookie := range r.Cookies() {
			fmt.Fprint(w, cookie.Name)
		}
	//可以看到通过request获取cookie非常方便。
	 */



}

//40.xml处理    Unmarshal  Marshal
func testXML()  {
	println(`GO语言通过xml包的Unmarshal函数来解析XML文件
	func Unmarshal(data []byte, v interface{}) error
	解析规则:https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/07.1.md
	`)
	UnmarshalDemo()

	println(`xml包中提供了Marshal和MarshalIndent两个函数，来生产XML文件
		func Marshal(v interface{}) ([]byte, error)
		func MarshalIndent(v interface{}, prefix, indent string) ([]byte, error)
		这两个函数主要的区别是第二个函数会增加前缀和缩进
		两个函数第一个参数是用来生成XML的结构定义类型数据，都是返回生成的XML数据流。
	`)
	MarshalIndentDemo()

}

func UnmarshalDemo()  {
	file, err := os.Open("servers.xml") // For read access.
	if err != nil {
		fmt.Printf("error: %v", err)
		return
	}
	defer file.Close()
	data, err := ioutil.ReadAll(file)
	if err != nil {
		fmt.Printf("error: %v", err)
		return
	}
	v := Recurlyservers{}
	err = xml.Unmarshal(data, &v)
	if err != nil {
		fmt.Printf("error: %v", err)
		return
	}

	fmt.Println(v)
}

type Recurlyservers struct {
	XMLName     xml.Name `xml:"servers"`
	Version     string   `xml:"version,attr"`
	Svs         []serverNode `xml:"server"`
	Description string   `xml:",innerxml"`
}
type serverNode struct {
	XMLName    xml.Name `xml:"server"`
	ServerName string   `xml:"serverName"`
	ServerIP   string   `xml:"serverIP"`
}

func MarshalIndentDemo()  {

	v := &Servers{Version: "1"}
	v.Svs = append(v.Svs, server{"Shanghai_VPN", "127.0.0.1"})
	v.Svs = append(v.Svs, server{"Beijing_VPN", "127.0.0.2"})
	output, err := xml.MarshalIndent(v, "  ", "    ")
	if err != nil {
		fmt.Printf("error: %v\n", err)
	}
	os.Stdout.Write([]byte(xml.Header))

	os.Stdout.Write(output)
}

type Servers struct {
	XMLName xml.Name `xml:"servers"`
	Version string   `xml:"version,attr"`
	Svs     []server `xml:"server"`
}

type server struct {
	ServerName string `xml:"serverName"`
	ServerIP   string `xml:"serverIP"`
}

func testJSON()  {

	//https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/07.2.md
	println(`Go的JSON包中有如下函数
	func Unmarshal(data []byte, v interface{}) error
	func Marshal(v interface{}) ([]byte, error)
	`)

	var s1 ServersliceJson
	str := `{"servers":[{"serverName":"Shanghai_VPN","serverIP":"127.0.0.1"},{"serverName":"Beijing_VPN","serverIP":"127.0.0.2"}]}`
	json.Unmarshal([]byte(str), &s1)
	fmt.Println(s1)

	var s ServersliceJson
	s.Servers = append(s.Servers, ServerJson{ServerName: "Shanghai_VPN", ServerIP: "127.0.0.1"})
	s.Servers = append(s.Servers, ServerJson{ServerName: "Beijing_VPN", ServerIP: "127.0.0.2"})
	b, err := json.Marshal(s)
	if err != nil {
		fmt.Println("json err:", err)
	}
	fmt.Println(string(b))

}

type ServerJson struct {
	ServerName string
	ServerIP   string
}
type ServersliceJson struct {
	Servers []ServerJson
}
/* //上边的struct生成的json字段名会完全按照属性名,也就是是首字母大写的,如果想生成小写的属性名可以通过struct tag定义来实现:
type Server struct {
	ServerName string `json:"serverName"`
	ServerIP   string `json:"serverIP"`
}
type Serverslice struct {
	Servers []Server `json:"servers"`
}
 */

func testRegexp()  {
	println(`!!! 通过正则判断是否匹配!
	Go语言的regexp包中含有三个函数用来判断是否匹配，如果匹配返回true，否则返回false
	func Match(pattern string, b []byte) (matched bool, error error)
	func MatchReader(pattern string, r io.RuneReader) (matched bool, error error)
	func MatchString(pattern string, s string) (matched bool, error error)
	上面的三个函数实现了同一个功能，就是判断pattern是否和输入源匹配，匹配的话就返回true，如果解析正则出错则返回error。
	三个函数的输入源分别是byte slice、RuneReader和string。
	`)
	if m, _ := regexp.MatchString("^[0-9]+$", "1234"); m {
		fmt.Println("数字")
	} else {
		fmt.Println("不是数字")
	}

	if IsIP("192.168.1.1") {
		fmt.Println("ip")
	} else {
		fmt.Println("不是ip")
	}

	println(`!!! 正则表达式复杂模式   通过正则表达式获取内容
	Match模式只能用来对字符串的判断，而无法截取字符串的一部分、过滤字符串、或者提取出符合条件的一批字符串。
	如果想要满足这些需求，那就需要使用正则表达式的复杂模式。

	使用复杂的正则首先是Compile，
	它会解析正则表达式是否合法，如果正确，那么就会返回一个Regexp，
	然后就可以利用返回的Regexp在任意的字符串上面执行需要的操作。
	解析正则表达式的几个方法：
	func Compile(expr string) (*Regexp, error)		//只采用最左方式搜索
	func CompilePOSIX(expr string) (*Regexp, error)		//使用最左最长方式搜索
	func MustCompile(str string) *Regexp			//不满足正确的语法则直接panic
	func MustCompilePOSIX(str string) *Regexp		//不满足正确的语法则直接panic

	Regexp提供这些方法来辅助我们操作字符串，
	首先我们来看下面这些用来搜索的函数：
		func (re *Regexp) Find(b []byte) []byte
		func (re *Regexp) FindAll(b []byte, n int) [][]byte
		func (re *Regexp) FindAllIndex(b []byte, n int) [][]int
		func (re *Regexp) FindAllString(s string, n int) []string
		func (re *Regexp) FindAllStringIndex(s string, n int) [][]int
		func (re *Regexp) FindAllStringSubmatch(s string, n int) [][]string
		func (re *Regexp) FindAllStringSubmatchIndex(s string, n int) [][]int
		func (re *Regexp) FindAllSubmatch(b []byte, n int) [][][]byte
		func (re *Regexp) FindAllSubmatchIndex(b []byte, n int) [][]int
		func (re *Regexp) FindIndex(b []byte) (loc []int)
		func (re *Regexp) FindReaderIndex(r io.RuneReader) (loc []int)
		func (re *Regexp) FindReaderSubmatchIndex(r io.RuneReader) []int
		func (re *Regexp) FindString(s string) string
		func (re *Regexp) FindStringIndex(s string) (loc []int)
		func (re *Regexp) FindStringSubmatch(s string) []string
		func (re *Regexp) FindStringSubmatchIndex(s string) []int
		func (re *Regexp) FindSubmatch(b []byte) [][]byte
		func (re *Regexp) FindSubmatchIndex(b []byte) []int
	上面这18个函数我们根据输入源(byte slice、string和io.RuneReader)不同还可以继续简化成如下几个，其他的只是输入源不一样，其他功能基本是一样的：
		func (re *Regexp) Find(b []byte) []byte
		func (re *Regexp) FindAll(b []byte, n int) [][]byte
		func (re *Regexp) FindAllIndex(b []byte, n int) [][]int
		func (re *Regexp) FindAllSubmatch(b []byte, n int) [][][]byte
		func (re *Regexp) FindAllSubmatchIndex(b []byte, n int) [][]int
		func (re *Regexp) FindIndex(b []byte) (loc []int)
		func (re *Regexp) FindSubmatch(b []byte) [][]byte
		func (re *Regexp) FindSubmatchIndex(b []byte) []int

	`)
	RegexpFindDemo()

	println(`!!! 正则表达式替换函数
		func (re *Regexp) ReplaceAll(src, repl []byte) []byte
		func (re *Regexp) ReplaceAllFunc(src []byte, repl func([]byte) []byte) []byte
		func (re *Regexp) ReplaceAllLiteral(src, repl []byte) []byte
		func (re *Regexp) ReplaceAllLiteralString(src, repl string) string
		func (re *Regexp) ReplaceAllString(src, repl string) string
		func (re *Regexp) ReplaceAllStringFunc(src string, repl func(string) string) string
	`)
	CrawlerDemo()

	println(`!!! Expand方法
	`)
	src := []byte(`
		call hello alice
		hello bob
		call hello eve
	`)
	pat := regexp.MustCompile(`(?m)(call)\s+(?P<cmd>\w+)\s+(?P<arg>.+)\s*$`)
	res := []byte{}
	for _, s := range pat.FindAllSubmatchIndex(src, -1) {
		res = pat.Expand(res, []byte("$cmd('$arg')\n"), src, s)
	}
	fmt.Println(string(res))


}

func IsIP(ip string) (b bool) {
	if m, _ := regexp.MatchString("^[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}$", ip); !m {
		return false
	}
	return true
}

func CrawlerDemo()  {
	resp, err := http.Get("http://www.baidu.com")
	if err != nil {
		fmt.Println("http get error.")
	}
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Println("http read error")
		return
	}

	src := string(body)

	//将HTML标签全转换成小写
	re, _ := regexp.Compile("\\<[\\S\\s]+?\\>")
	src = re.ReplaceAllStringFunc(src, strings.ToLower)

	//去除STYLE
	re, _ = regexp.Compile("\\<style[\\S\\s]+?\\</style\\>")
	src = re.ReplaceAllString(src, "")

	//去除SCRIPT
	re, _ = regexp.Compile("\\<script[\\S\\s]+?\\</script\\>")
	src = re.ReplaceAllString(src, "")

	//去除所有尖括号内的HTML代码，并换成换行符
	re, _ = regexp.Compile("\\<[\\S\\s]+?\\>")
	src = re.ReplaceAllString(src, "\n")

	//去除连续的换行符
	re, _ = regexp.Compile("\\s{2,}")
	src = re.ReplaceAllString(src, "\n")

	fmt.Println(strings.TrimSpace(src))
}

func RegexpFindDemo()  {
	a := "I am learning Go language"

	re, _ := regexp.Compile("[a-z]{2,4}")

	//查找符合正则的第一个
	one := re.Find([]byte(a))
	fmt.Println("Find:", string(one))

	//查找符合正则的所有slice,n小于0表示返回全部符合的字符串，不然就是返回指定的长度
	all := re.FindAll([]byte(a), -1)
	fmt.Println("FindAll", all)

	//查找符合条件的index位置,开始位置和结束位置
	index := re.FindIndex([]byte(a))
	fmt.Println("FindIndex", index)

	//查找符合条件的所有的index位置，n同上
	allindex := re.FindAllIndex([]byte(a), -1)
	fmt.Println("FindAllIndex", allindex)

	re2, _ := regexp.Compile("am(.*)lang(.*)")

	//查找Submatch,返回数组，第一个元素是匹配的全部元素，第二个元素是第一个()里面的，第三个是第二个()里面的
	//下面的输出第一个元素是"am learning Go language"
	//第二个元素是" learning Go "，注意包含空格的输出
	//第三个元素是"uage"
	submatch := re2.FindSubmatch([]byte(a))
	fmt.Println("FindSubmatch", submatch)
	for _, v := range submatch {
		fmt.Println(string(v))
	}

	//定义和上面的FindIndex一样
	submatchindex := re2.FindSubmatchIndex([]byte(a))
	fmt.Println(submatchindex)

	//FindAllSubmatch,查找所有符合条件的子匹配
	submatchall := re2.FindAllSubmatch([]byte(a), -1)
	fmt.Println(submatchall)

	//FindAllSubmatchIndex,查找所有字匹配的index
	submatchallindex := re2.FindAllSubmatchIndex([]byte(a), -1)
	fmt.Println(submatchallindex)
}

func testTemplate()  {

	//https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/07.4.md
}

func testFile()  {
	println(`GO文件操作的大多数函数都是在os包里面!`)
	println(`	!!!目录操作
		func Mkdir(name string, perm FileMode) error		创建名称为name的目录，权限设置是perm，例如0777
		func MkdirAll(path string, perm FileMode) error		根据path创建多级子目录，例如astaxie/test1/test2。
		func Remove(name string) error				删除名称为name的目录，当目录下有文件或者其他目录是会出错
		func RemoveAll(path string) error			根据path删除多级子目录，如果path是单个名称，那么该目录下的子目录全部删除。
	`)
	os.Mkdir("astaxie", 0777)
	os.MkdirAll("astaxie/test1/test2", 0777)
	err := os.Remove("astaxie")
	if err != nil {
		fmt.Println(err)
	}
	os.RemoveAll("astaxie")

	println(`!!!文件操作
		//新建文件
		func Create(name string) (file *File, err Error)		//根据提供的文件名创建新的文件，返回一个文件对象，默认权限是0666的文件，返回的文件对象是可读写的。
		func NewFile(fd uintptr, name string) *File		//根据文件描述符创建相应的文件，返回一个文件对象
		//打开文件：
		func Open(name string) (file *File, err Error)		//该方法打开一个名称为name的文件，但是是只读方式，内部实现其实调用了OpenFile。
		func OpenFile(name string, flag int, perm uint32) (file *File, err Error)	//打开名称为name的文件，flag是打开的方式，只读、读写等，perm是权限
		写文件函数：
		func (file *File) Write(b []byte) (n int, err Error)	//写入byte类型的信息到文件
		func (file *File) WriteAt(b []byte, off int64) (n int, err Error)	//在指定位置开始写入byte类型的信息
		func (file *File) WriteString(s string) (ret int, err Error)	//写入string信息到文件
	`)
	userFile := "astaxie.txt"
	fout, err := os.Create(userFile)
	if err != nil {
		fmt.Println(userFile, err)
		return
	}
	defer fout.Close()
	for i := 0; i < 10; i++ {
		fout.WriteString("Just a test!\r\n")
		fout.Write([]byte("Just a test!\r\n"))
	}

	println(` !!!读文件函数：
	func (file *File) Read(b []byte) (n int, err Error)		//读取数据到b中
	func (file *File) ReadAt(b []byte, off int64) (n int, err Error)	//从off开始读取数据到b中
	`)
	userFile := "asatxie.txt"
	fl, err := os.Open(userFile)
	if err != nil {
		fmt.Println(userFile, err)
		return
	}
	defer fl.Close()
	buf := make([]byte, 1024)
	for {
		n, _ := fl.Read(buf)
		if 0 == n {
			break
		}
		os.Stdout.Write(buf[:n])
	}

	println(`!!! 删除文件(或文件夹)
		Go语言里面删除文件和删除文件夹是同一个函数
		func Remove(name string) Error	//调用该函数就可以删除文件名为name的文件
	`)

}

func testStrings()  {
	println(`Go标准库中的strings和strconv两个包中的函数来对字符串进行有效快速的操作`)

	//strings包

	//func Contains(s, substr string) bool  //字符串s中是否包含substr，返回bool值
	fmt.Println(strings.Contains("seafood", "foo"))

	//func Join(a []string, sep string) string		//字符串链接，把slice a通过sep链接起来
	s := []string{"foo", "bar", "baz"}
	fmt.Println(strings.Join(s, ", "))		//Output:foo, bar, baz

	//func Index(s, sep string) int		//在字符串s中查找sep所在的位置，返回位置值，找不到返回-1
	fmt.Println(strings.Index("chicken", "ken"))	//Output:4
	fmt.Println(strings.Index("chicken", "dmr"))	//-1

	//func Repeat(s string, count int) string	//重复s字符串count次，最后返回重复的字符串
	fmt.Println("ba" + strings.Repeat("na", 2))		//Output:banana

	//func Replace(s, old, new string, n int) string	//在s字符串中，把old字符串替换为new字符串，n表示替换的次数，小于0表示全部替换
	fmt.Println(strings.Replace("oink oink oink", "k", "ky", 2))	//Output:oinky oinky oink
	fmt.Println(strings.Replace("oink oink oink", "oink", "moo", -1))		//moo moo moo

	//func Split(s, sep string) []string	//把s字符串按照sep分割，返回slice
	fmt.Printf("%q\n", strings.Split("a,b,c", ","))		//Output:["a" "b" "c"]
	fmt.Printf("%q\n", strings.Split("a man a plan a canal panama", "a "))		//["" "man " "plan " "canal panama"]
	fmt.Printf("%q\n", strings.Split(" xyz ", ""))		//[" " "x" "y" "z" " "]
	fmt.Printf("%q\n", strings.Split("", "Bernardo O'Higgins"))		//[""]

	//func Trim(s string, cutset string) string 	//在s字符串的头部和尾部去除cutset指定的字符串
	fmt.Printf("[%q]", strings.Trim(" !!! Achtung !!! ", "! ")) 	//Output:["Achtung"]

	//func Fields(s string) []string	//去除s字符串的空格符，并且按照空格分割返回slice
	fmt.Printf("Fields are: %q", strings.Fields("  foo bar  baz   ")) 	//Output:Fields are: ["foo" "bar" "baz"]


	//strconv中字符串转换函数
	//Append 系列函数将整数等转换为字符串后，添加到现有的字节数组中。
	str := make([]byte, 0, 100)
	str = strconv.AppendInt(str, 4567, 10)
	str = strconv.AppendBool(str, false)
	str = strconv.AppendQuote(str, "abcdefg")
	str = strconv.AppendQuoteRune(str, '单')
	fmt.Println(string(str))

	//Format 系列函数把其他类型的转换为字符串
	a := strconv.FormatBool(false)
	b := strconv.FormatFloat(123.23, 'g', 12, 64)
	c := strconv.FormatInt(1234, 10)
	d := strconv.FormatUint(12345, 10)
	e := strconv.Itoa(1023)
	fmt.Println(a, b, c, d, e)

	//Parse 系列函数把字符串转换为其他类型
	a1, err := strconv.ParseBool("false")
	checkError(err)
	b1, err := strconv.ParseFloat("123.23", 64)
	checkError(err)
	c1, err := strconv.ParseInt("1234", 10, 64)
	checkError(err)
	d1, err := strconv.ParseUint("12345", 10, 64)
	checkError(err)
	e1, err := strconv.Atoi("1023")
	checkError(err)
	fmt.Println(a1, b1, c1, d1, e1)

}

func checkError(e error){
	if e != nil{
		fmt.Println(e)
	}
}

func testSocket()  {
	//Go的net包支持的IP类型
	//type IP []byte


	//ParseIP(s string) IP函数会把一个IPv4或者IPv6的地址转化成IP类型
	name := "192.168.1.1"
	addr := net.ParseIP(name)
	if addr == nil {
		fmt.Println("Invalid address")
	} else {
		fmt.Println("The address is ", addr.String())
	}

	//TCP Socket
	/**
	Go语言的net包中有一个类型TCPConn，这个类型可以用来作为客户端和服务器端交互的通道，他有两个主要的函数：
		func (c *TCPConn) Write(b []byte) (n int, err os.Error)
		func (c *TCPConn) Read(b []byte) (n int, err os.Error)
	TCPConn可以用在客户端和服务器端来读写数据。
	还有我们需要知道一个TCPAddr类型，他表示一个TCP的地址信息，他的定义如下：

		type TCPAddr struct {
			IP IP
			Port int
		}
	在Go语言中通过ResolveTCPAddr获取一个TCPAddr
		func ResolveTCPAddr(net, addr string) (*TCPAddr, os.Error)
			net参数是"tcp4"、"tcp6"、"tcp"中的任意一个，分别表示TCP(IPv4-only),TCP(IPv6-only)或者TCP(IPv4,IPv6的任意一个).
			addr表示域名或者IP地址，例如"www.google.com:80" 或者"127.0.0.1:22".
	 */
	//TCP client
	/**
	Go语言中通过net包中的DialTCP函数来建立一个TCP连接，并返回一个TCPConn类型的对象
		func DialTCP(net string, laddr, raddr *TCPAddr) (c *TCPConn, err os.Error)
			net参数是"tcp4"、"tcp6"、"tcp"中的任意一个，分别表示TCP(IPv4-only)、TCP(IPv6-only)或者TCP(IPv4,IPv6的任意一个)
			laddr表示本机地址，一般设置为nil
			raddr表示远程的服务地址
	 */

	service := "10.208.224.250:80"
	//通过ResolveTCPAddr获取一个TCPAddr(TCP的地址信息)type TCPAddr struct { IP IP ,Port int}
	tcpAddr, err := net.ResolveTCPAddr("tcp4", service)
	checkError(err)
	//把tcpAddr传入DialTCP后创建了一个TCP连接conn
	conn, err := net.DialTCP("tcp", nil, tcpAddr)
	checkError(err)
	//通过conn来发送请求信息   (内容是模拟的http请求头)
	_, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n"))
	checkError(err)
	//通过ioutil.ReadAll从conn中读取全部的文本，也就是服务端响应反馈的信息
	result, err := ioutil.ReadAll(conn)
	checkError(err)
	fmt.Println(string(result))
	os.Exit(0)


	//  TCP server
	/**
	服务器端我们需要绑定服务到指定的非激活端口，并监听此端口，当有客户端请求到达的时候可以接收到来自客户端连接的请求。
	net包中有相应功能的函数，函数定义如下：
		func ListenTCP(net string, laddr *TCPAddr) (l *TCPListener, err os.Error)
		func (l *TCPListener) Accept() (c Conn, err os.Error)
		参数说明同DialTCP的参数一样
	 */
	//TCPServerDemo()


	//控制TCP连接
	/**
	TCP有很多连接控制函

	//设置建立连接的超时时间，客户端和服务器端都适用，当超过设置时间时，连接自动关闭
		func DialTimeout(net, addr string, timeout time.Duration) (Conn, error)

	//设置写入/读取一个连接的超时时间。当超过设置时间时，连接自动关闭。
		func (c *TCPConn) SetReadDeadline(t time.Time) error
		func (c *TCPConn) SetWriteDeadline(t time.Time) error
	//设置keepAlive属性，是操作系统层在tcp上没有数据和ACK的时候，会间隔性的发送keepalive包，操作系统可以通过该包来判断一个tcp连接是否已经断开，
	//在windows上默认2个小时没有收到数据和keepalive包的时候人为tcp连接已经断开，
	//这个功能和我们通常在应用层加的心跳包的功能类似。
		func (c *TCPConn) SetKeepAlive(keepalive bool) os.Error


	//UDP Socket
	//Go语言包中处理UDP Socket和TCP Socket不同的地方就是在服务器端处理多个客户端请求数据包的方式不同,
	//UDP缺少了对客户端连接请求的Accept函数。其他基本几乎一模一样，只有TCP换成了UDP而已。
	//UDP的几个主要函数如下所示：
		func ResolveUDPAddr(net, addr string) (*UDPAddr, os.Error)
		func DialUDP(net string, laddr, raddr *UDPAddr) (c *UDPConn, err os.Error)
		func ListenUDP(net string, laddr *UDPAddr) (c *UDPConn, err os.Error)
		func (c *UDPConn) ReadFromUDP(b []byte) (n int, addr *UDPAddr, err os.Error)
		func (c *UDPConn) WriteToUDP(b []byte, addr *UDPAddr) (n int, err os.Error)



	*/
	//UDPClientDemo()
	//UDPServiceDemo()



}



func TCPServerDemo() {
	service := ":1200"
	tcpAddr, err := net.ResolveTCPAddr("tcp4", service)		//获取TCPAddr
	checkError(err)
	listener, err := net.ListenTCP("tcp", tcpAddr)		//开启监听服务
	checkError(err)
	for {
		conn, err := listener.Accept()		//循环接受请求
		if err != nil {
			continue
		}
		go handleClient(conn)		//启用goroutine处理请求
	}
}

func handleClient(conn net.Conn) {
	//SetReadDeadline设置超时时间，当指定时间内客户端无请求发送，conn便会自动关闭
	conn.SetReadDeadline(time.Now().Add(2 * time.Minute)) // set 2 minutes timeout
	request := make([]byte, 128) // set maxium request length to 128B to prevent flood attack
	defer conn.Close()  // close connection before exit
	for {
		read_len, err := conn.Read(request)		//循环读取客户端发送的数据

		if err != nil {
			fmt.Println(err)
			break
		}

		if read_len == 0 {
			break // connection already closed by client
		} else if strings.TrimSpace(string(request[:read_len])) == "timestamp" {
			//实现时间同步服务的业务逻辑
			daytime := strconv.FormatInt(time.Now().Unix(), 10)
			conn.Write([]byte(daytime))
		} else {
			daytime := time.Now().String()
			conn.Write([]byte(daytime))
		}

		request = make([]byte, 128) // clear last read content
	}
}


func UDPClientDemo()  {

	service := "10.208.224.250:80"
	udpAddr, err := net.ResolveUDPAddr("udp4", service)
	checkError(err)
	conn, err := net.DialUDP("udp", nil, udpAddr)
	checkError(err)
	_, err = conn.Write([]byte("anything"))
	checkError(err)
	var buf [512]byte
	n, err := conn.Read(buf[0:])
	checkError(err)
	fmt.Println(string(buf[0:n]))
}

func UDPServiceDemo()  {
	service := ":1200"
	udpAddr, err := net.ResolveUDPAddr("udp4", service)
	checkError(err)
	conn, err := net.ListenUDP("udp", udpAddr)
	checkError(err)
	for {
		udpHandleClient(conn)
	}
}
func udpHandleClient(conn *net.UDPConn) {
	var buf [512]byte
	_, addr, err := conn.ReadFromUDP(buf[0:])
	if err != nil {
		return
	}
	daytime := time.Now().String()
	conn.WriteToUDP([]byte(daytime), addr)
}


func testWebsocket() {
	//对应的html页面demo见webSocketClient.html
	/*
	http.Handle("/", websocket.Handler(Echo))

	if err := http.ListenAndServe(":1234", nil); err != nil {
		log.Fatal("ListenAndServe:", err)
	}
	*/
}

func Echo(ws *websocket.Conn) {
	var err error

	for {
		var reply string

		if err = websocket.Message.Receive(ws, &reply); err != nil {
			fmt.Println("Can't receive")
			break
		}

		fmt.Println("Received back from client: " + reply)

		msg := "Received:  " + reply
		fmt.Println("Sending to client: " + msg)

		if err = websocket.Message.Send(ws, msg); err != nil {
			fmt.Println("Can't send")
			break
		}
	}
}


func testREST() {
	//github.com/julienschmidt/httprouter
	router := httprouter.New()
	router.GET("/", Index)
	router.GET("/hello/:name", Hello)

	router.GET("/user/:uid", getuser)
	router.POST("/adduser/:uid", adduser)
	router.DELETE("/deluser/:uid", deleteuser)
	router.PUT("/moduser/:uid", modifyuser)

	//log.Fatal(http.ListenAndServe(":8080", router))
}

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
	fmt.Fprint(w, "Welcome!\n")
}

func Hello(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	fmt.Fprintf(w, "hello, %s!\n", ps.ByName("name"))
}

func getuser(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	uid := ps.ByName("uid")
	fmt.Fprintf(w, "you are get user %s", uid)
}

func modifyuser(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	uid := ps.ByName("uid")
	fmt.Fprintf(w, "you are modify user %s", uid)
}

func deleteuser(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	uid := ps.ByName("uid")
	fmt.Fprintf(w, "you are delete user %s", uid)
}

func adduser(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	// uid := r.FormValue("uid")
	uid := ps.ByName("uid")
	fmt.Fprintf(w, "you are add user %s", uid)
}

func testRPC()  {

	println(`Go标准包中已经提供了对RPC的支持，而且支持三个级别的RPC：TCP、HTTP、JSONRPC。但Go的RPC包是独一无二的RPC，它和传统的RPC系统不同，它只支持Go开发的服务器与客户端之间的交互，因为在内部，它们采用了Gob来编码。

		Go RPC的函数只有符合下面的条件才能被远程访问，不然会被忽略，详细的要求如下：
			函数必须是导出的(首字母大写)
			必须有两个导出类型的参数，
			第一个参数是接收的参数，第二个参数是返回给客户端的参数，第二个参数必须是指针类型的
			函数还要有一个返回值error

		举个例子，正确的RPC函数格式如下：
			func (t *T) MethodName(argType T1, replyType *T2) error
			T、T1和T2类型必须能被encoding/gob包编解码。

		任何的RPC都需要通过网络来传递数据，Go RPC可以利用HTTP和TCP来传递数据，利用HTTP的好处是可以直接复用net/http里面的一些函数。
		`)

	//HTTP RPC 实现
	//HttpRpcServerDemo()
	//HttpRpcClinetDemo()

	//TCP RPC 实现
	//TcpRpcServerDemo()
	//TcpRpcClinetDemo()

	//JSON RPC 实现
	//JsonRpcServerDemo()
	//JsonRpcClinetDemo()

}

func HttpRpcServerDemo()  {
	arith := new(Arith)
	rpc.Register(arith)
	rpc.HandleHTTP()

	err := http.ListenAndServe(":1234", nil)
	if err != nil {
		fmt.Println(err.Error())
	}
}

type Args struct {
	A, B int
}
type Quotient struct {
	Quo, Rem int
}
type Arith int
func (t *Arith) Multiply(args *Args, reply *int) error {
	*reply = args.A * args.B
	return nil
}
func (t *Arith) Divide(args *Args, quo *Quotient) error {
	if args.B == 0 {
		return errors.New("divide by zero")
	}
	quo.Quo = args.A / args.B
	quo.Rem = args.A % args.B
	return nil
}

func HttpRpcClinetDemo() {
	serverAddress := "127.0.0.1:1234"

	client, err := rpc.DialHTTP("tcp", serverAddress+":1234")
	if err != nil {
		log.Fatal("dialing:", err)
	}
	// Synchronous call
	args := Args{17, 8}
	var reply int
	err = client.Call("Arith.Multiply", args, &reply)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d*%d=%d\n", args.A, args.B, reply)

	var quot Quotient
	err = client.Call("Arith.Divide", args, &quot)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d/%d=%d remainder %d\n", args.A, args.B, quot.Quo, quot.Rem)

}

func TcpRpcServerDemo() {

	arith := new(Arith)
	rpc.Register(arith)

	tcpAddr, err := net.ResolveTCPAddr("tcp", ":1234")
	checkError(err)

	listener, err := net.ListenTCP("tcp", tcpAddr)
	checkError(err)

	for {
		conn, err := listener.Accept()
		if err != nil {
			continue
		}
		rpc.ServeConn(conn)
	}

}

func TcpRpcClinetDemo() {
	service := "127.0.0.1:1234"

	client, err := rpc.Dial("tcp", service)
	if err != nil {
		log.Fatal("dialing:", err)
	}
	// Synchronous call
	args := Args{17, 8}
	var reply int
	err = client.Call("Arith.Multiply", args, &reply)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d*%d=%d\n", args.A, args.B, reply)

	var quot Quotient
	err = client.Call("Arith.Divide", args, &quot)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d/%d=%d remainder %d\n", args.A, args.B, quot.Quo, quot.Rem)

}


func JsonRpcServerDemo() {

	arith := new(Arith)
	rpc.Register(arith)

	tcpAddr, err := net.ResolveTCPAddr("tcp", ":1234")
	checkError(err)

	listener, err := net.ListenTCP("tcp", tcpAddr)
	checkError(err)

	for {
		conn, err := listener.Accept()
		if err != nil {
			continue
		}
		jsonrpc.ServeConn(conn)
	}

}

func JsonRpcClinetDemo() {
	service := "127.0.0.1:1234"

	client, err := jsonrpc.Dial("tcp", service)
	if err != nil {
		log.Fatal("dialing:", err)
	}
	// Synchronous call
	args := Args{17, 8}
	var reply int
	err = client.Call("Arith.Multiply", args, &reply)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d*%d=%d\n", args.A, args.B, reply)

	var quot Quotient
	err = client.Call("Arith.Divide", args, &quot)
	if err != nil {
		log.Fatal("arith error:", err)
	}
	fmt.Printf("Arith: %d/%d=%d remainder %d\n", args.A, args.B, quot.Quo, quot.Rem)

}

func testCsrf()  {

	//https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.1.md
	/**
		服务端的预防CSRF攻击的方式方法有多种，但思想上都是差不多的，主要从以下2个方面入手：
		1、正确使用GET,POST和Cookie；
			mux.Get("/user/:uid", getuser)
			mux.Post("/user/:uid", modifyuser)
		2、在非GET请求中增加伪随机数；
			大概有三种方式来进行
			(1)为每个用户生成一个唯一的cookie token，所有表单都包含同一个伪随机值，
				这种方案最简单，因为攻击者不能获得第三方的Cookie(理论上)，所以表单中的数据也就构造失败，
				但是由于用户的Cookie很容易由于网站的XSS漏洞而被盗取，所以这个方案必须要在没有XSS的情况下才安全。
			(2)每个请求使用验证码，这个方案是完美的，因为要多次输入验证码，所以用户友好性很差，所以不适合实际运用。
			(3)不同的表单包含一个不同的伪随机值，我们在4.4小节介绍“如何防止表单多次递交”时介绍过此方案，复用相关代码

			//不同的表单包含一个不同的伪随机值
			生成随机数token
				h := md5.New()
				io.WriteString(h, strconv.FormatInt(crutime, 10))
				io.WriteString(h, "ganraomaxxxxxxxxx")
				token := fmt.Sprintf("%x", h.Sum(nil))

				t, _ := template.ParseFiles("login.gtpl")
				t.Execute(w, token)
			输出token
				<input type="hidden" name="token" value="{{.}}">
			验证token
				r.ParseForm()
				token := r.Form.Get("token")
				if token != "" {
					//验证token的合法性
				} else {
					//不存在token报错
				}

	 */

}

func testInputVerify()  {
	/**
	输入过滤
	GO语言过滤数据主要采用如下一些库来操作：
		strconv包下面的字符串转化相关函数，因为从Request中的r.Form返回的是字符串，而有些时候我们需要将之转化成整/浮点数，Atoi、ParseBool、ParseFloat、ParseInt等函数就可以派上用场了。
		string包下面的一些过滤函数Trim、ToLower、ToTitle等函数，能够帮助我们按照指定的格式获取信息。
		regexp包用来处理一些复杂的需求，例如判定输入是否是Email、生日之类。

	r.ParseForm()
	username := r.Form.Get("username")
	CleanMap := make(map[string]interface{}, 0)
	if ok, _ := regexp.MatchString("^[a-zA-Z0-9]+$", username); ok {
		CleanMap["username"] = username
	}
	 */
}

func testXss()  {

	//XSS攻击：跨站脚本攻击(Cross-Site Scripting)，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。
	/**
		XSS通常可以分为两大类：
			一类是存储型XSS，主要出现在让用户输入数据，供其他浏览此页的用户进行查看的地方，包括留言、评论、博客日志和各类表单等。
				应用程序从数据库中查询数据，在页面中显示出来，攻击者在相关页面输入恶意的脚本数据后，用户浏览此类页面时就可能受到攻击。
				这个流程简单可以描述为:恶意用户的Html输入Web程序->进入数据库->Web程序->用户浏览器。
			另一类是反射型XSS，主要做法是将脚本代码加入URL地址的请求参数里，请求参数进入程序后在页面直接输出，用户点击类似的恶意链接就可能受到攻击。

		XSS目前主要的手段和目的如下：
			1.盗用cookie，获取敏感信息。
			2.利用植入Flash，通过crossdomain权限设置进一步获取更高权限；或者利用Java等得到类似的操作。
			3.利用iframe、frame、XMLHttpRequest或上述Flash等方式，以（被攻击者）用户的身份执行一些管理动作，或执行一些如:发微博、加好友、发私信等常规操作，前段时间新浪微博就遭遇过一次XSS。
			4.利用可被攻击的域受到其他域信任的特点，以受信任来源的身份请求一些平时不允许的操作，如进行不当的投票活动。
			5.在访问量极大的一些页面上的XSS可以攻击一些小型网站，实现DDoS攻击的效果
		如何预防XSS
			!!! 答案很简单，坚决不要相信用户的任何输入，并过滤掉输入中的所有特殊字符。这样就能消灭绝大部分的XSS攻击。

		目前防御XSS主要有如下几种方式：
			1.过滤特殊字符
				避免XSS的方法之一主要是将用户所提供的内容进行过滤，Go语言提供了HTML的过滤函数：
				text/template包下面的HTMLEscapeString、JSEscapeString等函数
			2.使用HTTP头指定类型
				`w.Header().Set("Content-Type","text/javascript")`
			这样就可以让浏览器解析javascript代码，而不会是html输出。
	 */


}

func testSQLInjection()  {
	//!!! 永远不要信任外界输入的数据，特别是来自于用户的数据，包括选择框、表单隐藏域和 cookie
	/**
	SQL注入攻击的危害这么大，那么该如何来防治呢?下面这些建议或许对防治SQL注入有一定的帮助。
		1.严格限制Web应用的数据库的操作权限，给此用户提供仅仅能够满足其工作的最低权限，从而最大限度的减少注入攻击对数据库的危害。
		2.检查输入的数据是否具有所期望的数据格式，严格限制变量的类型，例如使用regexp包进行一些匹配处理，或者使用strconv包对字符串转化成其他基本类型的数据进行判断。
		3.对进入数据库的特殊字符（'"\尖括号&*;等）进行转义处理，或编码转换。Go 的text/template包里面的HTMLEscapeString函数可以对字符串进行转义处理。
		4.所有的查询语句建议使用数据库提供的参数化查询接口，参数化的语句使用参数而不是将用户输入变量嵌入到SQL语句中，即不要直接拼接SQL语句。
				例如使用database/sql里面的查询函数Prepare和Query，或者Exec(query string, args ...interface{})。
		5.在应用发布之前建议使用专业的SQL注入检测工具进行检测，以及时修补被发现的SQL注入漏洞。网上有很多这方面的开源工具，例如sqlmap、SQLninja等。
		6.避免网站打印出SQL错误信息，比如类型错误、字段不匹配等，把代码里的SQL语句暴露出来，以防止攻击者利用这些错误信息进行SQL注入。
	 */
}

func testSavePassword()  {
	//密码存储的普通方案
		//将明文密码做单向哈希后存储
		h := sha256.New()
		io.WriteString(h, "His money is twice tainted: 'taint yours and 'taint mine.")
		fmt.Printf("% x", h.Sum(nil))

		//import "crypto/sha1"
		h := sha1.New()
		io.WriteString(h, "His money is twice tainted: 'taint yours and 'taint mine.")
		fmt.Printf("% x", h.Sum(nil))

		//import "crypto/md5"
		h := md5.New()
		io.WriteString(h, "需要加密的密码")
		fmt.Printf("%x", h.Sum(nil))

		/*
			单向哈希有两个特性：
			1）同一个密码进行单向哈希，得到的总是唯一确定的摘要。
			2）计算速度快。随着技术进步，一秒钟能够完成数十亿次单向哈希计算。
			结合上面两个特点，考虑到多数人所使用的密码为常见的组合，攻击者可以将所有密码的常见组合进行单向哈希，得到一个摘要组合, 然后与数据库中的摘要进行比对即可获得对应的密码。这个摘要组合也被称为rainbow table。

			因此通过单向加密之后存储的数据，和明文存储没有多大区别。因此，一旦网站的数据库泄露，所有用户的密码本身就大白于天下。
		 */

	//进阶方案
		//“加盐”哈希的方式来存储密码
			//import "crypto/md5"
			//假设用户名abc，密码123456
			h := md5.New()
			io.WriteString(h, "需要加密的密码")

			//pwmd5等于e10adc3949ba59abbe56e057f20f883e
			pwmd5 :=fmt.Sprintf("%x", h.Sum(nil))

			//指定两个 salt： salt1 = @#$%   salt2 = ^&*()
			salt1 := "@#$%"
			salt2 := "^&*()"

			//salt1+用户名+salt2+MD5拼接
			io.WriteString(h, salt1)
			io.WriteString(h, "abc")
			io.WriteString(h, salt2)
			io.WriteString(h, pwmd5)

			last :=fmt.Sprintf("%x", h.Sum(nil))
			println(last)

	//专家方案
		//推荐scrypt方案，scrypt是由著名的FreeBSD黑客Colin Percival为他的备份服务Tarsnap开发的
		//目前Go语言里面支持的库http://code.google.com/p/go/source/browse?repo=crypto#hg%2Fscrypt
											//https://github.com/golang/go#hg%2Fscrypt
											//https://github.com/golang/go/blob/master/src/crypto/crypto.go
									//https://github.com/golang/crypto/blob/master/scrypt/scrypt.go
									//https://godoc.org/golang.org/x/crypto/scrypt
			dk := scrypt.Key([]byte("some password"), []byte(salt), 16384, 8, 1, 32)
			//通过上面的的方法可以获取唯一的相应的密码值，这是目前为止最难破解的。


	/**
	总结:	看到这里，如果你产生了危机感，那么就行动起来：
		1）如果你是普通用户，那么我们建议使用LastPass进行密码存储和生成，对不同的网站使用不同的密码；
		2）如果你是开发人员， 那么我们强烈建议你采用专家方案进行密码存储。
	 */

}

func testCrypto()  {
	/** base64加解密
	如果Web应用足够简单，数据的安全性没有那么严格的要求，那么可以采用一种比较简单的加解密方法是base64
	 */
	base64Demo()
	/**  高级加解密
	Go语言的crypto里面支持对称加密的高级加解密包有：
		crypto/aes包：AES(Advanced Encryption Standard)，又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。
		crypto/des包：DES(Data Encryption Standard)，是一种对称加密标准，是目前使用最广泛的密钥系统，特别是在保护金融数据的安全中。曾是美国联邦政府的加密标准，但现已被AES所替代。
	 */
	aesDemo()
}

func base64Demo()  {

	// encode
	hello := "你好，世界！ hello world"
	debyte := base64Encode([]byte(hello))
	fmt.Println(debyte)
	// decode
	enbyte, err := base64Decode(debyte)
	if err != nil {
		fmt.Println(err.Error())
	}

	if hello != string(enbyte) {
		fmt.Println("hello is not equal to enbyte")
	}

	fmt.Println(string(enbyte))
}


func base64Encode(src []byte) []byte {
	return []byte(base64.StdEncoding.EncodeToString(src))
}
func base64Decode(src []byte) ([]byte, error) {
	return base64.StdEncoding.DecodeString(string(src))
}

func aesDemo()  {

	var commonIV = []byte{0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0a, 0x0b, 0x0c, 0x0d, 0x0e, 0x0f}
	//需要去加密的字符串
	plaintext := []byte("My name is Astaxie")
	//aes的加密字符串
	key_text := "astaxie12798akljzmknm.ahkjkljl;k"
	fmt.Println(len(key_text))

	/**
	调用函数aes.NewCipher,返回了一个cipher.Block接口
	(参数key必须是16、24或者32位的[]byte，分别对应AES-128, AES-192或AES-256算法)
	cipher.Block接口实现了三个功能
	type Block interface {
			// BlockSize returns the cipher's block size.
			BlockSize() int

			// Encrypt encrypts the first block in src into dst.
			// Dst and src may point at the same memory.
			Encrypt(dst, src []byte)

			// Decrypt decrypts the first block in src into dst.
			// Dst and src may point at the same memory.
			Decrypt(dst, src []byte)
		}
	 */
	// 创建加密算法aes
	c, err := aes.NewCipher([]byte(key_text))
	if err != nil {
		fmt.Printf("Error: NewCipher(%d bytes) = %s", len(key_text), err)
		os.Exit(-1)
	}

	//加密字符串
	cfb := cipher.NewCFBEncrypter(c, commonIV)
	ciphertext := make([]byte, len(plaintext))
	cfb.XORKeyStream(ciphertext, plaintext)
	fmt.Printf("%s=>%x\n", plaintext, ciphertext)

	// 解密字符串
	cfbdec := cipher.NewCFBDecrypter(c, commonIV)
	plaintextCopy := make([]byte, len(plaintext))
	cfbdec.XORKeyStream(plaintextCopy, ciphertext)
	fmt.Printf("%x=>%s\n", ciphertext, plaintextCopy)


}

func testI18n()  {

	//设置Locale
		//通过域名设置loacl
			/**
			//采用www.asta.com当做我们的英文站(默认站)，而把域名www.asta.cn当做中文站
			这样处理有几点好处：
				通过URL就可以很明显的识别
				用户可以通过域名很直观的知道将访问那种语言的站点
				在Go程序中实现非常的简单方便，通过一个map就可以实现
				有利于搜索引擎抓取，能够提高站点的SEO

			if r.Host == "www.asta.com" {
				i18n.SetLocale("en")
			} else if r.Host == "www.asta.cn" {
				i18n.SetLocale("zh-CN")
			} else if r.Host == "www.asta.tw" {
				i18n.SetLocale("zh-TW")
			}

			//通过子域名来设置地区，例如"en.asta.com"表示英文站点，"cn.asta.com"表示中文站点
			prefix := strings.Split(r.Host,".")

			if prefix[0] == "en" {
				i18n.SetLocale("en")
			} else if prefix[0] == "cn" {
				i18n.SetLocale("zh-CN")
			} else if prefix[0] == "tw" {
				i18n.SetLocale("zh-TW")
			}

			//弊端
				首先域名成本比较高，开发一个Locale就需要一个域名，而且往往统一名称的域名不一定能申请的到，
				其次我们不愿意为每个站点去本地化一个配置，而更多的是采用url后面带参数的方式


		//从域名参数设置Locale
			目前最常用的设置Locale的方式是在URL里面带上参数，
			例如www.asta.com/hello?locale=zh或者www.asta.com/zh/hello。
			这样我们就可以设置地区：i18n.SetLocale(params["locale"])
			mux.Get("/:locale/books", listbook)

			优点，它采用RESTful的方式，以使得我们不需要增加额外的方法来处理

		//从客户端设置地区
			//客户端请求的时候在HTTP头信息里面有Accept-Language，一般的客户端都会设置该信息
			AL := r.Header.Get("Accept-Language")
			if AL == "en" {
				i18n.SetLocale("en")
			} else if AL == "zh-CN" {
				i18n.SetLocale("zh-CN")
			} else if AL == "zh-TW" {
				i18n.SetLocale("zh-TW")
			}

		//IP地址设置地区
			另一种根据客户端来设定地区就是用户访问的IP，
			我们根据相应的IP库，对应访问的IP到地区，
			目前全球比较常用的就是GeoIP Lite Country这个库。
			这种设置地区的机制非常简单，我们只需要根据IP数据库查询用户的IP然后返回国家地区，根据返回的结果设置对应的地区。

		//用户profile设置地区
			当然你也可以让用户根据你提供的下拉菜单或者别的什么方式的设置相应的locale，
			然后我们将用户输入的信息，保存到与它帐号相关的profile中，
			当用户再次登陆的时候把这个设置复写到locale设置中，
			这样就可以保证该用户每次访问都是基于自己先前设置的locale来获得页面。
	 */

	//本地化文本信息
		localLableDemo()

	//本地化日期时间
		/** 需要解决两点:
				时区问题
				格式问题

			$GOROOT/lib/time包中的timeinfo.zip含有locale对应的时区的定义
			为了获得对应于当前locale的时间，
			我们应首先使用time.LoadLocation(name string)获取相应于地区的locale，
				比如Asia/Shanghai或America/Chicago对应的时区信息，
			然后再利用此信息与调用time.Now获得的Time对象协作来获得最终的时间
		 */
	//localLableDemo()

	//本地化视图和资源
		/**
		我们可能会根据Locale的不同来展示视图，这些视图包含不同的图片、css、js等各种静态资源。
		文件目录安排：
			views
			|--en  //英文模板
				|--images     //存储图片信息
				|--js         //存储JS文件
				|--css        //存储css文件
				index.tpl     //用户首页
				login.tpl     //登陆首页
			|--zh-CN //中文模板
				|--images
				|--js
				|--css
				index.tpl
				login.tpl
		对应的渲染的实现代码：
			s1, _ := template.ParseFiles("views"+lang+"index.tpl")
			VV.Lang=lang
			s1.Execute(os.Stdout, VV)
		对应的index.tpl
			// js文件
			<script type="text/javascript" src="views/{{.VV.Lang}}/js/jquery/jquery-1.8.0.min.js"></script>
			// css文件
			<link href="views/{{.VV.Lang}}/css/bootstrap-responsive.min.css" rel="stylesheet">
			// 图片文件
			<img src="views/{{.VV.Lang}}/images/btn.png">
		 */

	//管理本地包
		/**
		Go语言一般将
			Locale有关的文件放置在config/locales下，
			假设你要支持中文和英文，那么你需要在这个文件夹下放置en.json和zh.json。
				# zh.json
				{
				"zh": {
					"submit": "提交",
					"create": "创建"
					}
				}

				#en.json
				{
				"en": {
					"submit": "Submit",
					"create": "Create"
					}
				}

			为了支持国际化，在此我们使用了一个国际化相关的包——go-i18n，
			首先我们向go-i18n包注册config/locales这个目录,以加载所有的locale文件
				Tr:=i18n.NewLocale()
				Tr.LoadPath("config/locales")
			这个包使用起来很简单，你可以通过下面的方式进行测试：
				fmt.Println(Tr.Translate("submit"))
				//输出Submit
				Tr.SetLocale("zh")
				fmt.Println(Tr.Translate("submit"))
				//输出“递交”
	 	*/

	//自动加载本地包
		//go-i18n库已经预加载了很多默认的格式信息，例如时间格式、货币格式，用户可以在自定义配置时改写这些默认配置
		//https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/10.3.md#自动加载本地包


	//template mapfunc
		//Go语言的模板支持自定义模板函数
		/**
		1.文本信息
			文本信息调用Tr.Translate来实现相应的信息转换，mapFunc的实现如下：

			func I18nT(args ...interface{}) string {
				ok := false
				var s string
				if len(args) == 1 {
					s, ok = args[0].(string)
				}
				if !ok {
					s = fmt.Sprint(args...)
				}
				return Tr.Translate(s)
			}
			注册函数如下：
			t.Funcs(template.FuncMap{"T": I18nT})
			模板中使用如下：
			{{.V.Submit | T}}

		2.时间日期
			时间日期调用Tr.Time函数来实现相应的时间转换，mapFunc的实现如下：

			func I18nTimeDate(args ...interface{}) string {
				ok := false
				var s string
				if len(args) == 1 {
					s, ok = args[0].(string)
				}
				if !ok {
					s = fmt.Sprint(args...)
				}
				return Tr.Time(s)
			}
			注册函数如下：

			t.Funcs(template.FuncMap{"TD": I18nTimeDate})
			模板中使用如下：

			{{.V.Now | TD}}

		3.货币信息
			货币调用Tr.Money函数来实现相应的时间转换，mapFunc的实现如下：

			func I18nMoney(args ...interface{}) string {
				ok := false
				var s string
				if len(args) == 1 {
					s, ok = args[0].(string)
				}
				if !ok {
					s = fmt.Sprint(args...)
				}
				return Tr.Money(s)
			}
			注册函数如下：

			t.Funcs(template.FuncMap{"M": I18nMoney})
			模板中使用如下：

			{{.V.Money | M}}

		 */

	//astaxie的整合
	//https://github.com/astaxie/go-i18n

}

func localLableDemo()  {

	locales = make(map[string]map[string]string, 2)
	en := make(map[string]string, 10)
	en["pea"] = "pea"
	en["bean"] = "bean"
	en["how old"] ="I am %d years old"
	en["time_zone"]="America/Chicago"	//时区
	en["date_format"]="%Y-%m-%d %H:%M:%S"	//时间格式
	locales["en"] = en
	cn := make(map[string]string, 10)
	cn["pea"] = "豌豆"
	cn["bean"] = "毛豆"
	cn["how old"] ="我今年%d岁了"
	cn["time_zone"]="Asia/Shanghai"		//时区
	cn["date_format"]="%Y年%m月%d日 %H时%M分%S秒"		//时间格式
	locales["zh-CN"] = cn
	lang := "zh-CN"
	fmt.Println(msg(lang, "pea"))
	fmt.Println(msg(lang, "bean"))
	fmt.Printf(msg(lang, "how old"), 30)

	loc,_:=time.LoadLocation(msg(lang,"time_zone"))
	t:=time.Now()
	t = t.In(loc)
	fmt.Println(t.Format(time.RFC3339))		//时区

	fmt.Println(date(msg(lang,"date_format"),t))	//时间



}
var locales map[string]map[string]string
func msg(locale, key string) string {
	if v, ok := locales[locale]; ok {
		if v2, ok := v[key]; ok {
			return v2
		}
	}
	return ""
}
func date(fomate string,t time.Time) string{
	year, month, day = t.Date()
	hour, min, sec = t.Clock()
	//解析相应的%Y %m %d %H %M %S然后返回信息
	//%Y 替换成2012
	//%m 替换成10
	//%d 替换成24

}

func testDebug()  {

	/** 错误处理
		Go定义了一个叫做error的类型，来显式表达错误。在使用时，通过把返回的error变量与nil的比较，来判定操作是否成功
		 */
		//func Open(name string) (file *File, err error)
		f, err := os.Open("filename.ext")
		if err != nil {
			log.Fatal(err)
		}

		/**
		//可以通过errors.New把一个字符串转化为errorString，以得到一个满足接口error的对象，
			errors.New内部实现如下：
				// New returns an error that formats as the given text.
				func New(text string) error {
					return &errorString{text}
				}
		 */
		//errors.New使用示例
		f, err = Sqrt(-1)
		if err != nil {
			fmt.Println(err)
		}

	//自定义Error
		/**
		我们知道error是一个interface，所以在实现自己的包的时候，通过定义实现此接口的结构，我们就可以实现自己的错误定义，
		请看来自Json包的示例：
			type SyntaxError struct {
				msg    string // 错误描述
				Offset int64  // 错误发生的位置
			}
			func (e *SyntaxError) Error() string { return e.msg }
		 */

	/**
	!! 注意!!，函数返回自定义错误时，返回值推荐设置为error类型，而非自定义错误类型，特别需要注意的是不应预声明自定义错误类型的变量。例如：

		func Decode() *SyntaxError { // 错误，将可能导致上层调用者err!=nil的判断永远为true。
			var err *SyntaxError     // 预声明错误变量
			if 出错条件 {
				err = &SyntaxError{}
			}
			return err               // 错误，err永远等于非nil，导致上层调用者err!=nil的判断始终为true
		}
	 */

	/** 更复杂的错误处理
		我们来参考一下net包采用的方法：

			package net

			type Error interface {
				error
				Timeout() bool   // Is the error a timeout?
				Temporary() bool // Is the error temporary?
			}
		在调用的地方，通过类型断言err是不是net.Error,来细化错误的处理，例如下面的例子，如果一个网络发生临时性错误，那么将会sleep 1秒之后重试：

			if nerr, ok := err.(net.Error); ok && nerr.Temporary() {
				time.Sleep(1e9)
				continue
			}
			if err != nil {
				log.Fatal(err)
			}
	 */


	//错误处理
		/**
		Go在错误处理上采用了与C类似的检查返回值的方式，而不是其他多数主流语言采用的异常方式，
		这造成了代码编写上的一个很大的缺点:错误处理代码的冗余，
		对于这种情况是我们通过[复用检测函数]来减少类似的代码。
		 */
		http.HandleFunc("/view", viewRecord)

	//更高级的处理方式
		//!!没看懂!!  https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/11.1.md#错误处理



	//GDB调试
		/**
		工具都能够动态的显示变量信息，单步调试等。
		不过庆幸的是Go也有类似的工具支持：GDB。
		Go内部已经内置支持了GDB，所以，我们可以通过GDB来进行调试

		!!另外建议纯go代码使用delve可以很好的进行Go代码调试


		GDB是FSF(自由软件基金会)发布的一个强大的类UNIX系统下的程序调试工具
		功能:：
			1.启动程序，可以按照开发者的自定义要求运行程序。
			2.可让被调试的程序在开发者设定的调置的断点处停住。（断点可以是条件表达式）
			3.当程序被停住时，可以检查此时程序中所发生的事。
			4.动态的改变当前程序的执行环境。

		编译Go程序的时候需要注意以下几点
			传递参数-ldflags "-s"，忽略debug的打印信息
			传递-gcflags "-N -l" 参数，这样可以忽略Go内部做的一些优化，聚合变量和函数等优化，这样对于GDB调试来说非常困难，所以在编译的时候加入这两个参数避免这些优化。

		常用命令
			list
				简写命令l，用来显示源代码，默认显示十行代码，后面可以带上参数显示的具体行，
				例如：list 15，显示十行代码，其中第15行在显示的十行里面的中间，
				如下所示。
				  10	        time.Sleep(2 * time.Second)
				  11	        c <- i
				  12	    }
				  13	    close(c)
				  14	}
				  15
				  16	func main() {
				  17	    msg := "Starting main"
				  18	    fmt.Println(msg)
				  19	    bus := make(chan int)

			break
				简写命令 b,用来设置断点，后面跟上参数设置断点的行数，例如b 10在第十行设置断点。

			delete
				简写命令 d,用来删除断点，后面跟上断点设置的序号，这个序号可以通过info breakpoints获取相应的设置的断点序号，
				如下是显示的设置断点序号。
				  Num     Type           Disp Enb Address            What
				  2       breakpoint     keep y   0x0000000000400dc3 in main.main at /home/xiemengjun/gdb.go:23
				  breakpoint already hit 1 time

			backtrace
				简写命令 bt,用来打印执行的代码过程，如下所示：
				  #0  main.main () at /home/xiemengjun/gdb.go:23
				  #1  0x000000000040d61e in runtime.main () at /home/xiemengjun/go/src/pkg/runtime/proc.c:244
				  #2  0x000000000040d6c1 in schedunlock () at /home/xiemengjun/go/src/pkg/runtime/proc.c:267
				  #3  0x0000000000000000 in ?? ()

			info
				info命令用来显示信息，后面有几种参数，我们常用的有如下几种：
				info locals
					显示当前执行的程序中的变量值
				info breakpoints
					显示当前设置的断点列表
				info goroutines
					显示当前执行的goroutine列表，如下代码所示,带*的表示当前执行的
					  * 1  running runtime.gosched
					  * 2  syscall runtime.entersyscall
						3  waiting runtime.gosched
						4 runnable runtime.gosched

			print
				简写命令p，用来打印变量或者其他信息，后面跟上需要打印的变量名，
					当然还有一些很有用的函数$len()和$cap()，用来返回当前string、slices或者maps的长度和容量。

			whatis
				用来显示当前变量的类型，后面跟上变量名，
				例如whatis msg,显示如下：
				type = struct string

			next
				简写命令 n,用来单步调试，跳到下一步，当有断点之后，可以输入n跳转到下一步继续执行

			continue
				简称命令 c，用来跳出当前断点处，后面可以跟参数N，跳过多少次断点

			set variable
				该命令用来改变运行过程中的变量值，格式如：set variable <var>=<value>

		 */

	//TODO 以后再试
	//实测未成功 https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/11.2.md#调试过程



}

func Open(name string) (file *File, err error){}

func Sqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, errors.New("math: square root of negative number")
	}
	// implementation
}

func viewRecord(w http.ResponseWriter, r *http.Request) {
	c := appengine.NewContext(r)
	key := datastore.NewKey(c, "Record", r.FormValue("id"), 0, nil)
	record := new(Record)
	if err := datastore.Get(c, key, record); err != nil {
		http.Error(w, err.Error(), 500)
		return
	}
	if err := viewTemplate.Execute(w, record); err != nil {
		http.Error(w, err.Error(), 500)
	}
}

func testCase()  {
	/**
	Go语言中自带有一个轻量级的测试框架testing和自带的go test命令来实现单元测试和性能测试，
		testing框架和其他语言中的测试框架类似，你可以基于这个框架写针对相应函数的测试用例，也可以基于该框架写相应的压力测试用例

		!! 建议安装gotests插件自动生成测试代码:
			go get -u -v github.com/cweill/gotests/...


	 */

}

func testLog()  {

	/**
	Go目前标准包只是包含了简单的功能，
	如果我们想把我们的应用日志保存到文件，然后又能够结合日志实现很多复杂的功能
	（编写过Java或者C++的读者应该都使用过log4j和log4cpp之类的日志工具），
	可以使用第三方开发的日志系统:logrus和seelog，它们实现了很强大的日志功能，可以结合自己项目选择
	 */

}







```
  
  
