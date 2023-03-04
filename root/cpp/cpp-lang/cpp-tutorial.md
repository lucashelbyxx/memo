# 重学 C++



# C++ 编程简介



# 头文件与类的声明

C, C++ 关于数据与函数

函数就是用来处理数据的。在 C 中没有足够的关键字，这些数据一定是全局的，各个函数都可以处理它们。

后来发展出面向对象语言。C++ 把数据和处理这些数据的函数包在一起（Class），这些数据只有这些方法可以处理，其他人看不到。C++ 的 struct 几乎等同于 class。



最经典的 class 分类，一种是带有指针的，一种是里头不带指针的。这会影响后面的写法，影响深远。

```c++
// Object Based(基于对象)：面对的是单一 class 的设计
// Class with pointer member(s)
complex
field 实部，虚部  --create-->  c1,c2,c3,c4...

function 加减乘除，共轭，正弦...


// Object Oriented(面向对象)：面对的是多重 classes 的设计，
// classes 和classes 之间的关系
// Class without pointer member(s)
string
字符(s)	--create-->	s1,s2,s3,s4...
//其实是一个 ptr，指向一串字符

function 拷贝，输出，附加，插入...


// 创建对象
complex c1(2,1);
complex c2;
complex* pc = new complex(0,1);

string s1("Hello ");
string s2("World ");
string* ps = new string;
```

数据可以有很多份，但函数只有一份。中间桥梁？



C++ programs 代码基本形式

```
.h (header files)
Classes Declaration

+

.cpp
#include <iostream.h>
#include "complex.h"

	ex.main()

+

.h (header files)
Standard Library
```

extension file name 不一定是 .h 或 .cpp（在不同平台上可能不一样），也可能是 .hpp 或其他或甚至无延伸名。



Output，C++ vs. C

```cpp
// C++
#include <iostream.h>	// or #include <iostream>
using namespace std;

int main() {
	int i = 7;
	cout << "i=" << i << endl;
	
	return 0;
}


// C
#include <stdio.h>	// or #include <cstdio>

int main() {
	int i = 7;
	printf("i=%d \n", i);
	
	return 0;
}
```



Header（头文件）中的防卫式声明、布局

```cpp
// complex.h
#ifndef _COMPLEX_	// guard(防卫式声明)
#define _COMPLEX_

// ...主体
#include <cmath>

class ostream;	// forward declarations（前置声明）
class complex;

complex&
	_doapl (complex* ths, const complex& r);
	
class complex {	// class declarations（类-声明）
	...
};

complex::function ...	// class definition（类-定义）

#endif
```

因为很多程序都要用到这个头文件，include 含入的次序如果要求是特定的，对使用者负担太沉重了。

程序第一次 include 时定义这个 COMPLEX，会进入这个主体。第二次 COMPLEX 定义过了，就不会进入这个主体。



class 的声明（declaration）

```cpp
class complex	// class head
{	// class body
	// 这里面就要开始设计我的复数应该具备什么样的数据,
	//怎么样的函数才能满足使用者的需求
public:
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	complex& operator += (const complex&);	// 这里没有大括号，只是声明而已
	double real () const { return re; }	// 有些函数在此直接定义，另一些在 body 之外定义
	double imag () const { return im; }
private:
	double re, im;	// 不同类型需要定义好几个类
	
    // 设计另外一个函数，它和它之间是朋友关系
	friend complex& _doapl (complex*, const complex&);
};

{
	complex c1(2,1);
	complex c2;
	...
}
```



class template（模板）简介

```cpp
// 实部虚部的类型不要现在就写死，等到使用时再绑定、指定
template<typename T>
class complex
{
public:
	complex (T r = 0, T i = 0) : re (r), im (i)
	{ }
	complex& operator += (const complex&);
	double real () const { return re; }	
	double imag () const { return im; }
private:
	T re, im;
	
	friend complex& _doapl (complex*, const complex&);
};


{
	complex<double> c1(2.5,1.5);
	complex<int> c2(2,6);
	...
}
```

 



# 构造函数

inline（内联）函数

```cpp
class complex	// class head
{
public:
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }	// 函数若在 class body 内定义完成，
	double imag () const { return im; }	// 便自动成为 inline 候选人
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};


inline double	// 不在 body 定义，告诉编译器尽量把我看做 inline，由编译器决定
imag(const complex& x)
{
    return x.imag();
}
```

