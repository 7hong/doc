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

