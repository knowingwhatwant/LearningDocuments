# 1.C++基础

## C++关键字

## 命名空间

1.普通定义

```c++
//普通，可以定义变量，也可以函数
namespace first
{
    int a=1;
    int b=2;
    int Add(int a,int b)
    {
        return a+b;
    }
}
```

2.嵌套

```c++
//嵌套
namespace second
{
    int a=1;
    int b=2;
    namespace third
    {
        int c=0;
        int Sub(int a,int b)
        {
            return a-b;
        }
    }
}
```

3.同一工程中允许多个相同名称的命名空间，编译器最后会合成同一个命名空间中

## 命名空间使用

有点像结构体，命名空间中定义的变量不能直接在外面使用

### 1.符号`::`引用

符号`::`在C++中叫做作用域限定符，我们通过`命名空间名称::命名空间成员`便可以访问到命名空间中相应的成员

```c++
#include<iostream>
#include<stdio.h>
namespace first
{
    int a=1;
    int b=2;
    int Add(int a,int b)
    {
        return a+b;
    }
}

int main()
{
    printf("%d\n",first::a);//1
    return 0;
}
```

### 2.`using namespace`引入

```c++
using namespace first;
int main()
{
    printf("%d\n",a);//1
    return 0;
}
```

**弊端**

要是空间中的某些变量名与已存在的变量名重复，会导致命名的污染

### 3.使用`using`将namespace中成员引入

```c++
using first::a;
int main()
{
    printf("%d\n",a);//1
    return 0;
}
```

可以防止命名的污染，因为只引入部分。

## 输入输出

在C++中使用cin和cout，需要包含**头文件iostream**以及**std标准命名空间**

```c++
#include<iostream>
using namespace std;
int main()
{
    cout<<"hello world!"<<endl;
    return 0;
}
//注意：endl，这其中的l不是阿拉伯数字1，而是26个英文字母的l，它的作用相当于换行。
```

C++的输入输出方式与C语言更加方便，因为C++的输入输出不需要控制格式。

```c++
#include<iostream>
using namespace std;

int main()
{
    int a=1;
    float b=1.2;
    double c=2.11;
    char arr[10]={0};
    char d[]="abcdef";
    cin >>arr;
    cout<<arr<<endl;//"hello"
    cout<<a<<endl;
    cout<<b<<endl;
    cout<<c<<endl;
    cout<<d<<endl;
    return 0;
}
//cin是以遇到空格，tab或者换行符作为分隔符
//所以输入"hello world"会只取"hello"

```

## 缺省参数

声明或定义函数为函数参数指定一个默认值，调用函数时，若没有指定实参则采用默认值。

```c++
#include<iostream>
using namespace std;

void func(int a=0)//给a一个默认值
{
    cout<<a<<endl;
}
int main()
{
    func(10);
    func();//哈哈哈c就不行
    return 0;
}

```

## 全缺省

函数的全部形参都设置为缺省参数

```c++
#inlcude<iostream>
using namespace std;

void func(int a=0;,int b=1,int c=2)
{
    cout<<"a="<<a<endl;
    cout<<b<<endl;
    cout<<c<<endl;
}

int main()
{
    func();
    return 0;
}
```

## 半缺省参数

1、半缺省参数必须从右往左依次给出，不能间隔着给。

> 有点类似我之前问老师的那个printf的问题

2、缺省参数不能在函数声明和定义中同时出现

> 如果同时给出，又不同，则不确定到底用哪个

3、缺省值必须是常量或者全局变量

```c++
//全局变量的情况
int x=3;
void func(int a=0;,int b=1,int c=x)
{
    cout<<"a="<<a<endl;
    cout<<b<<endl;
    cout<<c<<endl;
}
```

## 函数重载

C++允许在**同一作用域**中声明几个功能类似的**同名函数**，同名函数的**形参列表（参数个数 或 类型 或 顺序）必须不同**常用来处理实现功能类似数据类型不同的问题

若仅仅只有返回值不同，其他都相同，则不构成函数重载

```c++
#include <iostream>
using namespace std;

int Add(int x, int y)
{
	return x + y;
}

double Add(double x, double y)
{
	return x + y;
}
int main()
{
	cout << Add(0,1) << endl;//打印0+1的结果
	cout << Add(1.1,2.2) << endl;//打印1.1+2.2的结果
	return 0;
}
```

## 函数重载原理

extern "C"

有时候在C++工程中可能需要将某些函数按照C的风格来编译，在函数前加extern “C”，意思是告诉编译器，
将该函数按照C语言规则来编译。比如：tcmalloc是google用C++实现的一个项目，他提供tcmallc()和tcfree
两个接口来使用，但如果是C项目就没办法使用，那么他就使用extern “C”来解决。

## 引用&

引用不是新定义一个变量，**而是给已存在变量取了一个别名**，编译器不会为引用变量开辟内存空间，它和它引用的变量**共用同一块内存空间**。

`类型& 引用变量名(对象名) = 引用实体；`

**引用类型必须和引用实体是同种类型的**

```c++
#include<iostream>
using namespace std;

int main()
{
    int a=1;
    int &b=a;//a的别名为b，b的类型为int
    //b和a共用同一块内存空间，所以改变b就相当于改变了a
    cout << a << endl;
	cout << b << endl;
	b = 3; 
	cout << b << endl;
	cout << a << endl;
    return 0;
}
```

## 引用的特征

### 1. 引用在定义时候必须初始化

```c++
//正确
int a=1;
int &b=a;
//错误
int a=1;
int &b;//定义时候未初始化
b=a;
```

### 2.一个变量可以有多个引用

```c
int a=10;
int &b=a;
int &c=a;
int &d=a;
```

### 3.引用一旦引用了一个实体，就不能再引用其他实体

````c++
int a =10;
int &b=a;//b作为a的引用
int c=20;
b=c;//error,b不能脚踏两条船
//实际效果是a的值变成了20
````



## 常引用

对常量进行引用

引用类型必须和引用实体是同种类型的。但是仅仅是同种类型，还不能保证能够引用成功，这儿我们还要注意可否可以修改的问题。

```c++
void TestConstRef()
{
 const int a = 10;
 //int& ra = a; // 该语句编译时会出错，a为常量
 const int& ra = a;
 // int& b = 10; // 该语句编译时会出错，b为常量
 const int& b = 10;
 double d = 12.34;
 //int& rd = d; // 该语句编译时会出错，类型不同
 const int& rd = d; 
 }
//这里的a，b，d都是常量，常量是不可以被修改的，但是如果你用int&ra等这样来引用a的话，那么引用的这个a是可以被修改的，因此会出问题。
```

将不可修改的量用可读可写的量来引用是不可以的，但是反过来是可以的，将可读可写的量用只可读的量来引用是可以的

```c++
#include<iostream>
using namespace std;
int main()
{
	int a = 10;
	const double&ra = a;//ra相当于常量，不随着a变化
    cout<<ra<<endl;//10
    a=20;
    cout<<ra<<endl;//10
    cout<<a<<endl;//20
}

```

## 引用的使用场景

### 1.引用做参数

C语言函数传参传递值或者地址，C++中可以用引用&来代替传参数

这里a和b是传入实参的引用，我们将a和b的值交换，就相当于将传入的两个实参交换了

```c++
//交换函数
void Swap(int& a, int& b)
{
	int tmp = a;
	a = b;
	b = tmp;
}
```