[C++ inline和#define宏的区别 - wywdahai - 博客园 (cnblogs.com)](https://www.cnblogs.com/yanwei-wang/p/8073111.html)

如果你的函数是 inline function 会比较快、好。但是有些 function （太复杂）你说是  inline，编译器也不会当做 inline。你的 inline 只是对编译器的建议而已，是否真正的 inline 由编译器决定。



access level（访问级别）

```cpp
class complex	// class head
{
public:
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }	// 函数若在 class body 内定义完成，
	double imag () const { return im; }	// 便自动成为 inline 候选人
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};


// XXX
{
    complex c1(2,1);
    cout << c1.re;
    cout << c1,im;
}


// OOO
{
    complex c1(2,1);
    cout << c1.real();
    cout << c1.imag();
}
```

body 之中可以区分为某几大段，用 public、private 关键字来区分，第三种 protected。

数据部分放 private，因为数据是要封装起来的，不要被外界任意看到，应该只有自己类才可以看到。数据一定要通过自己的函数传递出去或者被设定，一个读一个写。不能够直接外部去拿。除非这些数据是 public，但这是我们要避免的事情。

函数部分可以分为两部分：一部分是给外界用的，一部分是处理自己的私人事情的。这些段落可以任意交错出现。



constuctor（ctor，构造函数）

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) 	// default argument(默认实参)
        : re (r), im (i)	// 1、初始化，initialization list(初值列，初始列)
	{ /* 2、赋值 */ }
    
    // 这样写，相当于放弃了初始化阶段。虽然你还是把值放进去了，但时间点已经晚了，效率差了
    /* assignment (赋值)	? XXX
    complex (double r = 0, double i = 0) 
    { re = r, im =i; }
    */
    
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};


{
    complex c1(2,1);	// 1、创建实部为2，虚部为1的对象
    complex c2;		// 2、创建没有指明参数的对象，使用默认参数
    complex* p = new complex(4);	// 3、以动态的方式来创建一个对象，得到的是一个指针
    ...
}
```

C++ 语言说你想要创建一个对象，有一个函数会被自动调用起来，叫构造函数。构造函数的写法很独特，其名称一定要跟类名称相同，它可以拥有参数，参数是可以有默认值的。其他函数也可以写出默认值来。

构造函数没有返回类型，不需要。构造函数就是来创建对象的，不能改变的，没必要写。

构造函数有一个特别写法，其他函数没有的，initialization list。一个变量数值的设定有两阶段，第一个阶段就是初始化，第二个阶段是赋值。

对应的就有析构函数。不带指针的类多半不用写析构函数。



ctor (构造函数) 可以有很多个 overloading (重载)

C++ 里面同名函数可以同时存在多个，但在编译器看来它们名称并不相同。

```cpp
class complex	
{
public:
	// 1
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	// 2 ? XXX
	complex () : re(0), im(0) {} 
    
	complex& operator += (const complex&);	
    
    // 3
	double real () const { return re; }	
	double imag () const { return im; }	
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};


// 4
void real(double r) { re = r; }


// 3、4 real 函数编译后的实际名称可能是：取决于编译器（函数名称、参数类型/数量）
?real@Complex@@QBENXZ
?real@Complex@@QAENABN@Z
    

// 1、2 构造函数不能同时存在
// 1有默认参数，2无参数。编译器不知道调哪个。
{
    complex c1;
    complex c2();
    ...
}
```

可能有很多种初值的设定，所以需要重载。C 是不允许多个重载函数的。



constructor (构造函数) 被放在 private 区域

```cpp
class complex
{
public:
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
    
    // 把函数放在private表示不可以被外界调用。
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	friend complex& _doapl (complex*, const complex&);
};


// XXX
{
    complex c1(2,1);
    complex c2;
    ...
}
```

不允许被外界创建对象。这个类有什么用？

Singleton，单例。设计的 A，外界只能用一份。这一份在 static。

```cpp
class A {
public:
	static A& getInstance();
	setup() { ... }
private:
	A();
	A(const A& rhs);
	...
};

A& A::getInstance()
{
	static A a;
	return a;
}
```



const member functions (常量成员函数)

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};


// OOO
{
    complex c1(2,1);
    cout << c1.real();
    cout << c1.imag();
}

// XXX
{
    // 对象/变量的内容是不变的，但函数没加const表明有可能改data
   	// 前后矛盾，编译器报错
    const complex c1(2,1);
    cout << c1.real();
    cout << c1.imag();
}
```

不改变数据内容的在函数的后面加 const。class 里面的函数分成会改变数据和不会改变数据两种。



参数传递：pass by value vs. pass by reference (to const)

返回值传递：return by value vs. return by reference (to const)

```cpp
class complex
{
public:
    // pass value
	complex (double r = 0, double i = 0) 
        : re (r), im (i)
	{ }
    
    // pass reference
	complex& operator += (const complex&);	
    // return value
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
    // reference, to const
	friend complex& _doapl (complex*, const complex&);
};


{
    complex c1(2,1);
    complex c2;
    
    c2 += c1;
    cout << c2;
}


// pass reference, 传进去会对 os 进行改动
// return reference
ostream&
operator << (ostream& os, const complex& x)
{
    return os << '(' << real(x) << ',' << imag(x) << ')';
}
```

