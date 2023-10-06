# golang异常处理

## error

官方error包errors

~~~go
package errors

// New returns an error that formats as the given text.
// Each call to New returns a distinct error value even if the text is identical.
func New(text string) error {
	return &errorString{text}
}

// errorString is a trivial implementation of error.
type errorString struct {
	s string
}

func (e *errorString) Error() string {
	return e.s
}

~~~

自定义一个error类型

~~~go
//自定义一个error类型实现error接口
type sqrtError struct{
    errString string
}
func (err *sqrtError)Error()string{
    return err.errString
}
func newError(str string)*error{
    return &sqrtError{str}
}
func sqrt(num float64)float64,error{
    if num<0{
        return nil,newError{"num不可小于零"}
    }
    return math.Sqrt(num),nil
}
~~~

## panic()

panic会导致程序终止，即程序宕机

程序中断  

~~~go
func main(){
    fmt.Println("打印")
    panic("中断")
    fmt.Println("程序之执行不到这里")
}
~~~





## recover()与defer

recover()要搭配defer使用 注意多个defer函数时，倒序执行，即先注册的后执行

~~~go
func test() {
	fmt.Println("test()")
}
func main() {
	defer func() {
		test()
		time.Sleep(time.Second * 2)
		recover()
	}()
	fmt.Println("print")
	panic("panic")
}

~~~