### 2.引用做返回值

返回数据不能是函数内部创建的普通局部变量，因为在函数内部定义的普通的局部变量会随着函数调用的结束而被销毁。

返回的数据必须是被static修饰或者是动态开辟的或者是全局变量等不会随着函数调用的结束而被销毁的数据。

```c++
#include<iostream>
using namespace std;

int& Add(int a,int b)
{
    int c=a+b;//c就是这样定义，前面没有&
    return c;
}
//修改后
int& Add1(int a, int b)
{
	static int c = a + b;
	return c;
}

int main()
{
    int& ans=Add(1,3);
    Add(3,4);//再次运行，c重新初始化
    cout<<and<<endl;//7
	
    int&x=Add(1,3);
    printf("hehe\n");
    count << x << endl;//随机值
    //因为我的x只是引用，运行printf后，原本c所在的地方的内存空间中的值改变了

    int& y = Add1(1,2);//初始化
	Add1(3, 4);//已经初始化了，此次运行不起作用
	cout << y << endl;//3
    //问，那我还能再改变Add1()的返回值吗
    return 0;
}
```

如果函数返回时，出了函数作用域，返回对象还未还给系统，则可以使用引用返回；如果已经还给系统了，则必须使用传值返回。

```c++
int& Add(int a, int b)
{
    int c=a+b;  //出了函数作用域，c不在，回给了系统
	return c;
}

int& Add(int a,int b)
{
    static c=a+b;  //出了函数作用域，c还在，可以用引用返回
    return c;
}
```

传值的过程中会产生一个拷贝，而传引用的过程中不会，其实在做函数参数时也具有这个特点。

```c++
//int temp=n;
int Add1(int a,int b)
{
    static int c=a+b;
    return c;
}
//int& temp=n;
int& Add2(int a,int b)
{
    static int c=a+b;
    return c;
}
int main()
{
    int a=1;
    int b=2;
    int&c=Add2(a,b);
    const int&d=Add1(a,b);//传过来的temp是一个常量，不可修改
    
    
    return 0;
}
```

### 引用和指针的区别

在语法概念上引用就是一个别名，没有独立空间，和其引用实体共用同一块空间

```c++
int main()
{
 int a = 10;
 int& ra = a;
 
 cout<<"&a = "<<&a<<endl;
 cout<<"&ra = "<<&ra<<endl;
 return 0; }

```

在底层实现上实际是有空间的，因为引用是按照指针方式来实现的。

```c++
int main()
{
 int a = 10;
 
 int& ra = a;
 ra = 20;
 
 int* pa = &a;
 *pa = 20;
 
 return 0; }

```

引用和指针的区别
1、引用在定义时必须初始化，指针没有要求。（指针也还是要初始化）
2、引用在初始化时引用一个实体后，就不能再引用其他实体，而指针可以在任何时候指向任何一个同类型实体。
3、没有NULL引用，但有NULL指针。
4、在sizeof中的含义不同：引用的结果为引用类型的大小，但指针始终是地址空间所占字节个数（32位平台下占4个字节）。
5、引用进行自增操作就相当于实体增加1，而指针进行自增操作是指针向后偏移一个类型的大小。
6、有多级指针，但是没有多级引用。
7、访问实体的方式不同，指针需要显示解引用，而引用是编译器自己处理。
8、引用比指针使用起来相对更安全。

## 内联函数

> 以`inline`修饰的为内联函数，编译时C++编译器会在**调用内联函数的地方展开**，没有函数压栈的开销，
> 内联函数提升程序运行的效率

**特性**

> 1.inline是一种以空间换时间的做法，省去调用函数额开销。所以代码很长/递归的函数不适宜
> 使用作为内联函数。
> 2.inline对于编译器而言只是一个建议，编译器会自动优化，如果定义为inline的函数体内代码比较长/递归等
> 等，编译器优化时会忽略掉内联。
> 3.inline不建议声明和定义分离，分离会导致链接错误。因为inline被展开，就没有函数地址了，链接就会
> 找不到。

```c++
//对上述第三点
//F.h
#include <iostream>
using namespace std;

inline void f(int i);//内联函数声明和定义最好写在一起

// F.cpp
#include "F.h"
void f(int i) {
	cout << i << endl;
}

// main.cpp
#include "F.h"
int main()
{
	f(10);
	return 0;
}
// 链接错误：main.obj : error LNK2019: 无法解析的外部符号 "void __cdecl f(int)" (?
// f@@YAXH@Z)，该符号在函数 _main 中被引用

```



## c++有哪些技术可以代替宏

>1. 常量定义 换用const
>2. 函数定义 换用内联函数

## auto关键字(C++11)

>在早期的C/C++中auto的含义是：使用auto修饰的变量是具有自动存储器的局部变量;
>在C++11中，标准委员会赋予了auto全新的含义：auto不再是一个存储类型指示符，而是作为一个新的类型指示符来指示编译器，auto声明的变量必须由编译器在编译时期推导而得

auto型变量定义时必须赋值，此时变量自动转换成对应的赋值类型

```c++
#include<iostream>
using namespace std;
int TestAuto()
{
	return 10;
}
int main()
{
	int a = 10;
	auto b = a;
	auto c = 'a';
	auto d = TestAuto();
//打印出的是类型名
	cout << typeid(b).name() << endl;//int
	cout << typeid(c).name() << endl;//char
	cout << typeid(d).name() << endl;//int

	cout << a << endl;
	cout << b<< endl;
	cout << c << endl;
	cout << d << endl;

	//auto e; 无法通过编译，使用auto定义变量时必须对其进行初始化
	return 0;
}

```

## auto的使用细则

### 1.auto与指针和引用结合起来使用

> 用auto声明指针类型时，用auto和auto*没有任何区别，但用auto声明引用类型时则必须加&

```c++
#include <iostream>
using namespace std;
int main()
{
	int a = 10;
	auto b = &a;   //自动推导出b的类型为int*
	auto* c = &a;  //自动推导出c的类型为int*
	auto& d = a;   //自动推导出d的类型为int
	//打印变量b,c,d的类型
	cout << typeid(b).name() << endl;//打印结果为int*
	cout << typeid(c).name() << endl;//打印结果为int*
	cout << typeid(d).name() << endl;//打印结果为int
	return 0;
}
```

> 用auto声明引用时必须加&，否则创建的只是与实体类型相同的普通变量，只不过将其换了个姓名而已。

```c++
int main()
{
    int a=10;
    auto b=a;//只是名字不同
    auto&c=a;//正确的引用
    return 0;
}
```

### 2.在同一行定义多个变量

>当在同一行声明多个变量时，这些变量必须是相同的类型，否则编译器将会报错，因为编译器实际只对第一个类型进行推导，然后用推导出来的类型定义其他变量。

```c++
void TestAuto()
{
 auto a = 1, b = 2; 
 auto c = 3, d = 4.0; // 该行代码会编译失败，因为c和d的初始化表达式类型不同
}
```

## auto不能推导的场景

### 1.auto做为函数的参数

```c++
// 此处代码编译失败，auto不能作为形参类型，因为编译器无法对a的实际类型进行推导
void TestAuto(auto a)
{}

```

### 2.auto不能直接用来声明数组

```c++
void TestAuto()
{
 int a[] = {1,2,3};
 auto b[] = {4，5，6};
}
```

