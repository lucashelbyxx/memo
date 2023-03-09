# OOP, OOD

Object Oriented Programming, Object Oriented Design

我们已经能够良好的写出单一的class，不管他有没有带指针，写法不一样，都能够应付了。这种叫基于对象的设计。对于复数、字符串这样的类，它们基本不会跟其他的类发生关系。

但是考虑复杂问题的时候，就需要让类跟类之间产生关系。这个就叫面向对象的编程，面向对象的思想。

首先要探讨的是类和类之间有些的关系。我认为，我们只要了解这三种关系，就足够解决接下来所有类跟类之间的关系。

- Inheritance (继承)
- Composition (复合)
- Delegation (委托)



Composition (复合)，表示 has-a

```cpp
template <class T, class Sequence = deque<T> >
class queue {
    ...
protected:
    Sequence c;	// 底层容器
public:
    // 以下完全利用C的操作函数完成
    bool empty() const { return c.empty(); }
    size_type size() const { return c.size(); }
    reference front() { return c.front(); }
    reference back() { return c.back(); }
    // deque 是两端可进出，queue 是末端进前端出(先进先出)
    void push(const value_type& x) { c.push_back(x); }
    void pop() { c.pop_front(); }
}
```

标准库里取出来的一个例子，queue。queue里面有一个东西，这个东西类型叫sequence。第一行告诉我们，sequence默认就是deque类型(double end queue)。刚开始这样看可能不太习惯，把deque替换进来，变成下面。

```cpp
// queue ◆——> deque
template <class T>
class queue {
    ...
protected:
    deque<T> c;		// 底层容器
public:
    // 以下完全利用 c 的操作函数完成
    bool empty() const { return c.empty(); }
    size_type size() const { return c.size(); }
    reference front() { return c.front(); }
    reference back() { return c.back(); }
    //
    void push(const value_type& x) { c.push_back(x); }
    void pop() { c.pop_front(); }
}
```

这个 queue class 里面有个变量 c，c 是 deque，它是个模板类型。这个class有一个这种东西，这样我们就叫composition。我里头有另外一种东西，我跟另外那个的关系就叫composition，表现出来的是has-a的关系，我有n个这样的东西。

这种关系在面向对象出现之前早就存在。回想C语言的结构，C的struct里面就可以放其他的结构、整数、字符串。我们要慢慢习惯用图来表现类跟类之间的关系，尤其以后系统越来越庞大。你要用图来表现，而不是直接看源代码。

黑色菱形表示有东西，白色是另外一个话题。黑色菱形这端就是容器，它容纳了、拥有了另外一个东西。这样叫composition。

queue拥有了c之后，这几乎是完整的代码，前面有次要的...被拿掉了。这意思是queue里面的所有功能它都没有自己写，它都是调用它拥有的c来完成的。所有的功能都在deque这里已经完成了，而我这个queue想借用这个已经完成的功能来实现我的功能，所以queue写法非常简单。这是一个比较特殊的例子，要讲composition，只要 A 拥有 B，这样就是composition。现在这个例子是说 A 拥有 B，并且 A 的所有功能都让 B 来做，A自己不再做新的功能了，这是特例。这种特例让我们感受到queue拥有的这个东西显然功能很强大，它可以完全满足queue所需要的功能。

queue 是队列，先进先出。deque 是双端队列，两端都可进出，功能更加强大。

我们借由这个例子讲复合，而这个例子又比较独特，表现出一种什么形式呢？你已经有一个功能蛮强大的东西了，现在只是把它改装一下，就变成了另外一个class。什么叫改装？queue里面有一个函数pop()，这个函数其实它自己没有去做事情，它只是调用它里头的那个c的pop_front()做事情。所以就是deque里头的本来的名词pop_front，现在改头换面，没有多做什么事，pop_front就变成了pop，这就是刚刚所说的换了个面貌出现。

