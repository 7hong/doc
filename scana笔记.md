# 特点
* Scalable编程语言
* 纯正的面向对象语言
* 函数式编程语言
* 无缝的与JAVA互操作

# 函数式编程思想
* Pure Function(纯函数)
```java
//eg. 无副作用
int x = 1;
fun(int y){
	return x + y;
}
//eg2. 有副作用
int x = 1;
fun(int y){
	x+=y;
	return x;
}
```

* 引用透明（对于相同的输入，总是得到相同的输出）
```
//append()函数违反引用透明
var x = new StringBuilder("Hello")
var x = x.append("jiang")
var x = y.append("jiang")
```
* 表达式求值
1. 严格求值
2. 非严格求值
3. 惰性求值

* 递归函数
1. 尾递归

# 搭建环境

##下载
1. 下载Scala 2.11.7
2. 下载SBT 1.2.7
3. REPL

## 命令
* 打开REPL

```
>scala 
```
```
>sbt console
```
# Scala语法基础
## 变量
1. val
2. var 
3. lazy val
`可以不显示指定变量类型，Scala会自动进行类型推到`
```
//字符串插值
val name = "jiangqihong"
val str = s"I am ${name}"
```

## 类型体系

## 函数
```
def funName(param:ParamType,param:ParamType):ReturnType = {

}
```
* if是一个表达式是有结果的
```
val cc = if(false) {
  "jiang"
} else {
  "qihong"
}
println(cc) //qihong

val cc = if(false) {
  "jiang"
}
println(cc) //()
```
* for循环
```
val list = List("jiang","qi","hong")
for(str <- list if（str.length()>3){
	println(str)
}
```

* try 表达式
``` 
    var re = try {
      Integer.parseInt("jiang");
    }catch {
      case _ => "_"
    } finally {
      println("finally")
    }
```

* match表达式
```
    val exp = 2
    var ca = exp match {
      case 1 => "noe";
      case 2 => "two";
      case _ => "_"
    }
    println(ca)
```

## 求值策略
* Call by Value(通常使用）
对函数实参求值，只求一次

* Call by Name（函数形参类型使用=>开头）
每次在函数体内被用到的时候求值

```
def foo(x:Int) = x //Call By value

def foo(x:=> Int) = x //Call By Name
```

## Scala函数与匿名函数

* Scala支持的操作
1. 把函数作为实参传递给另一个函数
2. 把函数作为返回值
3. 把函数作为变量
4. 把函数存储在数据结构中
`Scala中函数就像普通变量一样`

### 函数类型
Int => String b

### 高阶函数
* 把函数作为形参或者返回值的函数
```
def operate(fun:(Int, Int) => Int) = {
	fun(4,5)
}
```

### 匿名函数
```
(x:Int,y:String) =>{

}
```

## 柯里化
`柯里化函数：把具有多个参数的函数转换为一条函数链，每个节点上是单一参数`

```

```
## 递归函数

```
//计算n!
def factor(n:Int):Int = {
	if(n <= 0) {
		1;
	}else {
		n * factor(n - 1)
	}
}
```

## 尾递归函数
`尾递归函数中所有递归形式的调用都出现在函数的末尾，当编译器检测到一个函数是尾递归的时候，它就覆盖当前的活动记录而不是去栈中去创建一个新的栈帧。`

```
@annotation.tailrec
//递归函数
```

# 集合

## List[T]
```
val a = List(1,2,3)

//::连接操作符 （将前面的元素整体加入后后一个List中作为一个元素）
val b = 0 :: a

//Nil 表示空List 
val c = "a"::"b"::Nil
c:List[String] = List(a,b)

// ::: 第二种连接操作符（平等的连接两个List）
a:::c
List("1","2","3","a","b")

//访问
val a = List(1,2,3,4)

a.head 			//1
a.tail 			//List(2,3,4)
a.isEmpty 		//false

//filter取出a中的奇数
a.filter(x => x%2 == 1)


//字符串转字符数组的方法
"Jiangqihong hongqi".toList

//takeWhile方法（返回false时就停止）

```