## 基于范围的for循环（C++11)

### 基于范围的for循环（C++11)

> 在C++98中如果要遍历一个数组，可以按照以下方式进行：

```c++
void TestFor()
{
 int array[] = { 1, 2, 3, 4, 5 };
 //将数组所有元素乘以2
 for (int i = 0; i < sizeof(array) / sizeof(array[0]); ++i)
 array[i] *= 2;
 
 for (int* p = array; p < array + sizeof(array)/ sizeof(array[0]); ++p)
 cout << *p << endl; }
```

>对于一个**有范围的集合**而言，由程序员来说明循环的范围是多余的，有时候还会容易犯错误。因此C++11中
>引入了基于范围的for循环。**for循环后的括号由冒号“ ：”分为两部分：第一部分是范围内用于迭代的变量，
>第二部分则表示被迭代的范围。**

```c++
void TestFor()
{
 int array[] = { 1, 2, 3, 4, 5 };
 //将数组中所有元素乘以2
 for(auto& e : array)//这样写要记得加上引用&定义
 e *= 2;
 
 for(auto e : array)
 cout << e << " ";
 //与普通循环类似，可用continue来结束本次循环，也可以用break来跳出整个循环。
 return 0; 
 }
```

## 范围for的使用条件

### 1.for循环迭代的范围必须是确定的

>对于数组而言，就是数组中第一个元素和最后一个元素的范围；对于类而言，应该提供begin和end的方法，begin和end就是for循环迭代的范围。

```c++
//问题代码，for范围不确定
void TestFor(int array[])
{
 for(auto& e : array)  //这里的array其实不是数组，数组在传参时会退化成指针
 cout<< e <<endl; }

```

### 2. 迭代的对象要实现++和==的操作。



## 指针空值nullptr

> NULL其实是一个宏，在传统的C头文件(stddef.h)中可以看到如下代码：

```c++
#ifndef NULL
#ifdef __cplusplus
#define NULL    0
#else  
#define NULL    ((void *)0)
#endif  
#endif  
```

>可以看到，NULL可能被定义为字面常量0，或者被定义为无类型指针(void*)的常量。不论采取何种定义，在
>使用空值的指针时，都不可避免的会遇到一些麻烦

```c++ 
```

# 类

## 一、类的构成

> 类声明中的内容包括数据和函数，分别称为数据成员和成员函数。按访问权限划分，数据成员和成员函数又可分为共有、保护和私有3种。

```C++
class Score{
    public:
    
    protected:
    
    private:
    
}
```

三者并非全有，至少要有其中之一。<br>不限顺序<br>`pravite`作为第一部分可以省略，没有关键字默认为私有<br>不能再类声明中给数据成员赋值

## 二、成员函数定义

普通成员函数

> 声明写在类内，定义写在类外
>
> `返回值类型 类名::函数名(参数){函数体}`

```c++
//分数类Score
class Score{
    public:
    void setScore(int m,int f);
    void showScore();
    private:
    int mid_exam;
    int fin_exam;
};

void Score::setScore(int m,int f)
{
    min_exam=m;
    fin_exam=f;
}
void Score::showScore()
{
    cout<<"期中成绩"<<mid_exam<<endl;
    cout>>"期末成绩"<<fin_exam<<endl;
}
```

内联函数成员定义

> 将函数定义在类的内部。函数体也在类内，就不需要inline

```c++
class Score{
public:
	void setScore(int m, int f)
	{
		mid_exam = m;
		fin_exam = f;
	}
	void showScore()
	{
		cout << "期中成绩: " << mid_exam << endl;
		cout << "期末成绩：" << fin_exam << endl;
	}
private:
	int mid_exam;
	int fin_exam;
};

```

> 函数声明在类内部，定义在类外部，声明时还是需要inline

```c++
class Score{
public:
	inline void setScore(int m, int f);//显式声明
	inline void showScore();
private:
	int mid_exam;
	int fin_exam;
};

inline void Score::setScore(int m, int f) 
{
	mid_exam = m;
	fin_exam = f;
}

inline void Score::showScore()//定义时候也要对应的inline
{
	cout << "期中成绩: " << mid_exam << endl;
	cout << "期末成绩：" << fin_exam << endl;
}

```

## 三、对象的定义和使用

> 声明类的时候同时定义

```c++
class Score{
public:
	void setScore(int m, int f);
	void showScore();
private:
	int mid_exam;
	int fin_exam;
}op1, op2;

```

> 声明后再定义

```c++
  Score op1, op2;
```

对象成员访问

> 类似结构体

**类的作用域和类成员的访问属性**

> 私有成员只能被类中的成员函数访问，不能在类的外部，通过类的对象进行访问

**对象赋值语句**

```c++
	Score op1, op2;
	op1.setScore(99, 100);//括号赋值
	op2 = op1;//你赋值给我
	op2.showScore();

```



## 四、构造函数和析构函数

### 构造函数

> 特殊成员函数
>
> 用于为对象分配空间，进行初始化
>
> 构造函数与类名相同
>
> 参数类型任意，但是无返回值
>
> 自动调用

```c++
class Score{
public:
	Score(int m, int f);  //构造函数
	void setScore(int m, int f);
	void showScore();
private:
	int mid_exam;
	int fin_exam;
};

Score::Score(int m, int f)
{
	mid_exam = m;
	fin_exam = f;
}
```

> 用构造函数赋值

```c++
//类名 对象名[(实参表)]
Score op1(99, 100);
op1.showScore();

//类名 *指针变量名 = new 类名[(实参表)]
Score *p;
p = new Score(99, 100);
p->showScore();
-----------------------
Score *p = new Score(99, 100);
p->showScore();

```

构造函数的名字必须与类名相同，否则编译程序将把它当做一般的成员函数来处理。
构造函数没有返回值，在定义构造函数时，是不能说明它的类型的。
与普通的成员函数一样，构造函数的函数体可以写在类体内，也可写在类体外。
构造函数一般声明为共有成员，但它不需要也不能像其他成员函数那样被显式地调用，它是在定义对象的同时被自动调用，而且只执行一次。
构造函数可以不带参数。

**成员初始化列表**

> 可以在构造函数中用赋值语句进行
>
> 还可以用成员初始化列表初始化

```c++
类名::构造函数(参数):成员1(所附的值),成员2(所附的值)//按照成员声明顺序
```

```c++
class A{
private:
	int x;
	int& rx;
	const double pi;
public:
	A(int v) : x(v), rx(x), pi(3.14)    //成员初始化列表
	{	}
	void print()
	{
		cout << "x = " << x << " rx = " << rx << " pi = " << pi << endl;
	}
};
```

类成员是按照它们在类里被声明的顺序进行初始化的，与它们在成员初始化列表中列出的顺序无关。

**带默认参数的构造函数**

```c++
Score::Score(int m, int f) : mid_exam(m), fin_exam(f)
{
	cout << "构造函数使用中..." << endl;
}
```

### 析构函数

> 与构造函数相反，常用于撤销对象的清理任务

1. 析构函数前面比构造函数多一个`~`
2. 析构函数没有参数返回值，不能被重载
3. 撤销对象时，编译系统会自动调用析构函数

