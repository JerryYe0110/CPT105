#　基于Java SE5　现在可能有修改
[toc]
# 1. 对象导论
## 

# 2. 一切都是对象

```java
String s;
```
这里创建的是引用，并不是对象．

引用需要有对象，通常使用**new** 来产生新的对象．
``` java
String s = new String("asdf");
```
这表示新建一个初始字符串的对象，并用ｓ引用

对象需要内存存放，有５个不同的地方可以存放内存
1. **寄存器** 寄存器在处理器内部，速度最快，位置稀少，java不能直接控制
2. **堆栈** 位于RAM通过*堆栈指针* 可以从处理器获得直接支持．如果指针向下移动，则分配新内存，向上移动就擦除并释放呢些内存．java在创建程序时，必须知道所有存在内存里的项的生命周期．这限制了程序的灵活性，java对象并不存在这里．
3. **堆** 也位于RAM中，用于存放对象，其有更大的灵活性，不需要知道生命周期．new可以直接创建对象在堆中．但堆对存储的分配，清理比堆栈需要更多时间．
4. **常量存储** 常量一般直接存放在程序代码内部，他们不会改变．在嵌入式系统中，常量会和其他部分分离，所以可能存储在ROM中．
5. **非RAM存储** 数据在程序外，完全不受到程序控制，独立于程序．基础例子是*流对象* 和*持久化对象*．流对象转化为字节流，通常发送给另外的机器，持久化对象被存储在磁盘中，程序终止也不会有影响．这种存储的技巧是：对象成为存储为其他事物，使用时可以恢复为基于RAM的对象．java有轻量级持久化支持，JDBC和Hibernate等可以读取和存储数据库内对象 
   
存储特例
基本类型．基本类型通常较小并且简单，存储在堆中并不是很好，所以不用new方法．这些类型通常是直接创建而非引用的变量，这些变量直接存储值，并且存储在堆栈中．

因为基本类型拥有包装器类，所以可以在堆中创建非基本对象，用来表示基本类型.
``` java
char c = 'x';
Character ch = 'x';
//也可以这么用
Character ch = new Character('x');
// Java SE5 会自动将基础类型转换为包装器类型：
Character ch = 'x';
//并可以反向转换：
char c = ch;
```
java 提供了两个高精度类型: **BigInteger** 和**BigDecimal** 大体上都属于包装器类，没有对应的基本类型．这些类包含的方法和**Int** 或**float** 的操作相似，但是因为是方法，所以速度相较会慢

java　数组相较Ｃ和Ｃ＋＋更加安全，但起会有更大的内存开销和运行时自带的下标检查

作用域在java中由花括号决定
```java
{
  int x = 12;
  {
    int x = 96; 
  }
}

```
以上做法并不被java所允许java不会将较大作用域的变量隐藏＞

与基本类型不同,当你用new创建Java对象时，这个对象可以存活在作用域外．
``` java
{
String s = new String("a string");
}
```
引用s在花括号结束时，就已经消失了，但是s指向的String对象还在占据内存空间，但在这里，花括号消失了，我们已经无法访问这个对象了．

java 的方法的基本形式
```java
ReturnType methodName(/* Argument list */){
  /* Method body */
  return ReturnType;
}
```
方法名和参数列表（合起来称作方法签名）唯一识别函数的方法
一般来说，方法只能通过对象来调用，

```java
int x = a.f();
```
这种调用行为通常被称作*发送信息给对象* ,在上面的例子中嗯，消息是f(), 对象是a.

通常来说，创建类只是在描述那个类的外观和行为，实际上并没有获得任何对象（除非用new来创建那个类的对象）但这个方法是无法实现两种情况的，
1. 只想为特定域分配单一存储空间，但不去考虑需要创建多个对象.甚至可以不创建对象
2. 希望某个方法不与包含他的类的任何对象关联起来，也就是说，即使没有创建对象，也能调用这个方法

**static** 可以满足这些需求，当声明一个事物是static的时候，意味着这个域或者方法不会和任何包含他的联系起来，即使没这个类的对象，也能调用这个方法或者访问这个域

```java

class StaticTest{
  static int i = 47;
}

StaticTest st1 = new StaticTest();
StaticTest st2 = new StaticTest();
StaticTest.i++;
```
在这个，st1.i和st2.i只有一个存储空间，意味这一个改变了，另一个也会改变．，也可以以上面最后一行的方式直接引用，不需要对象．使用类名是对static对象首选的应用方式，这个可以让编译器进行优化．类似的还有静态方法（static 方法）

# 3.操作符
赋值符
对一个对象＂赋值＂的时候，情况和对一个基本类型赋值并不相同．对一个对象操作，我们实际上在操作对对象的引用．将一个＂对象＂赋值给另一个＂对象＂，实际上是在将一个引用，从一个地方赋值到另一个地方．这意味着，两个＂对象＂最后都指向同一个对象.这会导致这俩个＂对象＂并不独立，我通过某一个引用，改变了指向的呢个对象之后，另一个拥有相同引用的也会改变.这种现象被称为别名问题．此外，在将一个对象换递给方法时，也会产生别名问题，下面为以个实际的例子
```java
class Letter{
  char c;
}

public class PassObject{
  static void f (Letter y){
  y.c = 'z';
  }
  public static void main(String[] args){
  Letter x = new Letter();
  x.c = 'a';
  print("1: x.c: " + x.c);
  f(x);
  print("2: x.c: " + x.c);
  }
  
}

/* Output:
1: x.c: a
2: x.c: z
*/

```

一元减号意为符号去反，一元加号为了与其对应，实际没有任何作用，最多只是将较小类型的操作数提升至int

三元操作符　if-else
```java
boolean-exp ? value0 : value1
```
如果前面为真，则计算value0的值，为假，则计算value1的值

# 4.控制执行流程