pass by value 就是 value 多大都整包传过去，传的动作是压到函数的栈 (stack) 里头去。传得时候有可能不是 double 4个字节，而是传100 个字节的东西。尽量不要传 value。

过去 C 可以传指针，这包东西太大了，可以把这包东西的地址 (指针，4个字节) 传出去。C++ 有引用 (reference)。指针可以打印出来，引用在底部就是一个指针，传引用相当于传指针那么快。尽量都传引用。如果传一个字符呢？也可以用 value。

C 传指针那个函数一改就影响我了，传引用也是。如果传引用只是为了速度，不希望改，可以 to const，传进去之后函数不能改传的引用。

如果可以的话，返回值也尽量传递引用。



friend (友元)

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) 
        : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};

inline complex& _doapl (complex* ths, const complex& r)
{
    // 自由取得friend 的private成员
    ths-> re += r.re;
    ths-> im += r.im;
    return *ths;
}
```

在语言里面，朋友可以来拿数据。外界获取数据可以通过函数，但是对于 friend 可以网开一面。C++ 强调封装，朋友就打开了封装的大门。



相同 class 的各个 objects 互为 friends (友元)

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) 
        : re (r), im (i)
	{ }
    
    // 处理另外一个复数。直接拿数据，破坏了封装性，没有 friend？
	int func(const complex& param)
    { return param.re + param.im; }
    
private:
	double re, im;	
};


{
    complex c1(2,1);
    complex c2;
    
    c2.func(c1);
}
```





class body 外的各种定义 (definitions)

什么情况下可以 pass by reference

什么情况下可以 return by reference

```cpp
inline complex& _doapl (complex* ths, const complex& r)
{
    // 第一参数将会被改动，
    ths-> re += r.re;
    ths-> im += r.im;
    return *ths;
}

inline complex& complex::operator += (const complex& r)
{
	return _doapl (this, r);
}
```

在第一个函数加完以后有结果，这个结果放哪里？第一种（如 ths->re + r.re），函数必须创建一个地方存放结果。这个结果在函数结束的时候就消失了，是 local 变量。这时候就不能返回 reference，传出去的话，外界就看到了不好的东西。这时就不能传引用。

第二种如果放在已有空间上面，就是上面的情况。



好的代码风格：

1. 数据一定放在 private
2. 参数尽可能用 reference 来传，要不要加 const 看状况
3. 返回值也尽量用 reference 来传，可不可行要加进一步设想
4. 在类的 body 里面应该加 const 就加，如果不加，使用的时候可能报错
5. 构造函数 initialize list 尽量用它



operator overloading (操作符重载-1，成员函数) this

```cpp
// 1
inline complex&
_doapl(complex* ths, const complex& r)	// do assignment plus
{
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}

inline complex& 
complex::operator += (const complex& r)
{
    return _doapl (this, r);
}


{
    complex c1(2,1);
    complex c2(5);
    
    // c2是一个指针，会把地址传给this pointer
    c2 += c1;
}


// 等同于1，差别在this。写代码时不能这样写
// 所有的成员函数都带有一个隐藏的参数this pointer，谁调用这个函数，this就指向它
inline complex& complex::operator += (this, const complex& r)
{
    return _doapl(this,r);
}
```



return by  reference 语法分析

传递者无需知道接收者是以 reference 形式接收

```cpp
inline complex&		// 返回的声明是一个reference，接收端
_doapl(complex* ths, const complex& r)	// 传进来的左操作数是一个指针
{
	...
    return *ths;	// 返回的是指针所指的东西，一个object (value)。与接收端是怎样搭配的？
}

inline complex& complex::operator += (const complex& r)
{
    return _doapl (this, r);
}


{
    complex c1(2,1);
    complex c2(5);
    
    // c1传的是value，接收是用的reference
    // 如果设计的人是用value来接收的，也没关系，不影响使用者
    // c1加到c2上就执行完了，返回类型用void也可以
    c2 += c1;
    
    // c1加到c2身上，c2加到c3身上
    // 但是使用者连串赋值的话不能用void。加完后还要当右操作数
    c3 += c2 += c1;
}
```

返回的是 value，但是接收端怎么接收它，不必在乎。这就是为什么要用reference的好处。

如果你用pointer来传，传的人必须知道现在传的是pointer，要有一个特殊符号。

用reference来接收速度快，用value来接收速度慢。但反正我送出去的都不管。



class body 之外的各种定义 (definition)