>在以下情况中，当对象的生命周期结束时，析构函数会被自动调用：
>
>1.如果定义了一个全局对象，则在程序流程离开其作用域时，调用该全局对象的析构函数。
>2.如果一个对象定义在一个函数体内，则当这个函数被调用结束时，该对象应该被释放，析构函数被自动调用。
>3.若一个对象是使用new运算符创建的，在使用delete运算符释放它时，delete会自动调用析构函数。

```c++
#include <iostream>
#include <string>
#include<string.h>
using namespace std;

class Student{
private:
	char *name;
	char *stu_no;
	float score;
public:
	Student(char *name1, char *stu_no1, float score1);//定义了构造函数
	~Student();
	void modify(float score1);
	void show();
};

Student::Student(char *name1, char *stu_no1, float score1)
{
	name = new char[strlen(name1) + 1];
	strcpy(name, name1);
	stu_no = new char[strlen(stu_no1) + 1];
	strcpy(stu_no, stu_no1);
	score = score1;
}

Student::~Student() 
{
	delete []name;//删除name数组算占用的内存，以便在析构函数里回收内存
	delete []stu_no;
}

void Student::modify(float score1) 
{
	score = score1;
}

void Student::show()
{
	cout << "姓名: " << name << endl;
	cout << "学号: " << stu_no << endl;
	cout << "成绩：" << score << endl;
}

int main()
{
	Student stu("雪女", "2020199012", 99);//定义了构造函数所以初始化用的圆括号
	stu.modify(100);
	stu.show();

	return 0;
}

```

**默认的构造函数和析构函数**

> 如果没有给类定义构造函数，则编译系统自动生成一个默认的构造函数。

1.对没有定义构造函数的类，其公有数据成员可以用初始值列表进行初始化

```c++
class A{
public:
	char name[10];
	int no;
};

A a = {"chen", 23};//使用的花括号
cout << a.name << a.no << endl;

```

2.只要一个类定义了一个构造函数（不一定是无参构造函数），系统将不再给它提供默认的构造函数。

每个类必须有一个析构函数。若没有显示地为一个类定义析构函数，编译系统会自动生成一个默认的析构函数

**构造函数的重载**

> 在一个类中，当无参数的构造函数和带默认参数的构造函数重载时，有可能产生二义性。

```c++
class Score{
public:
	Score(int m, int f);  //构造函数
	Score();//构造函数重载
	void setScore(int m, int f);
	void showScore();
private:
	int mid_exam;
	int fin_exam;
};
```

**拷贝构造函数**

> 拷贝构造函数是一种特殊的构造函数，其形参是本类对象的引用。拷贝构造函数的作用是在建立一个新对象时，使用一个已存在的对象去初始化这个新对象。

> 就是用已经有的对象来拷贝赋值给新对象

特点

1. 因为拷贝构造函数也是一种构造函数，所以其函数名与类名相同，并且该函数也没有返回值。
2. 拷贝构造函数只有一个参数，并且是同类对象的引用。
3. 每个类都必须有一个拷贝构造函数。可以自己定义拷贝构造函数，用于按照需要初始化新对象；如果没有定义类的拷贝构造函数，系统就会自动生成一个默认拷贝构造函数，用于复制出与数据成员值完全相同的新对象。

自定义拷贝构造函数

```c++
Score::Score(const Score &p)
{
	mid_exam = p.mid_exam;
	fin_exam = p.fin_exam;
}
```



```c++
类名::类名(const 类名 &对象名) 
{
    拷贝构造函数的函数体；
}

class Score{
public:
	Score(int m, int f);  //构造函数
	Score();
	Score(const Score &p);  //拷贝构造函数
	~Score();               //析构函数
	void setScore(int m, int f);
	void showScore();
private:
	int mid_exam;
	int fin_exam;
};

Score::Score(int m, int f)
{
	mid_exam = m;
	fin_exam = f;
}

Score::Score(const Score &p)
{
	mid_exam = p.mid_exam;
	fin_exam = p.fin_exam;
}

调用拷贝构造函数的一般形式为：
    类名 对象2(对象1);
    类名 对象2 = 对象1;
Score sc1(98, 87);
Score sc2(sc1);    //调用拷贝构造函数
Score sc3 = sc2;   //调用拷贝构造函数
```

**调用拷贝构造函数的三种情况**

- 当用类的一个对象去初始化该类的另一个对象时；
- 当函数的形参是类的对象，调用函数进行形参和实参结合时；
- 当函数的返回值是对象，函数执行完成返回调用者时。

**浅拷贝和深拷贝**

**浅拷贝**，就是由**默认**的拷贝构造函数所实现的数据成员逐一赋值。通常默认的拷贝构造函数是能够胜任此工作的，但若类中含有`指针类型`的数据，则这种按数据成员逐一赋值的方法会产生错误。

```c++
class Student{
public:
    Student(char *name1, float score1);
    ~Student();
private:
    char *name;
    float score;
};

如下语句会产生错误
Student stu1("白", 89);//这个没问题
Student stu2 = stu1;//但是由于name的类型是char*所以不能这样默认拷贝
```

> 上述错误是因为stu1和stu2所指的内存空间相同，在析构函数释放stu1所指的内存后，再释放stu2所指的内存会发生错误，因为此内存空间已被释放。解决方法就是重定义拷贝构造函数，为其变量重新生成内存空间。

```c++
//重新定义拷贝函数
Student::Student(const Student& stu)
{
    name = new char[strlen(stu.name) + 1];//重新分配内存，这样地址不同就可以正常拷贝
    if (name != 0) //判断name内存是否分配成功
    {
        strcpy(name, stu.name);
        score = stu.score;
    }
}
```

## 自引用指针this

`this`指针保存当前对象的地址，称为自引用指针

````c++
void Sample::cpoy(Sample& xy)
{
    if(this==&xy)
    {
        reutrn ;
    }
    *this=xy;
}
````

## 对象数组与对象指针

### **对象数组**

````c++
类名 数组名[下标表达式]
用只有一个参数的构造函数给对象数组赋值
Exam ob[4] = {89, 97, 79, 88};
用不带参数和带一个参数的构造函数给对象数组赋值
Exam ob[4] = {89, 90};
用带有多个参数的构造函数给对象数组赋值
Score rec[3] = {Score(33, 99), Score(87, 78), Score(99, 100)};

````

### **对象指针**

对象初始化在内存有空间，可以通过对象名访问，也可通过地址（指针）访问

```c++
Score score;
Score *p;//就是指针，类型是对应的类
p = &score;
p->成员函数();
```

用对象指针访问对象数组

```c++
Score score[2];
score[0].setScore(90, 99);
score[1].setScore(67, 89);

Score *p;
p = score;   //将对象score的地址赋值给p
p->showScore();
p++;    //对象指针变量加1
p->showSccore();

Score *q;
q =&score[1];   //将第二个数组元素的地址赋值给对象指针变量q
```

## string类

C++支持两种类型的字符串，第一种是C语言中介绍过的、包括一个结束符’`\0`’（即以`NULL`结束）的字符数组，标准库函数提供了一组对其进行操作的函数，可以完成许多常用的字符串操作。

C++标准库中声明了一种更方便的字符串类型，即字符串类string，类string提供了对字符串进行处理所需要的操作。使用string类必须在程序的开始包括头文件string，即要有以下语句：`#include <string>`

```c++
//常见运算符
=、+、+=、==、!=、<、<=、>、>=、[]（访问下标对应字符）、>>（输入）、<<（输出）
```

