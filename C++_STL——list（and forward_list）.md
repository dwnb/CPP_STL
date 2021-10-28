# C++_STL——list（and forward_list）

## 1、类模板

```cpp
template < class T, class Alloc = allocator<T> > class list;
```

### 1.1容器属性

| 容器属性           |                                                              |
| ------------------ | ------------------------------------------------------------ |
| 序列               | 序列容器中的元素以严格的线性顺序排序。单个元素按其在此序列中的位置访问。 |
| 双向链表           | 每个元素保留有关如何定位下一个和前一个元素的信息，允许在特定元素（甚至整个范围）之前或之后进行恒定时间插入和擦除操作，但不能直接随机访问。 |
| 分配器动态分配内存 | 容器使用分配器对象来动态处理其存储需求。                     |

### 1.2模板参数

| 模板参数 |                    |
| -------- | ------------------ |
| T        | 包含的元素的类型。 |
| Alloc    | 分配器             |

### 1.3例子

```cpp
std::list<int> mylist;
```

## 2、构造函数

### C++11：
|          C++11： |                                                              |
| ---------------: | ------------------------------------------------------------ |
|          default | `explicit list (const allocator_type& alloc = allocator_type()); ` |
|             fill | `explicit list (size_type n);         list (size_type n, const value_type& val,                const allocator_type& alloc = allocator_type()); ` |
|            range | `template <class InputIterator>  list (InputIterator first, InputIterator last,         const allocator_type& alloc = allocator_type()); ` |
|             copy | `list (const list& x); list (const list& x, const allocator_type& alloc); ` |
|             move | `list (list&& x); list (list&& x, const allocator_type& alloc); ` |
| initializer list | `list (initializer_list<value_type> il,       const allocator_type& alloc = allocator_type());` |

### 示例：

```cpp
// constructing lists
#include <iostream>
#include <list>

int main ()
{
  // constructors used in the same order as described above:
  std::list<int> first;                                // empty list of ints
  std::list<int> second (4,100);                       // four ints with value 100
  std::list<int> third (second.begin(),second.end());  // iterating through second
  std::list<int> fourth (third);                       // a copy of third

  // the iterator constructor can also be used to construct from arrays:
  int myints[] = {16,2,77,29};
  std::list<int> fifth (myints, myints + sizeof(myints) / sizeof(int) );

  std::cout << "The contents of fifth are: ";
  for (std::list<int>::iterator it = fifth.begin(); it != fifth.end(); it++)
    std::cout << *it << ' ';

  std::cout << '\n';

  return 0;
}
```

## 3、std::list::assign、back、begin、cbegin、end、cend、crbegin、crend、empty、clear、rbegin、rend、size、emplace、pop_back、pop_front、push_back、push_front、resize、swap、emplace_back、emplace_front、erase、front、insert、max_size、operator=

