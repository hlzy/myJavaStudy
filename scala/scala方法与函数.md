## Scala几种函数形式

### 1. 匿名函数

scala中匿名函数语法，箭头左边是参数列表，右边是函数体

```scala
var inc = (x:Int) => x+1
```



匿名函数可以复制给变量

```scala
var addInt = (x:Int,y:Int) => x+y //匿名函数
def myFun(f:(Int,Int)=>Int,x:Int,y:Int) = f(x,y) //f为匿名函数接收两个Int，返回一个Int
myFun(addInt,1,2) 
```