```c++
#include <iostream>
#include <string>
using namespace std;

int main()
{
	string str1 = "ABC";
	string str2("dfdf");
	string str3 = str1 + str2;//ABCdfdf
	cout<< "str1 = " << str1 << "  str2 = " << str2 << "  str3 = " << str3 << endl;
	str2 += str2;//dfdfdfdf
	str3 += "aff";//ABCdfdfaff
	cout << "str2 = " << str2 << "  str3 = " << str3 << endl;
	cout << "str1[1] = " << str1[1] << "  str1 == str2 ? " << (str1 == str2) << endl;//B 0
	string str = "ABC";
	cout << "str == str1 ? " << (str == str1) << endl;//1
	return 0;
}
```

## 向函数传递对象

- **使用对象作为函数参数**：对象可以作为参数传递给函数，其方法与传递其他类型的数据相同。在向函数传递对象时，是通过“传值调用”的方法传递给函数的。因此，函数中 对对象的任何修改均不影响调用该函数的对象（实参本身）。
- **使用对象指针作为函数参数**：对象指针可以作为函数的参数，使用对象指针作为函数参数可以实现传值调用，即在函数调用时使实参对象和形参对象指针变量指向同一内存地址，在函数调用过程中，形参对象指针所指的对象值的改变也同样影响着实参对象的值。
- **使用对象引用作为函数参数**：在实际中，使用对象引用作为函数参数非常普遍，大部分程序员喜欢使用对象引用替代对象指针作为函数参数。因为使用对象引用作为函数参数不但具有用对象指针做函数参数的优点，而且用对象引用作函数参数将更简单、更直接。

```c++
#include <iostream>
using namespace std;

class Point{
public:
	int x;
	int y;
	Point(int x1, int y1) : x(x1), y(y1)  //成员初始化列表
    { }
	int getDistance() 
	{
		return x * x + y * y;
	}
};

void changePoint1(Point point)    //使用对象作为函数参数
{
	point.x += 1;
	point.y -= 1;
}

void changePoint2(Point *point)   //使用对象指针作为函数参数
{
	point->x += 1;
	point->y -= 1;
}

void changePoint3(Point &point)  //使用对象引用作为函数参数
{
	point.x += 1;
	point.y -= 1;
}


int main()
{
	Point point[3] = {Point(1, 1), Point(2, 2), Point(3, 3)};
	Point *p = point;
	changePoint1(*p);//值传递，不改变
	cout << "the distance is " << p[0].getDistance() << endl;
	p++;
	changePoint2(p);//传指针、地址传递
	cout << "the distance is " << p->getDistance() << endl;
	changePoint3(point[2]);//不用传地址
	cout << "the distance is " << point[2].getDistance() << endl;

	return 0;
}

```



## 静态成员

在一个类中，若将一个数据成员说明为`static`，则这种成员被称为**静态数据成员**。与一般的数据成员不同，无论建立多少个类的对象，都只有一个静态数据成员的拷贝。从而实现了同一个类的不同对象之间的数据共享

> static 同一个类的多个对象中共通的数据

定义静态数据成员的格式如下：`static 数据类型 数据成员名;`

说明：

1. 静态数据成员的定义与普通数据成员相似，但前面要加上static关键字。
2. 静态数据成员的**初始化**与普通数据成员**不同**。**静态**数据成员**初始化**应在**类外单独进行**，而且应在**定义对象之前**进行。一般在main()函数之前、类声明之后的特殊地带为它提供定义和初始化。
3. 静态数据成员属于类（准确地说，是属于类中对象的集合），而不像普通数据成员那样属于某一对象，因此，可以使用“类名::”访问静态的数据成员。格式如下：类名::静态数据成员名。
4. 静态数据成员与**静态变量**一样，是在编译时创建并初始化。它在该类的任何对象被建立之前就存在。因此，共有的静态数据成员可以在**对象定义之前被访问**。对象定以后，共有的静态数据成员也可以通过对象进行访问。

```c++
//访问格式
对象名.静态数据成员名;
对象指针->静态数据成员名;
```



### 静态成员函数 	

在类定义中，**前面有static**说明的成员函数称为静态成员函数。静态成员函数属于整个类，是该类所有对象**共享的成员函数**，而不属于类中的某个对象。静态成员函数的作用不是为了对象之间的沟通，而是为了**处理静态数据成员**

使用方式：<br>`类名::静态成员函数名(实参表);
 对象.静态成员函数名(实参表);
 对象指针->静态成员函数名(实参表);`

> 态成员函数不访问类中的非静态成员。若确实需要，静态成员函数只能通过对象名（或对象指针、对象引用）访问该对象的非静态成员。

1. 一般情况下，静态函数成员主要用来访问静态成员函数。当它与静态数据成员一起使用时，达到了对同一个类中对象之间共享数据的目的。
2. 私有静态成员函数不能被类外部的函数和对象访问。
3. 使用静态成员函数的一个原因是，可以用它在**建立任何对象之前**调用静态成员函数，以**处理静态数据成员**，这是普通成员函数不能实现的功能
4. 编译系统将静态成员函数限定为**内部连接**，也就是说，与现行文件相连接的其他文件中的同名函数不会与该函数发生冲突，维护了该函数使用的**安全性**，这是使用静态成员函数的另一个原因。
5. 静态成员函数是类的一部分，而不是对象的一部分。如果要在类外调用公有的静态成员函数，使用如下格式较好：类名::静态成员函数名()

````c++
#include <iostream>
using namespace std;

class Score{
private:
	int mid_exam;
	int fin_exam;
	static int count;     //静态数据成员，用于统计学生人数
	static float sum;     //静态数据成员，用于统计期末累加成绩
	static float ave;     //静态数据成员，用于统计期末平均成绩
public:
	Score(int m, int f);
	~Score();
	static void show_count_sum_ave();   //静态成员函数
};

Score::Score(int m, int f)//构造函数
{
	mid_exam = m;
	fin_exam = f;
	++count;
	sum += fin_exam;
	ave = sum / count;
}

Score::~Score()//析构函数
{

}

/*** 静态成员初始化 ***/
int Score::count = 0;
float Score::sum = 0.0;
float Score::ave = 0.0;

void Score::show_count_sum_ave()
{
	cout << "学生人数: " << count << endl;
	cout << "期末累加成绩: " << sum << endl;
	cout << "期末平均成绩: " << ave << endl;
}

int main()
{
	Score sco[3] = {Score(90, 89), Score(78, 99), Score(89, 88)};//对象数组
	sco[2].show_count_sum_ave();
	Score::show_count_sum_ave();

	return 0;
}
````

## 友元

类的主要特点之一是数据隐藏和封装，即类的私有成员（或保护成员）只能在类定义的范围内使用，也就是说**私有成员只能通过它的成员函数来访问**。但是，有时为了访问类的私有成员而需要在程序中多次调用成员函数，这样会因为频繁调用带来较大的时间和空间开销，从而降低程序的运行效率。

为此，C++提供了友元来对私有或保护成员进行访问。友元包括友元函数和友元类。


### 友元函数

友元函数既可以是不属于任何类的**非成员函数**，也可以是另一个类的**成员函数**。友元函数不是当前类的成员函数，但它**可以访问该类的所有成员**，包括私有成员、保护成员和公有成员。