可以看这篇文章[C++_STL——deque（C++11）](https://blog.csdn.net/asdasdde/article/details/118253298)

还有这篇[C++_可变参数模板到emplace_back再到construct再到forward](https://blog.csdn.net/asdasdde/article/details/119190700)

> 注意insert与deque的有些许不同，指向元素的迭代器不会随着元素后移而指向新插入的位置，还是指向以前元素，与其他标准序列容器不同，list 和 forward_list 对象专门设计用于在任何位置高效插入和删除元素，甚至在序列的中间。

```cpp
// inserting into a list
#include <iostream>
#include <list>
#include <vector>

int main ()
{
  std::list<int> mylist;
  std::list<int>::iterator it;

  // set some initial values:
  for (int i=1; i<=5; ++i) mylist.push_back(i); // 1 2 3 4 5

  it = mylist.begin();
  ++it;       // it points now to number 2           ^

  mylist.insert (it,10);                        // 1 10 2 3 4 5

  // "it" still points to number 2                      ^
  mylist.insert (it,2,20);                      // 1 10 20 20 2 3 4 5

  --it;       // it points now to the second 20            ^

  std::vector<int> myvector (2,30);
  mylist.insert (it,myvector.begin(),myvector.end());
                                                // 1 10 20 30 30 20 2 3 4 5
                                                //               ^
  std::cout << "mylist contains:";
  for (it=mylist.begin(); it!=mylist.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 4、std::list::get_allocator

```cpp
allocator_type get_allocator() const noexcept;
```

### 4.1功能

返回与列表容器关联的分配器对象类型的副本。 

### 4.2返回值

分配器。 成员类型 allocator_type 是容器使用的分配器的类型,与定义时的第二参数分配器的类型相同 ，不影响原来的容器;

```cpp
// list::get_allocator
#include <iostream>
#include <list>

int main ()
{
  std::list<int> mylist;
  int * p;

  // allocate an array of 5 elements using mylist's allocator:
  p=mylist.get_allocator().allocate(5);

  // assign some values to array
  for (int i=0; i<5; ++i) p[i]=i;

  std::cout << "The allocated array contains:";
  for (int i=0; i<5; ++i) std::cout << ' ' << p[i];
  std::cout << '\n';
  
  std::cout <<mylist.size()<< '\n';

  mylist.get_allocator().deallocate(p,5);

  return 0;
}
```

## 5、std::array::merge

```cpp
//(1)	
  void merge (list& x);
  void merge (list&& x);
//(2)	
template <class Compare>
  void merge (list& x, Compare comp);
template <class Compare>
  void merge (list&& x, Compare comp);
```

### 5.1功能

通过将其各自有序位置的所有元素转移到容器中，将 x 合并到列表中（两个容器都应该已经被排序）。根据由 operator< 或 comp 定义的严格弱排序。

### 5.2参数

x：相同类型的列表对象，左值引用与右值引用都可

comp: 比较函数

```cpp
// list::merge
#include <iostream>
#include <list>

// compare only integral part:
bool mycomparison (double first, double second)
{ return ( int(first)<int(second) ); }

int main ()
{
  std::list<double> first, second;

  first.push_back (3.1);
  first.push_back (2.2);
  first.push_back (2.9);

  second.push_back (3.7);
  second.push_back (7.1);
  second.push_back (1.4);

  first.sort();
  second.sort();

  first.merge(second);//此处是转移，不是复制，即second的内存权转移到了first，second为空
std::cout <<second.size()<<'\n';
  // (second is now empty)

  second.push_back (2.1);

  first.merge(second,mycomparison);//mycomparison是整形比较，故2.1回放在以2为整数部分的末尾

  std::cout << "first contains:";
  for (std::list<double>::iterator it=first.begin(); it!=first.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 6、std::list::remove

```cpp
void remove (const value_type& val);
```

### 6.1功能

从容器中移除所有比较等于 val 的元素。 这将调用这些对象的析构函数并通过移除的元素数量来减小容器大小。 与成员函数 list::erase 按元素的位置（使用迭代器）擦除元素不同，此函数 (list::remove) 按元素的值删除元素。 存在一个类似的函数 list::remove_if ，它允许使用相等比较以外的条件来确定元素是否被删除。 

### 6.2参数

val 元素值

```cpp
// remove from list
#include <iostream>
#include <list>

int main ()
{
  int myints[]= {17,89,7,14};
  std::list<int> mylist (myints,myints+4);

  mylist.remove(89);

  std::cout << "mylist contains:";
  for (std::list<int>::iterator it=mylist.begin(); it!=mylist.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 7、std::list::remove_if

```cpp
template <class Predicate>
  void remove_if (Predicate pred);
```

### 7.1功能

从容器中移除所有 Predicate pred 返回 true 的元素。 这将调用这些对象的析构函数并通过移除的元素数量来减小容器大小。 该函数为每个元素调用 pred(*i)（其中 i 是该元素的迭代器）。 列表中返回 true 的任何元素都将从容器中删除。 

### 7.2参数

pred：比较函数

```cpp
// list::remove_if
#include <iostream>
#include <list>

// a predicate implemented as a function:
bool single_digit (const int& value) { return (value<10); }

// a predicate implemented as a class:
struct is_odd {
  bool operator() (const int& value) { return (value%2)==1; }
};

int main ()
{
  int myints[]= {15,36,7,17,20,39,4,1};
  std::list<int> mylist (myints,myints+8);   // 15 36 7 17 20 39 4 1

  mylist.remove_if (single_digit);           // 15 36 17 20 39

  mylist.remove_if (is_odd());               // 36 20

  std::cout << "mylist contains:";
  for (std::list<int>::iterator it=mylist.begin(); it!=mylist.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 8、std::list::sort

```cpp
(1)	
  void sort();
(2)	
template <class Compare>
  void sort (Compare comp);xxxxxxxxxx (1)   void sort();(2) template <class Compare>  void sort (Compare comp);value_type* data() noexcept;const value_type* data() const noexcept;
```

### 8.1功能

与算法里的sort功能一样，但比算法的快

## 9、std::list::splice

```cpp
entire list (1)	
void splice (const_iterator position, list& x);
void splice (const_iterator position, list&& x);
single element (2)	
void splice (const_iterator position, list& x, const_iterator i);
void splice (const_iterator position, list&& x, const_iterator i);
element range (3)	
void splice (const_iterator position, list& x,
             const_iterator first, const_iterator last);
void splice (const_iterator position, list&& x,
             const_iterator first, const_iterator last);
```

## 9.1功能

将元素从 x 转移到容器中，将它们插入到位置。 这有效地将这些元素插入容器并从 x 中删除它们，从而改变两个容器的大小。 该操作不涉及任何元素的构建或破坏。 无论 x 是左值还是右值，或者 value_type 是否支持移动构造，它们都会被转移。 第一个版本 (1) 将 x 的所有元素传输到容器中。 第二个版本 (2) 仅将 i 指向的元素从 x 传输到容器中。 第三个版本 (3) 将范围 [first,last) 从 x 传输到容器中。 

```cpp
// splicing lists
#include <iostream>
#include <list>

int main ()
{
  std::list<int> mylist1, mylist2;
  std::list<int>::iterator it;

  // set some initial values:
  for (int i=1; i<=4; ++i)
     mylist1.push_back(i);      // mylist1: 1 2 3 4

  for (int i=1; i<=3; ++i)
     mylist2.push_back(i*10);   // mylist2: 10 20 30

  it = mylist1.begin();
  ++it;                         // points to 2

  mylist1.splice (it, mylist2); // mylist1: 1 10 20 30 2 3 4
                                // mylist2 (empty)
                                // "it" still points to 2 (the 5th element)
                                          
  mylist2.splice (mylist2.begin(),mylist1, it);
                                // mylist1: 1 10 20 30 3 4
                                // mylist2: 2
                                // "it" is now invalid.
  it = mylist1.begin();
  std::advance(it,3);           // "it" points now to 30

  mylist1.splice ( mylist1.begin(), mylist1, it, mylist1.end());
                                // mylist1: 30 3 4 1 10 20

  std::cout << "mylist1 contains:";
  for (it=mylist1.begin(); it!=mylist1.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  std::cout << "mylist2 contains:";
  for (it=mylist2.begin(); it!=mylist2.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 10、std::list::unique

|  (1) | `void unique(); `                                            |
| ---: | ------------------------------------------------------------ |
|  (2) | `template <class BinaryPredicate>  void unique (BinaryPredicate binary_pred);` |

### 10.1功能

唯一性，删除重复元素，删除前必须排序

### 10.2参数

默认无参删除重复元素

BinaryPredicate：删除满足BinaryPredicate的元素

```cpp
// list::unique
#include <iostream>
#include <cmath>
#include <list>

// a binary predicate implemented as a function:
bool same_integral_part (double first, double second)
{ return ( int(first)==int(second) ); }

// a binary predicate implemented as a class:
struct is_near {
  bool operator() (double first, double second)
  { return (fabs(first-second)<5.0); }
};

int main ()
{
  double mydoubles[]={ 12.15,  2.72, 73.0,  12.77,  3.14,
                       12.77, 73.35, 72.25, 15.3,  72.25 };
  std::list<double> mylist (mydoubles,mydoubles+10);
  
  mylist.sort();             //  2.72,  3.14, 12.15, 12.77, 12.77,
                             // 15.3,  72.25, 72.25, 73.0,  73.35

  mylist.unique();           //  2.72,  3.14, 12.15, 12.77
                             // 15.3,  72.25, 73.0,  73.35

  mylist.unique (same_integral_part);  //  2.72,  3.14, 12.15
                                       // 15.3,  72.25, 73.0

  mylist.unique (is_near());           //  2.72, 12.15, 72.25

  std::cout << "mylist contains:";
  for (std::list<double>::iterator it=mylist.begin(); it!=mylist.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

# forward_list

## 1、std:: forward_list::before_begin

```cpp
iterator before_begin() noexcept;
const_iterator before_begin() const noexcept;
```

### 1.1功能

返回指向容器中第一个元素之前位置的迭代器。

返回的迭代器不应取消引用：它旨在用作成员函数 emplace_after、insert_after、erase_after 或 splice_after 的参数，以将序列的开头指定为执行操作的位置。 

### 1.2返回值

指向序列开始之前位置的迭代器。 如果 forward_list 对象是 const 限定的，则该函数返回一个 const_iterator。 否则，它返回一个迭代器。

```cpp
// forward_list::before_begin
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list<int> mylist = {20, 30, 40, 50};

  mylist.insert_after ( mylist.before_begin(), 11 );

  std::cout << "mylist contains:";
  for ( int& x: mylist ) std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 2、std:: forward_list::cbefore_begin

```cpp
const_iterator cbefore_begin() const noexcept;
```

### 2.1功能

在开始之前将 const_iterator 返回到 返回一个 const_iterator 指向容器中第一个元素之前的位置。 const_iterator 是指向 const 内容的迭代器。 这个迭代器可以增减（除非它本身也是const），就像forward_list::before_begin返回的迭代器一样，但是不能用来修改它指向的内容。

### 2.2返回值

一个 const_iterator 到序列开始之前的位置。 成员类型 const_iterator 是指向 const 元素的前向迭代器类型。 

```cpp
// forward_list::cbefore_begin
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list<int> mylist = {77, 2, 16};

  mylist.insert_after ( mylist.cbefore_begin(), 19 );

  std::cout << "mylist contains:";
  for ( int& x: mylist ) std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 3、std::forward_list::emplace_after

> 无emplace与emplace_back（因为是单向链表）

```cpp
template <class... Args>
  iterator emplace_after (const_iterator position, Args&&... args);
```

### 3.1功能

通过在位置元素之后插入一个新元素来扩展容器。 这个新元素是使用 args 作为构造参数就地构造的。 这有效地将容器大小增加了 1。 与其他标准序列容器不同，list 和 forward_list 对象专门设计用于在任何位置高效插入和删除元素，甚至在序列的中间。 要在 forward_list 的开头放置元素，请使用成员函数 emplace_front，或以 before_begin 为位置调用此函数。 该元素是通过调用 allocator_traits::construct 并转发 args 就地构造的。 存在一个类似的成员函数 insert_after，它将现有对象复制或移动到容器中。 

### 3.2参数

position：迭代器位置

args：构造函数列表

```cpp
// forward_list::emplace_after
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list< std::pair<int,char> > mylist;
  auto it = mylist.before_begin();

  it = mylist.emplace_after ( it, 100, 'x' );
  it = mylist.emplace_after ( it, 200, 'y' );
  it = mylist.emplace_after ( it, 300, 'z' );

  std::cout << "mylist contains:";
  for (auto& x: mylist)
    std::cout << " (" << x.first << "," << x.second << ")";

  std::cout << '\n';
  return 0;
}
```

## 4、std::forward_list::emplace_front

> 无emplace与emplace_back（因为是单向链表）

```cpp
template <class... Args>
  void emplace_front (Args&&... args);
```

### 4.1功能

在开头构造和插入元素 在 forward_list 的开头插入一个新元素，就在其当前第一个元素之前。 这个新元素是使用 args 作为构造参数就地构造的。 这有效地将容器大小增加了 1。 存在一个类似的成员函数 push_front，它将现有对象复制或移动到容器中。

### 4.2参数

构造函数列表

```cpp
// forward_list::emplace_front
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list< std::pair<int,char> > mylist;

  mylist.emplace_front(10,'a');
  mylist.emplace_front(20,'b');
  mylist.emplace_front(30,'c');

  std::cout << "mylist contains:";
  for (auto& x: mylist)
    std::cout << " (" << x.first << "," << x.second << ")";

  std::cout << std::endl;
  return 0;
}
```

## 5、std::forward_list::erase_after

> 无erase

```cpp
iterator erase_after (const_iterator position);
iterator erase_after (const_iterator position, const_iterator last);
```

### 5.1功能

从 forward_list 容器中移除单个元素（位置之后的元素）或一系列元素（(position,last)）。

这通过移除的元素数量有效地减小了容器的大小，这些元素被破坏了。

与其他标准序列容器不同，list 和 forward_list 对象专门设计用于在任何位置高效插入和删除元素，甚至在序列的中间。 

### 5.2参数

position：迭代器位置，删除后面一个元素

last：指向要删除的最后一个元素之后的元素的迭代器。 移除的元素范围是开区间(position,last)，包括position和last之间的所有元素，但不包括position和last本身。成员类型 const_iterator 是指向元素的前向迭代器类型。 

### 5.3返回值

指向删除元素下一个元素的迭代器

```cpp
// erasing from forward_list
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list<int> mylist = {10, 20, 30, 40, 50};

                                            // 10 20 30 40 50
  auto it = mylist.begin();                 // ^

  it = mylist.erase_after(it);              // 10 30 40 50
                                            //    ^
  it = mylist.erase_after(it,mylist.end()); // 10 30
                                            //       ^

  std::cout << "mylist contains:";
  for (int& x: mylist) std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 7、std::forward_list::insert_after

> 无insert

|  (1) | `iterator insert_after ( const_iterator position, const value_type& val ); ` |
| ---: | ------------------------------------------------------------ |
|  (2) | `iterator insert_after ( const_iterator position, value_type&& val ); ` |
|  (3) | `iterator insert_after ( const_iterator position, size_type n, const value_type& val ); ` |
|  (4) | `template <class InputIterator>  iterator insert_after ( const_iterator position, InputIterator first, InputIterator last ); ` |
|  (5) | `iterator insert_after ( const_iterator position, initializer_list<value_type> il );` |

### 7.1功能

通过在位置元素之后插入新元素来扩展容器。这通过插入的元素数量有效地增加了容器大小。与其他标准序列容器不同，list 和 forward_list 对象专门设计用于在任何位置高效插入和删除元素，甚至在序列的中间。存在一个类似的成员函数，emplace_after，它直接就地构造一个插入元素对象，不执行任何复制或移动操作。参数确定插入的元素数量以及它们被初始化的值：

### 7.2参数同其他容器的insert

### 7.3返回值

指向新插入的元素（序列末尾）

```cpp
// forward_list::insert_after
#include <iostream>
#include <array>
#include <forward_list>

int main ()
{
  std::array<int,3> myarray = { 11, 22, 33 };
  std::forward_list<int> mylist;
  std::forward_list<int>::iterator it;

  it = mylist.insert_after ( mylist.before_begin(), 10 );          // 10
                                                                   //  ^  <- it
  it = mylist.insert_after ( it, 2, 20 );                          // 10 20 20
                                                                   //        ^
  it = mylist.insert_after ( it, myarray.begin(), myarray.end() ); // 10 20 20 11 22 33
                                                                   //                 ^
  it = mylist.begin();                                             //  ^
  it = mylist.insert_after ( it, {1,2,3} );                        // 10 1 2 3 20 20 11 22 33
                                                                   //        ^

  std::cout << "mylist contains:";
  for (int& x: mylist) std::cout << ' ' << x;
  std::cout << '\n';
  return 0;
}
```

## 8、std::forward_list::splice_after

> 同std::list::splice有些许区别

将元素从 fwdlst 传输到容器中，将它们插入到位置指向的元素之后。 这有效地将这些元素插入容器并从 fwdlst 中删除它们，从而改变两个容器的大小。 该操作不涉及任何元素的构建或破坏。 无论 fwdlst 是左值还是右值，或者 value_type 是否支持移动构造，它们都会被转移。 第一个版本 (1) 将 fwdlst 的所有元素传输到容器中。 第二个版本 (2) 仅将 i 指向的元素从 fwdlst 传输到容器中。 第三个版本 (3) 将范围 (first,last) 从 fwdlst 传输到容器中。

```cpp
// forward_list::splice_after
#include <iostream>
#include <forward_list>

int main ()
{
  std::forward_list<int> first = { 1, 2, 3 };
  std::forward_list<int> second = { 10, 20, 30 };

  auto it = first.begin();  // points to the 1

  first.splice_after ( first.before_begin(), second );
                          // first: 10 20 30 1 2 3
                          // second: (empty)
                          // "it" still points to the 1 (now first's 4th element)

  second.splice_after ( second.before_begin(), first, first.begin(), it);
                          // first: 10 1 2 3
                          // second: 20 30

  first.splice_after ( first.before_begin(), second, second.begin() );
                          // first: 30 10 1 2 3
                          // second: 20
                          // * notice that what is moved is AFTER the iterator

  std::cout << "first contains:";
  for (int& x: first) std::cout << " " << x;
  std::cout << std::endl;

  std::cout << "second contains:";
  for (int& x: second) std::cout << " " << x;
  std::cout << std::endl;

  return 0;
}
```

[list](http://www.cplusplus.com/reference/list/)

[ forward_list](http://www.cplusplus.com/reference/forward_list/)