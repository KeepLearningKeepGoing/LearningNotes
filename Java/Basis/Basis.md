## java语言结构、语法规则

[TOC]

------

### 语言结构

java语言的源码是由一个或多个编译单元（compilation unit）组成的，每个编译单元只能包含以下内容：

1. 一个程序包语句（package statement）
2. 若干导入语句（import statement）
3. 类的声明（class declarations）
4. 接口声明（interface declarations）

```
package xxx.xxx.xxx;		//程序包语句	

import xxx.xxx.xxx;			//导入包
...

public class 类名1 {		   //类声明
	//属性定义;
	...
	
	//方法定义
	方法名(){
      //方法体
	}
	...
}
class 类名2{
  ...
}
```

------

### 语言规则

#### 关键字

是被java赋予了特殊含义的单词

关键字列表：

| [abstract](http://baike.baidu.com/view/122814.htm) | [assert](http://baike.baidu.com/view/653925.htm) | [boolean](http://baike.baidu.com/view/1229867.htm) | break                                    | [byte](http://baike.baidu.com/view/44243.htm) |
| ---------------------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| case                                     | [catch](http://baike.baidu.com/view/1908899.htm) | [char](http://baike.baidu.com/view/1006519.htm) | [class](http://baike.baidu.com/view/76711.htm) | const                                    |
| continue                                 | [default](http://baike.baidu.com/view/1109945.htm) | [do](http://baike.baidu.com/view/126968.htm) | [double](http://baike.baidu.com/view/860124.htm) | [else](http://baike.baidu.com/view/2249891.htm) |
| [enum](http://baike.baidu.com/view/827326.htm) | [extends](http://baike.baidu.com/view/745501.htm) | [final](http://baike.baidu.com/view/2116821.htm) | [finally](http://baike.baidu.com/view/137061.htm) | float                                    |
| [for](http://baike.baidu.com/view/124948.htm) | goto                                     | [if](http://baike.baidu.com/view/127156.htm) | [implements](http://baike.baidu.com/view/2424683.htm) | [import](http://baike.baidu.com/view/2117022.htm) |
| [instanceof](http://baike.baidu.com/view/1989052.htm) | [int](http://baike.baidu.com/view/804413.htm) | [interface](http://baike.baidu.com/view/334756.htm) | long                                     | native                                   |
| new                                      | [package](http://baike.baidu.com/view/1006568.htm) | [private](http://baike.baidu.com/view/51615.htm) | [protected](http://baike.baidu.com/view/833512.htm) | [public](http://baike.baidu.com/view/1022383.htm) |
| [return](http://baike.baidu.com/view/1350512.htm) | [strictfp](http://baike.baidu.com/view/1866622.htm) | [short](http://baike.baidu.com/view/981206.htm) | [static](http://baike.baidu.com/view/536145.htm) | [super](http://baike.baidu.com/view/496937.htm) |
| [switch](http://baike.baidu.com/view/600161.htm) | [synchronized](http://baike.baidu.com/view/1207212.htm) | [this](http://baike.baidu.com/view/626297.htm) | [throw](http://baike.baidu.com/view/3019126.htm) | [throws](http://baike.baidu.com/item/throws) |
| [transient](http://baike.baidu.com/view/1698173.htm) | try                                      | [void](http://baike.baidu.com/view/1004734.htm) | [volatile](http://baike.baidu.com/view/608706.htm) | [while](http://baike.baidu.com/view/1455003.htm) |

关键字大致含义：

| **关键字**      | **含义**                                   |
| ------------ | ---------------------------------------- |
| abstract     | 表明类或者成员方法具有抽象属性                          |
| assert       | 用来进行程序调试                                 |
| boolean      | 基本数据类型之一，布尔类型                            |
| break        | 提前跳出一个块                                  |
| byte         | 基本数据类型之一，字节类型                            |
| case         | 用在switch语句之中，表示其中的一个分支                   |
| catch        | 用在异常处理中，用来捕捉异常                           |
| char         | 基本数据类型之一，字符类型                            |
| class        | 类                                        |
| const        | 保留关键字，没有具体含义                             |
| continue     | 回到一个块的开始处                                |
| default      | 默认，例如，用在switch语句中，表明一个默认的分支              |
| do           | 用在do-while循环结构中                          |
| double       | 基本数据类型之一，双精度浮点数类型                        |
| else         | 用在条件语句中，表明当条件不成立时的分支                     |
| enum         | 枚举                                       |
| extends      | 表明一个类型是另一个类型的子类型，这里常见的类型有类和接口            |
| final        | 用来说明最终属性，表明一个类不能派生出子类，或者成员方法不能被覆盖，或者成员域的值不能被改变 |
| finally      | 用于处理异常情况，用来声明一个基本肯定会被执行到的语句块             |
| float        | 基本数据类型之一，单精度浮点数类型                        |
| for          | 一种循环结构的引导词                               |
| goto         | 保留关键字，没有具体含义                             |
| if           | 条件语句的引导词                                 |
| implements   | 表明一个类实现了给定的接口                            |
| import       | 表明要访问指定的类或包                              |
| instanceof   | 用来测试一个对象是否是指定类型的实例对象                     |
| int          | 基本数据类型之一，整数类型                            |
| interface    | 接口                                       |
| long         | 基本数据类型之一，长整数类型                           |
| native       | 用来声明一个方法是由与计算机相关的语言（如C/C++/FORTRAN语言）实现的 |
| new          | 用来创建新实例对象                                |
| null         | 用来标识一个不确定的对象                             |
| package      | 包                                        |
| private      | 一种访问控制方式：私用模式                            |
| protected    | 一种访问控制方式：保护模式                            |
| public       | 一种访问控制方式：共用模式                            |
| return       | 从成员方法中返回数据                               |
| short        | 基本数据类型之一,短整数类型                           |
| static       | 表明具有静态属性                                 |
| strictfp     | 用来声明FP_strict（单精度或双精度浮点数）表达式遵循IEEE 754算术规范 |
| super        | 表明当前对象的父类型的引用或者父类型的构造方法                  |
| switch       | 分支语句结构的引导词                               |
| synchronized | 表明一段代码需要同步执行                             |
| this         | 指向当前实例对象的引用                              |
| throw        | 抛出一个异常                                   |
| throws       | 声明在当前定义的成员方法中所有需要抛出的异常                   |
| transient    | 声明不用序列化的成员域                              |
| try          | 尝试一个可能抛出异常的程序块                           |
| void         | 声明当前成员方法没有返回值                            |
| volatile     | 表明两个或者多个变量必须同步地发生变化                      |
| while        | 用在循环结构中                                  |

注意：

​	*1)main不是关键字，但是它是java程序默认的入口，因此也不可被用作其他用途*

​	*2)在写java程序时，一个java文件中只能存在一个公共类，也就是一个.java文件中只能有一个类被pulbic修饰*



------

#### 标识符

在程序中自定义的一些名称（类，方法，变量，常量等的名称）

可由数字、字母、_、$组成。

​	1）不能以数字开头；

        2）不能使用关键字和保留字；如main，goto等。

        3）java严格区分大小写。最好不要使用大小写的不同来区别不同的标示符，容易出现混乱。

java命名约定：

​	1)包名：多单词组成时所有字母都小写。如： xxxyyyzzz
​	2)类名接口名：多单词组成时，所有单词的首字母大写。如： XxxYyyZzz
​	3)变量名和函数名：多单词组成时，第一个单词首字母小写，第二个单词开始每个单词首字母大写。如：		xxxYyyZzz
​	4)常量名：所有字母都大写。多单词时每个单词用下划线连接。如： XXX_YYY



------

#### 数据类型

##### 数据类型分类

在java源代码中，每个变量都必须声明一种类型（type）。有两种类型：primitive type和reference type。引用类型引用对象（reference to object），而基本类型直接包含值（directly contain value）。因此，Java数据类型（type）可以分为两大类：基本类型（primitive types）和引用类型（reference types）。primitive types 包括boolean类型以及数值类型（numeric types）。numeric types又分为整型（integer types）和浮点型（floating-point type）。整型有5种：byte short int long char(char本质上是一种特殊的int)。浮点类型有float和double。关系整理一下如下图：



注意：

​	*1）整数默认：int，浮点数默认：double*

​	*2）java语言中同一种数据类型在任何平台上占据的内存长度都是相同的。*

​	*3）java语言的每种数据类型都对应着一个缺省值*

另外八种基本数据类型有对应的封装类：Integer ,Double ,Long ,Float, Short,Byte,Character,Boolean

------

##### 常量

“常量”在程序运行时，不会被修改的量。换言之，常量虽然是为了硬件、软件、编程语言服务，但是它并不是因为硬件、软件、编程语言而引入。

```
Java 常量，有2种意思，我分别说明：
第1种意思，就是一个值，这个值本身，我们可以叫它常量，举几个例子：
整型常量: 123
实型常量：3.14
字符常量: 'a'
逻辑常量：true、false
字符串常量："helloworld

还有另一种：
第2种意思，表示不可变的变量，这种也叫常量，从语法上来讲也就是，加上final，使用final关键字来修饰某个变量，然后只要赋值之后，就不能改变了，就不能再次被赋值了，据个例子：
final int i = 0;
那么这个i的值是绝对不能再被更改了，只能是0，所以说是 不可变的变量，这句话看似矛盾，其实不矛盾，这句话这样理解：
i就是一个int类型的变量，变量本身是可变的（可被更改值），但是现在加了final，所以不可变了，所以是不可变的变量。
```



##### 变量

变量就是在程序运行中值可变的量（本身代表保持数据的内存单元）。在使用变量前需要先定义

在Java中，所有的变量必须先声明才能使用它们。变量声明的基本形式如下：

```
type identifier [ = value][, identifier [= value] ...] ;
```

 type 是Java 的数据类型之一。该标识符是该变量的名称。申报指定类型的多个变量，用逗号分隔的列表。

下面是各种类型的变量声明的几个例子。需要注意的是它们可能也包括初始化。

```
int a, b, c;         // declares three ints, a, b, and c.
int d = 3, e, f = 5; // declares three more ints, initializing
                     // d and f.
byte z = 22;         // initializes z.
double pi = 3.14159; // declares an approximation of pi.
char x = 'x';        // the variable x has the value 'x'.
```

本章将解释各种变量类型Java语言提供。有三种类型的变量在Java中：

- 局部变量
- 实例变量
- 类/静态变量

**局部变量：**

- 局部变量的方法，构造函数或块中声明。
- 创建局部变量的方法，构造函数或块时进入，一旦退出方法，构造函数或块中的变量将被销毁。
- 访问修饰符不能用于局部变量。
- 局部变量是可见的，只有内声明的方法，构造函数或块。
- 局部变量在堆栈级别内部实现。
- 在这里对局部变量没有默认值，因此局部变量应该声明和初始值应在第一次使用前分配。

**实例：**

在这里，age 是一个局部变量。这是定义里面 pupAge()  方法，其范围仅限于该方法。

```
public class Test{ 
   public void pupAge(){
      int age = 0;
      age = age + 7;
      System.out.println("Puppy age is : " + age);
   }
   
   public static void main(String args[]){
      Test test = new Test();
      test.pupAge();
   }
}
```

这将产生以下结果：

```
Puppy age is: 7

```



下面的示例使用 age 没有初始化它，所以它会在编译时给出错误信息。

```
public class Test{ 
   public void pupAge(){
      int age;
      age = age + 7;
      System.out.println("Puppy age is : " + age);
   }
   
   public static void main(String args[]){
      Test test = new Test();
      test.pupAge();
   }
}
```

编译它，这将产生以下错误：

```
Test.java:4:variable number might not have been initialized
age = age + 7;
         ^
1 error

```

**实例变量：**

- 实例变量在类中声明，但在方法的外面，构造函数或任何块。
- 当空间分配给某个对象在堆中，插槽为每个实例变量创建值。
- 实例变量认为必须由一个以上的方法，构造函数或块，或一个对象的状态的关键部分必须出现在整个类中引用的值。
- 实例变量可以在使用前或后级的级别声明。
- 访问修饰符可以给出实例变量。
- 实例变量对于所有方法，构造函数和块在类中可见。通常，建议，使这些变量私有（接入层）。然而能见度子类可以给这些变量与使用访问修饰符。
- 实例变量有默认值。对于数字的默认值是0，为布尔值是false和对象引用为null。值可以在声明或构造函数中分配。
- 实例变量可以直接通过调用变量名的类的内部访问。然而在静态方法和不同的类（当实例变量被赋予访问）应使用完全限定名调用 ObjectReference.VariableName.

**例子：**

```
import java.io.*;

public class Employee{
   // this instance variable is visible for any child class.
   public String name;
   
   // salary  variable is visible in Employee class only.
   private double salary;
   
   // The name variable is assigned in the constructor. 
   public Employee (String empName){
      name = empName;
   }

   // The salary variable is assigned a value.
   public void setSalary(double empSal){
      salary = empSal;
   }
   
   // This method prints the employee details.
   public void printEmp(){
      System.out.println("name  : " + name );
      System.out.println("salary :" + salary);
   }

   public static void main(String args[]){
      Employee empOne = new Employee("Ransika");
      empOne.setSalary(1000);
      empOne.printEmp();
   }
}
```

这将产生以下结果:

```
name  : Ransika
salary :1000.0

```

**类/静态变量：**

- 类变量也称为静态变量在类的static关键字声明的，但在方法外面，构造函数或块。
- 每个类变量只有一个副本，不管有多少对象从它被创建。
- 静态变量很少使用不是被声明为常量等。常量是被声明为公共/私营，最终和静态变量。常量变量从来没有从他们的初始值改变。
- 静态变量被存储在静态存储器中。这是罕见的使用静态变量以外声明为final，用作公共或私有常数。
- 在程序启动时的静态变量被创建，在程序停止销毁。
- 能见度类似于实例变量。然而，大多数静态变量声明为 public，因为它们必须可用于类的使用者。
- 默认值是相同的实例变量。对于数字，默认值是0;为布尔值，它是假的，和对象引用，它为null。值可以在声明或构造函数中分配。另外值可以在特殊的静态初始化块进行分配。
- 静态变量可以通过调用与类名来访问。 ClassName.VariableName.
- 当定义的变量为 public static final ，那么变量的名称（常量）都是大写。如果静态变量是不公开的和最终的命名语法是相同的实例变量和局部变量。

**例子：**

```
import java.io.*;

public class Employee{
   // salary  variable is a private static variable
   private static double salary;

   // DEPARTMENT is a constant
   public static final String DEPARTMENT = "Development ";

   public static void main(String args[]){
      salary = 1000;
      System.out.println(DEPARTMENT+"average salary:"+salary);
   }
}
```

这将产生以下结果:

```
Development average salary:1000

```

注意：如果变量是从外部类访问的常数应被访问 Employee.DEPARTMENT



------

#####  数据类型转换

**一、基本数据类型的类型转换**

基本数据类型中，布尔类型boolean占有一个字节，由于其本身所代码的特殊含义，**boolean类型与其他基本类型不能进行类型的转换（既不能进行自动类型的提升，也不能强制类型转换）， 否则，将编译出错**。

1.基本数据类型中数值类型的自动类型提升

数值类型在内存中直接存储其本身的值，对于不同的数值类型，内存中会分配相应的大小去存储。如:byte类型的变量占用8位，int类型变量占用32位等。相应的，不同的数值类型会有与其存储空间相匹配的取值范围。具体如下所示：

![img](Images/292112153618743.png)

图中依次表示了各数值类型的字节数和相应的取值范围。**在Java中，整数类型（byte/short/int/long）中，对于未声明数据类型的整形，其默认类型为int型。在浮点类型（float/double）中，对于未声明数据类型的浮点型，默认为double型。**

接下来我们看看如下一个较为经典例子：

```
 1 package com.corn.testcast;
 2 
 3 
 4 public class TestCast {
 5     
 6     public static void main(String[] args) {
 7         byte a = 1000;   // 编译出错 Type mismatch: cannot convert from int to byte
 8         float b = 1.5;   // 编译出错 Type mismatch: cannot convert from double to float
 9         byte c = 3;      // 编译正确
10     }
11     
12 }
```

是不是有点奇怪？按照上面的思路去理解，将一个int型的1000赋给一个byte型的变量a，编译出错，提示"cannot convert from int to byte"是对的，1.5默认是一个double型，将一个double类型的值赋给一个float类型，编译出错，这也是对的。但是最后一句：将一个int型的3赋给一个byte型的变量c，居然编译正确，这是为什么呢？

原因在于：jvm在编译过程中，对于默认为int类型的数值时，当赋给一个比int型数值范围小的数值类型变量（在此统一称为数值类型k，k可以是byte/char/short类型），会进行判断，如果此int型数值超过数值类型k，那么会直接编译出错。因为你将一个超过了范围的数值赋给类型为k的变量，k装不下嘛，你有没有进行强制类型转换，当然报错了。但是如果此int型数值尚在数值类型k范围内，jvm会自定进行一次隐式类型转换，将此int型数值转换成类型k。如图中的虚线箭头。这一点有点特别，需要稍微注意下。

**在其他情况下，当将一个数值范围小的类型赋给一个数值范围大的数值型变量，jvm在编译过程中俊将此数值的类型进行了自动提升。在数值类型的自动类型提升过程中，数值精度至少不应该降低（整型保持不变，float->double精度将变高）。**

```
 1 package com.corn.testcast;
 2 
 3 public class TestCast {
 4 
 5     public static void main(String[] args) {
 6         long a = 10000000000; //编译出错: The literal 10000000000 of type int is out of range 
 7         long b = 10000000000L; //编译正确
 8         int c = 1000;
 9         long d = c;
10         float e = 1.5F;
11         double f = e;
12     }
13 
14 }
```

如上：定义long类型的a变量时，将编译出错，原因在于10000000000默认是int类型，同时int类型的数值范围是-2^31 ~ 2^31-1，因此，10000000000已经超过此范围内的最大值，故而其自身已经编译出错，更谈不上赋值给long型变量a了。

此时，若想正确赋值，改变10000000000自身默认的类型即可，直接改成10000000000L即可将其自身类型定义为long型。此时再赋值编译正确。

将值为1000的int型变量c赋值给long型变量d，按照上文所述，此时直接发生了自动类型提升， 编译正确。同理，将e赋给f编译正确。

接下来，还有一个地方需要注意的是：char型其本身是unsigned型，同时具有两个字节，其数值范围是0 ~ 2^16-1，因为，这直接导致**byte型不能自动类型提升到char，char和short直接也不会发生自动类型提升（因为负数的问题）**，同时，byte当然可以直接提升到short型。

 

2.基本数据类型中的数值类型强制转换

 当我们需要将数值范围较大的数值类型赋给数值范围较小的数值类型变量时，由于**此时可能会丢失精度**（1讲到的从int到k型的隐式转换除外），因此，需要人为进行转换。我们称之为强制类型转换。

首先我们看一下如下的例子：

```
 1 package com.corn.testcast;
 2 
 3 public class TestCast {
 4 
 5     public static void main(String[] args) {
 6         byte p = 3; // 编译正确:int到byte编译过程中发生隐式类型转换
 7         int  a = 3;
 8         byte b = a; // 编译出错：cannot convert from int to byte
 9         byte c = (byte) a; // 编译正确
10         float d = (float) 4.0;
11     }
12 
13 }
```



byte p =3；编译正确在1中已经进行了解释。接下来将一个值为3的int型变量a赋值给byte型变量b，发生编译错误。这两种写法之间有什么区别呢？

**区别在于前者3是直接量，编译期间可以直接进行判定，后者a为一变量，需要到运行期间才能确定，也就是说，编译期间为以防万一，当然不可能编译通过了。****此时，需要进行强制类型转换。**

强制类型转换所带来的结果是可能会丢失精度，如果此数值尚在范围较小的类型数值范围内，对于整型变量精度不变，但如果超出范围较小的类型数值范围内，很可能出现一些意外情况。

如下经典例子：

```
 1 package com.corn.testcast;
 2 
 3 public class TestCast {
 4 
 5     public static void main(String[] args) {
 6         int a = 233;
 7         byte b = (byte) a;
 8         System.out.println("b:" + b);  // 输出：-23
 9     }
10 
11 }
```

为什么结果是-23？需要从最根本的二进制存储考虑。

233的二进制表示为：24位0 + 11101001，byte型只有8位，于是从高位开始舍弃，截断后剩下：11101001，由于二进制最高位1表示负数，0表示正数，其相应的负数为-23。

 

3.进行数学运算时的数据类型自动提升与可能需要的强制类型转换

 如下代码：

```
 1 package com.corn.testcast;
 2 
 3 public class TestCast {
 4 
 5     public static void main(String[] args) {
 6         byte a = 3 + 5; // 编译正常 编译成 3+5直接变为8
 7         int b = 3, c = 5;
 8         byte d = b + c; // 编译错误：cannot convert from int to byte
 9 
10         byte e = 10, f = 11;
11         byte g = e + f; // 编译错误 +直接将10和11类型提升为了int
12         byte h = (byte) (e + f);  //编译正确
13     }
14 
15 }
```



**当进行数学运算时，数据类型会自动发生提升到运算符左右之较大者**，以此类推。当将最后的运算结果赋值给指定的数值类型时，可能需要进行强制类型转换。

**二、引用类型的类型转换/造型**

   编写java程序时，引用变量只能调用它编译时类型的方法，而不能调用它运行时类型的方法，即使它实际所引用对象确实包含该方法。

引用类型之间的转换只能把一个父类变量转换成子类类型。如果试图把一个父类实例转换成子类类型，则必须这个对象实际上是子类实例才行（即编译时类型为父类类型，运行时为子类类型），否则会发生ClassCastException异常。

```
public class Test {

     public static void main(String[] args) {

         Object o1=new Object();            //o1的编译类型是Object，实际类型是Object，是string的父类，所以可以强制转换成string类

         String o2=new String();             //o2的编译类型是String，实际类型是String

         Object o3=new String();            //o3的编译类型是Object，实际类型是String

         o2=(String)o1;                          //编译时没错，运行时有错(o2和o1的实际类型不一样)

        o2=(String)o3;                           //编译时没错，运行时没错（o2和o3的实际类型一样）

        o3=(String)o1;                          //编译时没错，运行时有错(o1和o3的实际类型不一样)

        o3=o1;                                     //编译时没错，运行时也没错

     }

}
```

    在强制转换之前可以先用instanceof来判断是否可以成功转换，instanceof 的前一个操作数通常是引用类型变量，后一个通常是类。它用于判断前面的对象是否是后面的类或其子类的实例，如果是返回true否则返回false。instanceof前面的操作数的编译类型要么与后面的类相同，要么是后面类的父类，否则会发生编译错误。

```
public class Test {

 public static void main(String[] args) {

   　 Object a1=new String();

 　　System.out.println(a1 instanceof Object);     　　//返回true

 　　System.out.println(a1 instanceof String);　　　　//返回true

 　　System.out.println(a1 instanceof Math);　　　　//返回false，math是object的子类

 　　System.out.println(a1 instanceof Comparable);　　//返回true，string实现了comparable接口

 　　String a2=new String();

　　 System.out.println(a2 instanceof Math );　　　　//编译错误因为a2不是math类，也不是math的父类

 }

}
```

------