在类中声明友元函数时，需要在其函数名前加上关键字`friend`。此声明可以放在公有部分，也可以放在保护部分和私有部分。友元函数可以定义在类内部，也可以定义在类外部。

> 位置随便放

#### **1、将非成员函数声明为友元函数**

```c++
#include <iostream>
using namespace std;
class Score{
private:
	int mid_exam;
	int fin_exam;
public:
	Score(int m, int f);
	void showScore();
	friend int getScore(Score &ob);//声明为友元函数
};

Score::Score(int m, int f)
{
	mid_exam = m;
	fin_exam = f;
}

int getScore(Score &ob)//非成员函数，前面可以没有类名
{						//传参类型可以是对象名、对象指针、对象引用
	return (int)(0.3 * ob.mid_exam + 0.7 * ob.fin_exam);
}

int main()
{
	Score score(98, 78);
	cout << "成绩为: " << getScore(score) << endl;

	return 0;
}
```

**说明：**

1. 友元函数虽然可以访问类对象的私有成员，但他毕竟不是成员函数。因此，在类的外部定义友元函数时，不必像成员函数那样，在函数名前加上“类名::”。
2. 因为友元函数不是类的成员，所以它不能直接访问对象的数据成员，也不能通过this指针访问对象的数据成员，它必须通过作为入口**参数传递进来的对象名（或对象指针、对象引用）**来访问该对象的数据成员。
3. 友元函数提供了不同类的成员函数之间、类的成员函数与一般函数之间进行数据共享的机制。尤其当一个函数需要访问多个类时，友元函数非常有用，普通的成员函数只能访问其所属的类，但是**多个类的友元函数能够访问相关的所有类的数据**。

```c++
一个函数同时定义为两个类的友元函数
#include <iostream>
#include <string>
using namespace std;

class Score;    //对Score类的提前引用说明
class Student{
private:
	string name;
	int number;
public:
	Student(string na, int nu) {
		name = na;
		number = nu;
	}
	friend void show(Score &sc, Student &st);//声明为Student的友元函数
};

class Score{
private:
	int mid_exam;
	int fin_exam;
public:
	Score(int m, int f) {
		mid_exam = m;
		fin_exam = f;
	}
	friend void show(Score &sc, Student &st);//声明为Score的友元函数
};

void show(Score &sc, Student &st) //在外定义的函数、参数分别为两个类的对象
{
	cout << "姓名：" << st.name << "  学号：" << st.number << endl;
	cout << "期中成绩：" << sc.mid_exam << "  期末成绩：" << sc.fin_exam << endl;
}

int main() {
	Score sc(89, 99);
	Student st("白", 12467);
	show(sc, st);

	return 0;
}
```

#### **2、将成员函数声明为友元函数**

一个类的成员函数可以作为另一个类的友元，它是友元函数中的一种，称为**友元成员函数**。友元成员函数不仅可以访问自己所在类对象中的私有成员和公有成员，还可以访问friend声明语句所在类对象中的所有成员，这样能使两个类相互合作、协调工作，完成某一任务。

```c++
#include <iostream>
#include <string>
using namespace std;

class Score;    //对Score类的提前引用说明
class Student{
private:
	string name;
	int number;
public:
	Student(string na, int nu) {
		name = na;
		number = nu;
	}
	void show(Score &sc);//Student的成员函数，这个参数是Score函数，不需要创Student的参数，因为可以直接在函数体调用
};
//定义Score的类
class Score{
private:
	int mid_exam;
	int fin_exam;
public:
	Score(int m, int f) {
		mid_exam = m;
		fin_exam = f;
	}
	friend void Student::show(Score &sc);
};

void Student::show(Score &sc) {
	cout << "姓名：" << name << "  学号：" << number << endl;//不用依靠对象，可以直接调用，对Student来说是成员函数
	cout << "期中成绩：" << sc.mid_exam << "  期末成绩：" << sc.fin_exam << endl;//依赖对象调用，相对Score来说是友元函数
}

int main() {
	Score sc(89, 99);
	Student st("白", 12467);
	st.show(sc);//传参数

	return 0;
}
```

注意：一个类的成员函数作为另一个类的友元函数时，必须先定义这个类`class Score`。并且在声明友元函数时，需要加上成员函数所在类的类名`void show(Score &sc);`；

### 友元类

可以将一个类声明为另一个类的友元

```c++
class Y{
    ···
};
class X{
    friend Y;    //声明类Y为类X的友元类
};
```

当一个类被说明为另一个类的友元类时，它所有的成员函数都成为另一个类的友元函数，这就意味着作为友元类中的所有成员函数都可以访问另一个类中的所有成员。

- 友元关系不具有**交换性**和**传递性**。

## 类的组合

> 在一个类中内嵌另一个类的对象作为数据成员，称为类的组合。该内嵌对象称为对象成员，又称为**子对象**。

```c++
class Y{
    ···
};
class X{
    Y y;//定义Y类的对象，X的子对象
    ···
};

```

## 共享数据的保护

常类型的引入就是为了既保护数据共享又防止数据被改动。常类型是指使用类型修饰符const说明的类型，常类型的变量或对象成员的值在程序运行期间是不可改变的。

### 常引用

如果在说明引用时用const修饰，则被说明的引用为常引用。常引用所引用的对象不能被更新。如果用常引用做形参，便不会产生对实参的不希望的更改。

`const 类型& 引用名`

```c++
int a = 5;
const int& b = a;
此时再对b赋值是非法的。
---------------------------
int add(const int& m, const int& n) {
    return m + n;
}
在此函数中对变量m和变量n更新是非法的，报错yo
```

### 常对象

如果在说明对象时用const修饰，则被说明的对象为**常对象**。常对象中的数据成员为常量且必须要有**初值**。

`类名 const 对象名[(参数表)];`

`const Date date(2021, 5, 31);`常对象要有初值

### 常对象成员

1、**常数据成员**

类的数据成员可以是常量或常引用，使用const说明的数据成员称为常数据成员。如果在一个类中说明了常数据成员，那么构造函数就**只能通过成员初始化列表对该数据成员进行初始化**，而任何其他函数都不能对该成员赋值。

```c++
class Date{
private:
	const int year;//只能成员初始化列表进行初始化，初始化完成后值不能改变
	int month;
	int day;
public:
	Date(int y, int m, int d) : year(y), month(m), day(d) {
	}
};
```

2、**常成员函数**

const是函数类型的一个组成部分，因此在声明函数和定义函数时都要有关键字const。在调用时不必加const

```c++
class Date{
private:
	int year;
	int month;
	int day;
public:
	Date(int y, int m, int d) : year(y), month(m), day(d){

	}
	void showDate();
	void showDate() const;//const加在后面的原来是
};						//函数头部的结尾加上 const 表示常成员函数，这种函数只能读取成员变量的值，而不能修改成员变量的值
//表示这个函数不会修改数据，更加保险
void Date::showDate() {
	//···
}

void Date::showDate() const {
	//···
}

//注意
//必须在成员函数的声明和定义处同时加上 const 关键字
char* getname() const
//和
char* getname()
//是两个不同的函数原型，如果只在一个地方加 const 会导致声明和定义处的函数原型冲突。
```

**说明：**

1. 常成员函数可以访问常数据成员，也可以访问普通数据成员。
2. 常对象只能调用它的常成员函数，而不能调用普通成员函数。常成员函数是常对象唯一的对外接口。
3. 常对象函数不能更新对象的数据成员，也不能调用该类的普通成员函数，这就保证了在常成员函数中绝不会更新数据成员的值。