而说不定这个deque有100个功能，但是被包进来只开放了6个功能，而且名字也可能换了。存在这种情况的话，这就表现出一个设计模式，叫Adapter (改造、适配)。用在这个地方就是这个东西已经很棒了，你手上已经有了这个东西了。现在客户需要另外一个东西，但是你手上的这个东西的功能其实完全满足，只是可能名称不一样、接口不一样，那我们就说我们来写一个adapter好了，把它改造一下，只是把名字换一下。那这里queue就是一个adapter。

<br>

从内存角度解释 Composition

```cpp
// Sizeof:40
template <class T>
class queue {
protected:
    deque<T> c;
...
};

// Sizeof:16*2+4+4
// queue ◆——> deque
template <class T>
class deque {
protected:
    Itr<T> start;
    Itr<T> finish;
    T**	map;	// 指针的指针
    unsigned int map_size;
};

// Sizeof:4*4
// deque ◆——> Itr
template <class T>
struct Itr {
	T* cur;
    T* first;
    T* last;
    T** node;
...
};
```

<br>

Composition (复合) 关系下的构造和析构

```cpp
// Container ◆——> Component
// Container object 包含 Component part

// 构造由内而外
// Container 的构造函数首先调用 Component 的 default 构造函数，然后才执行自己。
Container::Container(...) : Component() { ... };

// 析构由外而内
// Container 的析构函数首先执行自己，然后才调用 Component 的析构函数。
Container::~Container(...) { ... ~Component() };
```

构造一定要由内而外构造，这样基础才稳定。这在我们人类的生活理念中也是这样，你要做一个东西，如果它有内部也有外部，当然是把里面构造好了才扎实。所以C++也要表现出这种特性。

构造函数有它的初值列，“Component()” 表示的是外面的构造函数调用里面的构造函数，等它执行完后才做外部自己的事情。强调先后关系。我们在写外部的Container其实不管这个东西，是希望C++语言帮我们实现这个东西，因为我们觉得这样很合理。“Component()” 是编译器帮我们加上去的。注意，也许构造函数有几个，编译器不知道调哪个，只能调用默认这个。要指定哪个带参构造函数的话，需要自己写。

析构函数，从外面一层层剥开，不能先把最里面的抽掉，抽掉整个塌了。

<br>

Delegation (委托). Composition by reference.

```cpp
// String ◇——> StringRep

// file String.hpp
class StringRep;
class String {
public:
    String();
    String(const char* s);
    String(const String& s);
    String &operator=(const String& s);
    ~String();
...
private:
    StringRep* rep; // pimpl
};


// file String.cpp
#include "String.hpp"
namespace {
class StringRep {
freind class String;
    StringRep(const char* s);
    ~Stringrep();
    int count;
    char* rep;
};
}

String::String() { ... }
...
```



<br>

Inheritance (继承)，表示 is-a

```cpp
struct _List_node_base
{
    _List_node_base* _M_next;
    _List_node_base* _M_prev;
};


template<typename _Tp> struct _List_node : public _List_node_base
{
    _Tp _M_data;
};


// _List_node ——▷ _List_node_base
```

Inheritance (继承) 关系下的构造和析构

```cpp
// Derived ——▷ Base
// Derived object 包含 Base part
// base class 的 dtor 必须是 virtual，否则会出现 undefined behavior。

// 构造由内而外
// Derived 的构造函数首先调用 Base 的 default 构造函数，然后才执行自己。
Derived::Derived(...) : Base() { ... };

// 析构由外而内
// Derived 的析构函数首先执行自己，然后才调用 Base 的析构函数。
Derived::~Derived(...) { ... ~Base() };
```

Inheritance (继承) with virtual functions (虚函数)

non-virtual 函数：你不希望 derived class 重新定义 (override, 复写)它；

virtual 函数：你希望 derived class 重新定义 (override, 复写)它，且你对它已有默认定义。

pure virtual 函数：你希望 derived class 一定要重新定义 (override, 复写)它，你对它没有默认定义。

```cpp
class Shape{
public:
    // pure virtual
    virtual void draw() const = 0;
    
    // impure virtual
    virtual void error(const std::string& msg);
    
    //  non-virtual
    int objectID() const;
    ...
};

class Rectangle: public Shape { ... };
class Ellipse: public Shape { ... };
```











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