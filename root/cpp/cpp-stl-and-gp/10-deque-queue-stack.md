# 容器 deque

Deque描述，双向开口的空间。对于单向开口，就是vector，vector进行单向扩充。

![image-20230327130340071](assets/image-20230327130340071.png)



```cpp
//如果n不为0，传回n，表示buffer size由使用者自定
//如果n为0，表示buffer size使用预设值，那么
// 如果sz(sizeof(value_type))小于512，传回512/sz,
// 如果sz不小于512，传回1。
inline size_t __deque_buf_size(size_t, size_t sz)
{
    return n != 0 ? n : (sz < 512 ? size_t(512/sz) : size_t(1));
}


template <class T, class Alloc=alloc, size_t BufSiz=0>
class deque {	//所谓buffer size是指每个buffer容纳的元素个数
public:
    typedef T value_type;
    typedef __deque_iterator<T, T&, BufSiz> iterator;
protected:
    typedef pointer* map_pointer; // T**
protected
    iterator start;
    iterator finish;
    map_pointer map;
    size_type map_size;
public:
    iterator begin() { return start; }
    iterator end() { return finish; }
    size_type size() const { return finish - start;}
...
};
```



# deque's iterator

```cpp
template <class T, class Ref, class Ptr, size_t BufSiz>
struct __deque_iterator {
    typedef random_access_iterator_tag iterator_category;	//(1)
    typedef T value_type;									//(2)
    typedef Ptr pointer;									//(3)
    typedef Ref reference;									//(4)
    typedef size_t size_type;
    typedef ptrdiff_t difference_type;						//(5)
    typedef T** map_pointer;
    typedef __deque_iterator self;
    
    T* cur;
    T* first;
    T* last;
    map_pointer node;
...
};
```



```cpp
// 在position处安插一个元素，其值为 x
iterator insert(iterator position, const calue_type& x) {
    if (position.cur == start.cur) {//如果安插点是deque最前端，交给push_front()做
        push_front(x);
        return start;
    }
    else if (position.cur == finish.cur) {//安插点是deque最尾端，给push_back()做
        push_back(x);
        iterator tmp = finish;
        --tmp;
        return tmp;
    }
    else {
        return insert_aux(position, x);
    }
}


// deque<T>::insert()
template <class T, class Alloc, size_t BufSize>
typename deque<T, Alloc, BufSize>::iterator
deque<T, Alloc, BufSize>::insert_aux(iterator pos, const value_type& x) {
    difference_type index = pos - start;	//安插点之前的元素个数
    value_type x_copy = x;
    if (index < size() / 2) {	//如果安插点之前的元素个数较少
        push_front(front());	//在最前端加入与第一元素同值的元素
        ...
		push(front2, pos1, front1);	//元素搬移
    }
    else {					//安插点之后的元素个数较少
        push_back(back());	//在尾段加入与最末元素同值的元素
        ...
		copy_backward(pos, back2, back1);	//元素搬移
    }
    *pos = x_copy;		//在安插点上设定新值
    return pos;
}
```



# deque 如何模拟连续空间

全都是deque iterators的功劳

```cpp
reference operator[] (size_type n)
{
    return start[difference_type(n)];
}


reference front()
{ return *start; }

reference back()
{
    iterator tmp = finish;
    --tmp;
    return *tmp;
}

size_type size() const
{ return finish - start; }

bool empty() const
{ return finish == start; }


reference operator*() const
{ return *cur; }

pointer operator->() const
{ return &(operator*()); }


//两根iterators之间的具体相当于
//(1)两根iterators间的buffers的总长度 +
//(2)itr至其buffer末尾的长度 +
//(3)x 至其buffer起头的长度
difference_type operator-(const self& x) const
{
    return difference_type(buffer_size()) * (node - x.node - 1) + 
        (cur - first) + (x.last - x.cur);
    	//末尾(当前)buffer的元素量 + 起始buffer的元素量
}
```



![image-20230327130829194](assets/image-20230327130829194.png)

```cpp
self& operator+=(difference_type n) {
	difference_type offser = n + (cur - first);
	if (offset >= 0 && offset < difference_type(buffer_size()))
		//目标位置在同一缓冲区内
		cur += n;
	else {
		//目标位置不在同一缓冲区内
		difference_type node_offset = offset > 0 ? offset / difference_type(buffer_size()) 
            : -difference_type((-offset - 1) / buffer_size()) - 1;
        //切换至正确的缓冲区
        set_node(node + node_offset);
        //切换至正确的元素
        cur = first + (offset - node_offset * difference_type(buffer_size()));
	}
    return *this;
}

self operator+(difference_type n) const {
    self tmp = *this;
    return tmp += n;
}

self& operator-=(difference_type n)
{ return *this += -n; }

self operator-(difference_type n) const
{
    self tmp = *this;
    return tmp -= n;
}

reference operator[](difference_type n) const
{ return *(*this + n); }
```



![image-20230327181620134](assets/image-20230327181620134.png)



![image-20230327181701496](assets/image-20230327181701496.png)



# 容器 queue

![image-20230327181733375](assets/image-20230327181733375.png)



# 容器 stack

![image-20230327181847798](assets/image-20230327181847798.png)



queue 和 stack，关于其 iterator 和底层结构

stack 或 queue 都不允许遍历，也不提供 iterator。

`stack<string>::iterator ite;	//[Error]'iterator' is not a member of 'std::stack<std::basic_string<char>>'`

`queue<string>::iterator ite;	//[Error]'iterator' is not a member of 'std::stack<std::basic_string<char>>'`

stack 和 queue 都可选择 list 或 deque 做为底层结构。

```cpp
stack<string, list<string>> c;
	for(long i=0; i<10; ++i) {
        snprintf(buf, 10, "%d", rand());
        c.push(string(buf));
    }
	cout << "stack.size()=" << c.size() << endl;
	cout << "stack.top()=" << c.top() <<endl;
	c.pop();
	cout << "stack.size()=" << c.size() << endl;
	cout << "stack.top()=" << c.top() <<endl;
```



## queue 和 stack，关于其 iterator 和底层结构

queue 不可选择 vector 做为底层结构。

`c.pop();	//[Error] 'class std::vector<std::basic_string<char>>' has no member named 'pop_front'`

stack 可选择 vector 做为底层结构。



stack 和 queue 都不可选择 set 或 map 做为底层结构。