# 五、继承与派生

继承可以在已有类的基础上创建新的类，新类可以从一个或多个已有类中继承成员函数和数据成员，而且可以重新定义或加进新的数据和函数，从而形成类的层次或等级。其中，已有类称为**基类**或**父类**，在它基础上建立的新类称为**派生类**或**子类**。

## 继承与派生的概念

类的继承是新的类从已有类那里得到**已有的特性**。从另一个角度来看这个问题，从已有类产生新类的过程就是类的派生。类的继承和派生机制较好地解决了代码重用的问题。

关于基类和派生类的关系，可以表述为：派生类是基类的具体化，而基类则是派生类的抽象。

以下是两种典型的使用继承的场景：
\1) 当你创建的新类与现有的类相似，只是多出若干成员变量或成员函数时，可以使用继承，这样不但会减少代码量，而且新类会拥有基类的所有功能。

\2) 当你需要创建多个类，它们拥有很多相似的成员变量或成员函数时，也可以使用继承。可以将这些类的共同成员提取出来，定义为基类，然后从基类继承，既可以节省代码，也方便后续修改成员。

继承的使用

```c++
#include <iostream>
#include <string>
using namespace std;

class Person{//基类
private:
	string name;
	string id_number;
	int age;
public:
	Person(string name1, string id_number1, int age1) {
		name = name1;
		id_number = id_number1;
		age = age1;
	}
	~Person() {

	}
	void show() {
		cout << "姓名: " << name << "  身份证号: " << id_number << " 年龄: " << age << endl;
	}
};

class Student:public Person{//class <派生类名>:[继承方式] <基类名>
private:
	int credit;
public:
	Student(string name1, string id_number1, int age1, int credit1):Person(name1, id_number1, credit1) {
		credit = credit1;
	}
	~Student() {

	}
	void show() {
		Person::show();
		cout << "学分: " << credit << endl;
	}
};

int main() {
	Student stu("白", "110103**********23", 12, 123);
	stu.show();

	return 0;
}
//继承方式包括 public（公有的）、private（私有的）和 protected（受保护的），此项是可选的，如果不写，那么默认为 private。我们将在下节详细讲解这些不同的继承方式。
```

从已有类派生出新类时，可以在派生类内完成以下几种功能：

1. 可以增加新的数据成员和成员函数
2. 可以对基类的成员进行重定义
3. 可以改变基类成员在派生类中的访问属性

**基类成员在派生类中的访问属性**

派生类可以继承基类中除了**构造函数**与析构函数之外的成员，但是这些成员的访问属性在派生过程中是可以调整的。从基类继承来的成员在派生类中的访问属性也有所不同。

| 基类中成员 | 继承方式                 | 基类在派生类中的访问属性    |
| ---------- | ------------------------ | --------------------------- |
| private    | public\protected\private | 不可直接访问                |
| public     | public\protected\private | public\protected\private    |
| protected  | public\protected\private | protected\protected\private |

**派生类对基类成员的访问规则**

基类的成员可以有public、protected、private3中访问属性，基类的成员函数可以访问基类中其他成员，但是在类外通过基类的对象，就只能访问该基类的公有成员。同样，派生类的成员也可以有public、protected、private3种访问属性，派生类的成员函数可以访问派生类中自己增加的成员，但是在派生类外通过派生类的对象，就只能访问该派生类的公有成员。

派生类对基类成员的访问形式主要有以下两种：

- 内部访问：由派生类中新增的成员函数对基类继承来的成员的访问。
- 对象访问：在派生类外部，通过派生类的对象对从基类继承来的成员的访问。
  

\1) 基类成员在派生类中的访问权限不得高于继承方式中指定的权限。例如，当继承方式为 protected 时，那么基类成员在派生类中的访问权限最高也为 protected，高于 protected 的会降级为 protected，但低于 protected 不会升级。再如，当继承方式为 public 时，那么基类成员在派生类中的访问权限将保持不变。

也就是说，继承方式中的 public、protected、private 是用来指明基类成员在派生类中的最高访问权限的。

\2) 不管继承方式如何，基类中的 private 成员在派生类中始终不能使用（不能在派生类的成员函数中访问或调用）。

\3) 如果希望基类的成员能够被派生类继承并且毫无障碍地使用，那么这些成员只能声明为 public 或 protected；只有那些不希望在派生类中使用的成员才声明为 private。

\4) 如果希望基类的成员既不向外暴露（不能通过对象访问），还能在派生类中使用，那么只能声明为 protected。

**在派生类中访问基类 private 成员的唯一方法就是借助基类的非 private 成员函数，如果基类没有非 private 成员函数，那么该成员在派生类中将无法访问。**

```c++
#include<iostream>
using namespace std;
//基类People
class People{
public:
    void setname(char *name);
    void setage(int age);
    void sethobby(char *hobby);
    char *gethobby();
protected:
    char *m_name;
    int m_age;
private:
    char *m_hobby;
};
void People::setname(char *name){ m_name = name; }
void People::setage(int age){ m_age = age; }
void People::sethobby(char *hobby){ m_hobby = hobby; }
char *People::gethobby(){ return m_hobby; }
//派生类Student
class Student: public People{
public:
    void setscore(float score);
protected:
    float m_score;
};
void Student::setscore(float score){ m_score = score; }
//派生类Pupil
class Pupil: public Student{
public:
    void setranking(int ranking);
    void display();
private:
    int m_ranking;
};
void Pupil::setranking(int ranking){ m_ranking = ranking; }
void Pupil::display(){
    cout<<m_name<<"的年龄是"<<m_age<<"，考试成绩为"<<m_score<<"分，班级排名第"<<m_ranking<<"，TA喜欢"<<gethobby()<<"。"<<endl;
}
int main(){
    Pupil pup;
    pup.setname("小明");
    pup.setage(15);
    pup.setscore(92.5f);
    pup.setranking(4);
    pup.sethobby("乒乓球");
    pup.display();
    return 0;
}
//这是一个多级继承的例子，Student 继承自 People，Pupil 又继承自 Student，它们的继承关系为 People --> Student --> Pupil。Pupil 是最终的派生类，它拥有基类的 m_name、m_age、m_score、m_hobby 成员变量以及 setname()、setage()、sethobby()、gethobby()、setscore() 成员函数。

//注意，在派生类 Pupil 的成员函数 display() 中，我们借助基类的 public 成员函数 gethobby() 来访问基类的 private 成员变量 m_hobby，因为 m_hobby 是 private 属性的，在派生类中不可见，所以只能借助基类的 public 成员函数 sethobby()、gethobby() 来访问。
```

**改变访问权限**

注意：using 只能改变基类中 public 和 protected 成员的访问权限，不能改变 private 成员的访问权限，因为基类中 private 成员在派生类中是不可见的，根本不能使用，所以基类中的 private 成员在派生类中无论如何都不能访问。