```cpp
// 不带class名称，全域函数
inline double
imag(const complex& x)
{
    return x.imag ();
}

inline double
real(const complex& x)
{
    return x.real();
}


{
    complex c1(2,1);
    
    cout << imag(c1);
    cout << real(c1);
}
```



Header (头文件) 的布局

```cpp
#ifndef _COMPLEX_
#define _COMPLEX_

// 0 forward declaration (前置声明)
#include <cmath>

class ostream;
class complex;

complex& _doapl (complex* ths, const complex&)


// 1 class declaration (类-声明),class 本体
class complex
{
    ...
};


// 2 class definition (类-定义)
// 这段存在的都是函数，要么是这个类的成员函数(全名)，要么就是全局函数(前面不会带class name)
complex::function ...

    
#endif
```



operator overloading (操作符重载-2，非成员函数) 无this

2-3 为了对付 client 的三种可能用法，这里对应开发三个函数

```cpp
inline complex operator + (const complex& x, const complex& y)
{
    return complex (real (x) + real (y),
                    imag (x) + imag (y));
}

inline complex operator + (const complex& x, double y)
{
    return complex (real (x) + y, imag (x));
}

inline complex operator + (double x, const complex& y)
{
    return complex (x + real (y), imag (y));
}


{
    complex c1(2,1);
    complex c2;
    
    c2 = c1 + c2;
    c2 = c1 + 5;
    c2 = 7 + c1;
}
```

任何一个函数可以设计为成员函数或全局函数，没有谁一定比谁好。

```cpp
inline complex conj (const complex& x)
{
    return complex (real (x), -imag (x));
}

#include <iostream.h>
// 
ostream& operator << (ostream& os, const complex& x)
{
   	// 每加一种东西都会改变os的状态
    return os << '()' << real (x) << ',' << imag (x) << ')';
}


{
    complex c1(2,1);
    // 第一个参数就是cout，一个object，是一种ostream的东西，
    // 且第一个参数前不能加const，否则传进去的os不能被改变
    cout << conj(c1);
    // 连串写法决定返回类型不能为void，c1给cout返回的仍要是cout
    // 返回类型前不能加const，他还要接收conj(c1)，改变状态
    cout << c1 << conj(c1);
}
```

C++ 语法没有作用在右边身上的语法。cout是标准库早就写好的，不认识你现在写的复数类型，只认识当时既有的 (building, 内置的)。对于 << 你的函数只能使用全局的写法。





temp object (临时对象) typename();

2-3 下面这些函数绝不可 return by reference，因为它们返回的必定是个 local object。

```cpp
inline complex operator + (const complex& x, const complex& y)
{
    return complex (real (x) + real (y),
                    imag (x) + imag (y));
}

inline complex operator + (const complex& x, double y)
{
    return complex (real (x) + y, imag (x));
}

inline complex operator + (double x, const complex& y)
{
    return complex (x + real (y), imag (y));
}


{
    int(7);
    
    complex c1(2,1);
    complex c2();
    
    // 这两个的生命到他们的下一行就没有了
    complex();
    complex(4,5);
    
    cout << complex(2);
}
```



class body 之外的各种定义 (definitions)

```cpp
// 判断是正号还是加号，看参数个数
inline complex operator + (const complex& x)
{
    // 这里返回的不是local object，可以返回reference提速
    return x;
}

// 这个函数绝不可返回reference，因为其返回的是local object
inline complex operator - (const complex& x)	// negate 反相(取反)
{
    return complex (-real (x), -imag (x));
}


{
    complex c1(2,1);
    complex c2;
    cout << -c1;
    cout << +c1;
}
```



class 的声明 (declaration)

```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) 
        : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};
```



```cpp
class complex
{
public:
	complex (double r = 0, double i = 0) 
        : re (r), im (i)
	{ }
	complex& operator += (const complex&);	
	double real () const { return re; }
	double imag () const { return im; }
private:
	double re, im;	
	
	friend complex& _doapl (complex*, const complex&);
};
```



# 参数传递与返回值

# 操作符重载与临时对象

# Complex 类的实现过程

# 三大函数：拷贝构造，拷贝复制，析构

# 堆、栈与内存管理

# String 类的实现过程

# 扩展：类模板，函数模板，及其他

# 组合与继承

# 虚函数与多态

# 委托相关设计



导读

conversion function

non-explicit one argument constructor

pointer-like classes

function-like classes

namespace经验谈

class template

function template

member template

specialization

模板偏特化

模板参数

关于 C++ 标准库

三个主题

reference

符合&继承关系下的构造和析构

关于 vptr 和 vtbl

关于 Dynamic Binding

关于 this

关于 new， delete

operator new, operator delete

示例

重载 new(), delete()

basic_string 使用new(extra)扩充申请量