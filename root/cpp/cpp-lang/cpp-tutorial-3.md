导读



conversion function

```cpp
class Fraction
{
public:
    Fraction(int num, int den=1) : m_numerator(num), m_denominator(den) { }
    operator double() const {
        return (double) (m_numberator / m_denominator);
    }
private:
    int m_numerator;	//分子
    int m_denominator;	//分母
};


Fraction f(3,5);
double d=4+f;	//调用operator double()将 f 转为 0.6
```

<br>

non-explicit one argument constructor

```cpp
class Fraction
{
public:
    Fraction(int num, int den=1)
        : m_numerator(num), m_denominator(den) { }
    Fraction operator+(const Fraction& f) {
        return Fraction(...);
    }
private:
    int m_numerator;
    int denomiator;
}


Fraction f(3,5);
Fraction d2=f+4;	//调用non-explicit ctor将4转换为(4,1)
					//然后调用operator+
```

<br>

conversion function, 转换函数

```cpp
template<class Alloc>
class vector<bool, Alloc>
{
public:
    typedef _bit_reference reference;
protected:
    reference operator[] (size_type n) {
        return * (begin() + difference_type(n));
    }
...
};


struct _bit_reference {
    unsigned int* p;
    unsigned int mask;
    ...
public:
    operator bool() const { return !(!(*p & mask)); }
}
```

<br>

pointer-like classes, 关于智能指针

```cpp
template<class T>
class shared_ptr
{
public:
    T& operator*() const
    { return *px; }
    
    T* operator->() const
    { return px; }
    
    shared_ptr(T* p) : px(p) { }
    
private:
    T* px;	//todo, ? T*、*T 
    long* pn;
...
};


struct Foo
{
    ...
	void method(void) { ... }
};


shared_ptr<Foo> sp(new Foo);

Foo f(*sp);

sp->method();
//等同 px->method();
```

<br>

pointer-like classes, 关于迭代器

```cpp
template<class T, class Ref, class Ptr>
struct _list_iterator {
    typedef _list_iterator<T, Ref, Ptr> self;
    typedef Ptr pointer;
    typedef Ref reference;
    typedef _list_node<T>* link_type;
    link_type node;
    bool operator==(const self& x) const { return node == x.node; }
    ...
	reference operator*() const { return (*node).data; }
    pointer operator->() const { return &(operator*()); }
    ...
	self& operator--() { node = (link_type) ((*node).prev); return *this; }
    self operator--(int) { self tmp = *this; --*this; return tmp; }
};


template <class T>
struct _list_node {
    void *prev;
    void *next;
    T data;
};


list<Foo>::iterator ite;
...
*ite;	//获得一个 Foo object
ite->method();
//意思是调用 Foo::method()
//相当于(*ite).method();
//相当于(&(*ite))->method;
```



function-like classes, 所谓仿函数

```cpp
template <class T>
struct identity {
    const T& operator() (const T& x) const { return x; }
};


template <class Pair>
struct select1st {
    const typename Pair::first_type& 
    operator() (const Pair& x) const 
    { return x.first; }
};


template <class Pair>
struct select2nd {
    const typename Pair::second_type&
	operator() (const Pair& x) const
    { return x.second; }
}


template <class T1, class T2>
struct pair {
    T1 first;
    T2 second;
    pair() : first(T1()), second(T2()) {}
    pair(const T1& a, const T2& b) : first(a), second(b) {}
...
};
```

<br>

namespace经验谈

```cpp
using namespace std;
//-----------------
#include <iostream>
#include <memory> //shared_ptr
namespace jj01
{
void test_member_template()
{ ... }
} //namespace
//------------------
#include <iostream>
#include <list>
namespace jj02
{
template<typename T>
using Lst = list<T, allocator<T>>;
void test_template_template_param()
{ ... }
} //namespace
//------------------


int main(int argc, char** argv)
{
    jj01::test_member_template();
    jj02::test_template_template_param();
}
```

<br>

class template, 类模板

```cpp
template<typename T>
class complex
{
public:
    complex (T r = 0, T i = 0) : re (r), im (i) { }
    complex& operator += (const complex&);
    T real () const { return re; }
    T imag () const { return im; }
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



function template, 函数模板

```cpp
class stone
{
public:
    stone(int w, int h, int we) : _w(w), _h(h), _weight(we) { }
    bool operator< {const stone& rhs} const { return _weight < rhs._weight; }
private:
    int _w, _h, _weight;
};


template <class T>
inline
const T& min(const T& a, const T& b)
{
    return b < a ? b : a;
}


//编译器会对 function template 进行实参推导 (argument deduction)
//实参推导的结果，T为stone，于是调用stone::operator<
stone r1(2,3), r2(3,3), r3;
r3 = min(r1,r2);
```

<br>

member template

```cpp
template <class T1, class T2>
struct pair {
    typedef T1 first_type;
    typedef T2 second_type;
    
    T1 first;
    T2 second;
    
    pair()
        : fist(T1()), second(T2()) {}
    pair(const T1& a, const T2& b)
        : first(a), second(b) {}
    
    template <class U1, class U2>
	pair(const pair<U1, U2>& p)
        : first(p.first), second(p.second) {}
};
```

```cpp
class Base1 {};
class Derived1:public Base1 {};

class Base2 {};
class Derived2:public Base2 {};


pair<Derived1, Derived2> p;
pair<Base1, Base2> p2(p);
//相当于 pair<Base1, Base2> p2(pair<Derived1, Derived2>());
//把一个有鲫鱼和麻雀构成的pair，放进(拷贝到)一个由鱼类和鸟类构成的pair中，可以吗？
//反之，可以吗？
```

```cpp
template<typename _Tp>
class shared_ptr:public _shared_ptr<_Tp>
{
...
    template<typename _Tp1>
    explicit shared_ptr(_Tp1* _p)
    :_shared_ptr<_Tp>(_p) {}
...
};


Base* ptr = new Derived1;	//up-cast
shared_ptr<Base1> sptr(new Derived1);	//模拟 up-cast
```

<br>

specialization，模板特化

```cpp
template <class Key>
struct hash { };

template<>
struct hash<char> {
    size_t operator() (char x) const { return x; }
};

template<>
struct hash<int> {
    size_t operator() (int x) const { return x; }
};

template<>
struct hash<long> {
    size_t operator() (long x) const { return x; }
};

```



partial specialization, 模板偏特化，个数的偏

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