```c++
#include<iostream>
using namespace std;
//基类People
class People {
public:
    void show();
protected:
    char *m_name;
    int m_age;
};
void People::show() {
    cout << m_name << "的年龄是" << m_age << endl;
}
//派生类Student
class Student : public People {
public:
    void learning();
public:
    using People::m_name;  //将protected改为public
    using People::m_age;  //将protected改为public
    float m_score;
private:
    using People::show;  //将public改为private
};
void Student::learning() {
    cout << "我是" << m_name << "，今年" << m_age << "岁，这次考了" << m_score << "分！" << endl;
}
int main() {
    Student stu;
    stu.m_name = "小明";
    stu.m_age = 16;
    stu.m_score = 99.5f;
    stu.show();  //compile error
    stu.learning();
    return 0;
}
代码中首先定义了基类 People，它包含两个 protected 属性的成员变量和一个 public 属性的成员函数。定义 Student 类时采用 public 继承方式，People 类中的成员在 Student 类中的访问权限默认是不变的。

不过，我们使用 using 改变了它们的默认访问权限，如代码第 21~25 行所示，将 show() 函数修改为 private 属性的，是降低访问权限，将 name、age 变量修改为 public 属性的，是提高访问权限。

因为 show() 函数是 private 属性的，所以代码会报错。
```



## 派生类的构造函数和析构函数

构造函数的主要作用是对数据进行初始化。在派生类中，如果对派生类新增的成员进行初始化，就需要加入派生类的构造函数。与此同时，对所有从基类继承下来的成员的初始化工作，还是由基类的构造函数完成，但是基类的构造函数和析构函数不能被继承，因此必须在派生类的构造函数中对基类的构造函数所需要的参数进行设置。同样，对撤销派生类对象的扫尾、清理工作也需要加入新的析构函数来完成。

调用顺序

```c++
#include <iostream>
#include <string>
using namespace std;

class A{
public:
	A() {
		cout << "A类对象构造中..." << endl;
	}
	~A() {
		cout << "析构A类对象..." << endl;
	}
};

class B : public A{
public:
	B() {
		cout << "B类对象构造中..." << endl;
	}
	~B(){
		cout << "析构B类对象..." << endl;
	}
};

int main() {
	B b;
	return 0;
}
```

可见：构造函数的调用严格地按照先调用基类的构造函数，后调用派生类的构造函数的顺序执行。析构函数的调用顺序与构造函数的调用顺序正好相反，先调用派生类的析构函数，后调用基类的析构函数。

**派生类构造函数和析构函数的构造规则**

```c++
派生类构造函数的一般格式为：
派生类名(参数总表):基类名(参数表) {
    派生类新增数据成员的初始化语句
}
-----------------------------------------------------------------
含有子对象的派生类的构造函数：
派生类名(参数总表):基类名(参数表0),子对象名1(参数表1),...,子对象名n(参数表n)
{
    派生类新增成员的初始化语句
}
```

在定义派生类对象时，构造函数的调用顺序如下：

调用基类的构造函数，对基类数据成员初始化。<br>调用子对象的构造函数，对子对象的数据成员初始化。<br>调用派生类的构造函数体，对派生类的数据成员初始化。

**说明：**

1. 当基类构造函数不带参数时，派生类不一定需要定义构造函数；然而当基类的构造函数哪怕只带有一个参数，它所有的派生类都必须定义构造函数，甚至所定义的派生类构造函数的函数体可能为空，它仅仅起参数的传递作用。
2. 若基类使用默认构造函数或不带参数的构造函数，则在派生类中定义构造函数时可略去“:基类构造函数名(参数表)”，此时若派生类也不需要构造函数，则可不定义构造函数。
3. 如果派生类的基类也是一个派生类，每个派生类只需负责其直接基类数据成员的初始化，依次上溯。
   

##  多继承

```c++
class 派生类名:继承方式1 基类名1,...,继承方式n 基类名n {
    派生类新增的数据成员和成员函数
};
默认的继承方式是private
```

多继承派生类的构造函数与析构函数：<br>与单继承派生类构造函数相同，多重继承派生类构造函数必须同时负责该派生类所有基类构造函数的调用。

多继承构造函数的调用顺序与单继承构造函数的调用顺序相同，也是遵循先调用基类的构造函数，再调用对象成员的构造函数，最后调用派生类构造函数的原则。析构函数的调用与之相反。


## 虚基类

虚基类的作用：如果一个类有多个直接基类，而这些直接基类又有一个共同的基类，则在最低层的派生类中会保留这个间接的共同基类数据成员的多份同名成员。在访问这些同名成员时，必须在派生类对象名后增加直接基类名，使其唯一地标识一个成员，以免产生二义性。

> 注意基类构造函数的调用次数和顺序

```c++
#include <iostream>
#include <string>
using namespace std;

class Base{
protected:
	int a;
public:
	Base(){
		a = 5;//析构函数中给a赋值
		cout << "Base a = " << a << endl;
	}
};

class Base1: public Base{
public:
	Base1() {
		a = a + 10;
		cout << "Base1 a = " << a << endl;
	}
};

class Base2: public Base{
public:
	Base2() {
		a = a + 20;
		cout << "Base2 a = " << a << endl;
	}
};

class Derived: public Base1, public Base2{
public:
	Derived() {
		cout << "Base1::a = " << Base1::a << endl;//标识好基类名
		cout << "Base2::a = " << Base2::a << endl;//
	}
};

int main() {
	Derived obj;
	return 0;
}
//结果
Base a = 5//base构造函数调用
Base1 a = 15
Base a = 5//bsae构造函数调用、因为认为是两个独立的基类所以分别调用
Base2 a = 25
Base1::a = 15
Base2::a = 25
```

**虚基类的声明：**

不难理解，如果在上列中类base只存在一个拷贝(即只有一个数据成员a)，那么对a的访问就不会产生二义性。在C++中，可以通过将这个**公共的基类声明为虚基类**来解决这个问题。这就要求从类base派生新类时，使用关键字`virtual`将base声明为虚基类。

```c++
class 派生类:virtual 继承方式 类名{ //虚基类的声明
    ·····
};
```

修改上方部分代码如下

```c++
class Base1:virtual public Base{//声明用的是虚基类
public:
	Base1() {
		a = a + 10;
		cout << "Base1 a = " << a << endl;
	}
};

class Base2:virtual public Base{//声明用的是虚基类
public:
	Base2() {
		a = a + 20;
		cout << "Base2 a = " << a << endl;
	}
};
//结果
Base a = 5//标识为一个虚基类，不再重复实现两次
Base1 a = 15
Base2 a = 35
Base1::a = 35
Base2::a = 35
```

**虚基类的初始化：**<br>虚基类的初始化与一般的多继承的初始化在语法上是一样的，但构造函数的调用顺序不同。在使用虚基类机制时应该注意以下几点：

1. 如果在虚基类中定义有带形参的构造函数，并且没有定义默认形式的构造函数，则整个继承结构中，所有直接或间接的派生类都必须在构造函数的成员初始化列表中列出对虚基类构造函数的调用，以初始化在虚基类中定义的数据成员。
2. 建立一个对象时，如果这个对象中含有从虚基类继承来的成员，则虚基类的成员是由最远派生类的构造函数通过调用虚基类的构造函数进行初始化的。该派生类的其他基类对虚基类构造函数的调用都被自动忽略。
3. 若同一层次中同时包含虚基类和非虚基类，应先调用虚基类的构造函数，再调用非虚基类的构造函数，最后调用派生类构造函数。
4. 对于多个虚基类，构造函数的执行顺序仍然是先左后右，自上而下。
5. 若虚基类由非虚基类派生而来，则仍然先调用基类构造函数，再调用派生类的构造函数。
