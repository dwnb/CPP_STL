#  C++_STL——deque and vector

# deque

```cpp
template < class T, class Alloc = allocator<T> > class deque;
```

## 双端队列

> vector和deque都提供了非常相似的API，可以用于类似的目的，但在内部都以完全不同的方式工作：虽然向量使用一个需要偶尔重新分配以增长的单个数组，但双端队列的元素可以分散在不同的存储块，容器在内部保存必要的信息，以在恒定时间内提供对其任何元素的直接访问，并具有统一的顺序接口（通过迭代器）。因此，双端队列在内部比向量要复杂一些，但是这允许它们在某些情况下更有效地增长，特别是对于非常长的序列，在这种情况下，重新分配变得更加昂贵。

> 因此，它们提供了类似于vector的功能，但在序列的开头，而不仅仅是在其末尾，还可以有效地插入和删除元素。但是，与vector不同，双端队列不能保证将其所有元素存储在连续的存储位置：通过将指针偏移到另一个元素来访问双端队列中的元素会导致未定义的行为。

> 特定的库可能以不同的方式实现双端队列，通常作为某种形式的动态数组。但无论如何，它们允许通过随机访问迭代器直接访问单个元素，并通过根据需要扩展和收缩容器来自动处理存储。

## 参数

T：元素类型

Alloc：用于定义存储分配模型的分配器对象的类型。 默认情况下使用分配器类模板，它定义了最简单的内存分配模型，并且与值无关。 

## 容器特点

序列：序列容器中的元素以严格的线性顺序排列。 单个元素按其在此序列中的位置进行访问。 

动态数组：通常作为动态数组实现，它允许直接访问序列中的任何元素，并在序列的开头或结尾提供相对快速的元素添加/删除。 

动态分配： 容器使用分配器对象来动态处理其存储需求。 

## 1、构造函数

|             构造函数 |                                                              |
| -------------------: | ------------------------------------------------------------ |
|          default (1) | `explicit deque (const allocator_type& alloc = allocator_type()); ` |
|             fill (2) | `explicit deque (size_type n);         deque (size_type n, const value_type& val,                const allocator_type& alloc = allocator_type()); ` |
|            range (3) | `template <class InputIterator>  deque (InputIterator first, InputIterator last,         const allocator_type& alloc = allocator_type()); ` |
|             copy (4) | `deque (const deque& x); deque (const deque& x, const allocator_type& alloc); ` |
|             move (5) | `deque (deque&& x); deque (deque&& x, const allocator_type& alloc); ` |
| initializer list (6) | `deque (initializer_list<value_type> il,       const allocator_type& alloc = allocator_type());` |

`（1） 空容器构造函数（默认构造函数）`

​			`构造一个没有元素的空容器。`

`（2） 填充构造函数`

​			`构造一个包含n个元素的容器。每个元素都是val的副本（如果提供）。`

`（3） 范围构造函数`

​			`构造一个包含与范围[第一，最后)相同数量的元素的容器，每个元素的安放位置都是从该范围内相应的元素以相同的顺序构造的。`

`（4） 复制构造函数（并使用分配器复制）`

​			`以相同的顺序构造一个包含x中每个元素的副本的容器。`

`（5） 移动构造函数（并使用分配器移动）`

​			`构造一个获取x元素的容器。`

​			`如果指定了alloc并且它与x的分配器不同，那么元素将被移动。否则，不会构造任何元素（它们的所有权直接转移）。`

​			`x处于未指定但有效的状态。`

`（6） 初始值设定项列表构造函数`

​			`以相同的顺序构造一个包含il中每个元素的副本的容器。`

```cpp
#include <iostream>
#include <deque>

int main ()
{
  unsigned int i;

  // constructors used in the same order as described above:
  std::deque<int> first;                                // empty deque of ints
  std::deque<int> second (4,100);                       // four ints with value 100
  std::deque<int> third (second.begin(),second.end());  // iterating through second
  std::deque<int> fourth (third);                       // a copy of third

  // the iterator constructor can be used to copy arrays:
  int myints[] = {16,2,77,29};
  std::deque<int> fifth (myints, myints + sizeof(myints) / sizeof(int) );

  std::cout << "The contents of fifth are:";
  for (std::deque<int>::iterator it = fifth.begin(); it!=fifth.end(); ++it)
    std::cout << ' ' << *it;

  std::cout << '\n';

  return 0;
}
```

## 2、std::deque::at、back、begin、cbegin、end、cend、crbegin、crend、empty

请看这篇文章，含义一样[C++_STL——array（C++11)](https://blog.csdn.net/asdasdde/article/details/118075244)

## 3、std::deque::assign

|            range (1) | `template <class InputIterator>  void assign (InputIterator first, InputIterator last); ` |
| -------------------: | ------------------------------------------------------------ |
|             fill (2) | `void assign (size_type n, const value_type& val); `         |
| initializer list (3) | `void assign (initializer_list<value_type> il);`             |

### 3.1range (1)——参数输入迭代器[first,last),也可以传指针 

输入迭代器，把一个deque(双向队列)的输入迭代器作为参数传入，新内容是从*第一*个和*最后*一个范围内的每个元素以相同的顺序构建的元素。

下面程序的指针传递：

`third.assign (myints,myints+3);   // assigning from array.`

`third.assign (&myints[0],&myints[0]+3);   // assigning from array.`

### 3.2fill (2)——参数n个val

新内容为 n 元素，每个元素初始化为 Val 副本。

### 3.3initializer list (3)  ——参数列表类型，如{1,2,3}

新内容是作为初始化列表传递的值的副本，顺序相同。

### 3.4返回值无

```cpp
// deque::assign
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> first;
  std::deque<int> second;
  std::deque<int> third;

  first.assign (7,100);             // 7 ints with a value of 100

  std::deque<int>::iterator it;
  it=first.begin()+1;

  second.assign (it,first.end()-1); // the 5 central values of first

  int myints[] = {1776,7,4};
  third.assign (myints,myints+3);   // assigning from array.

  std::cout << "Size of first: " << int (first.size()) << '\n';
  std::cout << "Size of second: " << int (second.size()) << '\n';
  std::cout << "Size of third: " << int (third.size()) << '\n';
  return 0;
}
```

## 4、std::deque::emplace(C++11)

```cpp
template <class... Args>
  iterator emplace (const_iterator position, Args&&... args);
```

### 4.1参数

| 参数     |                                        |
| -------- | -------------------------------------- |
| position | 迭代器类型，位置，就在迭代器为位置插入 |
| args     | 用于构造新元素的参数。                 |

### 4.2返回值

返回新插入元素的位置迭代器。

### 4.3功能

在位置position插入args，其余元素“后移”

```cpp
// deque::emplace
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque = {10,20,30};

  auto it = mydeque.emplace ( mydeque.begin()+1, 100 );
  mydeque.emplace ( it, 200 );
  mydeque.emplace ( mydeque.end(), 300 );

  std::cout << "mydeque contains:";
  for (auto& x: mydeque)
    std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 5、std::deque::emplace_back、std::deque::emplace_front

```cpp
template <class... Args>
  void emplace_back (Args&&... args);

template <class... Args>
  void emplace_front (Args&&... args);
```

### 5.1参数

| 参数 |                        |
| ---- | ---------------------- |
| args | 用于构造新元素的参数。 |

### 5.2功能

分别在头部与尾部插入新元素args；

### 5.3返回值无

```cpp
// deque::emplace_from
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque = {10,20,30};

  mydeque.emplace_back (100);
  mydeque.emplace_back (200);

  std::cout << "mydeque contains:";
  for (auto& x: mydeque)
    std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 6、std::deque::erase

```cpp
iterator erase (const_iterator position );
iterator erase (const_iterator first, const_iterator last );
```

删除指定位置迭代器的元素，或者删除范围内位置迭代器元素[first,last),包头去尾（包含first，不包括last）；

### 6.1参数

（范围）位置迭代器

### 6.2返回值

返回新位置的迭代器

```cpp
// erasing from deque
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque;

  // set some values (from 1 to 10)
  for (int i=1; i<=10; i++) mydeque.push_back(i);

  // erase the 6th element
  mydeque.erase (mydeque.begin()+5);

  // erase the first 3 elements:
  mydeque.erase (mydeque.begin(),mydeque.begin()+3);

  std::cout << "mydeque contains:";
  for (std::deque<int>::iterator it = mydeque.begin(); it!=mydeque.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

### 7、std::deque::front

```cpp
 reference front();
const_reference front() const;
```
### 7.1功能

功能返回指向第一个元素的引用

### 7.2返回值

指向第一个元素的引用，如果deque被const修饰，则返回const_reference，无法修改val

```cpp
// deque::front
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque;

  mydeque.push_front(77);
  mydeque.push_back(20);

  mydeque.front() -= mydeque.back();

  std::cout << "mydeque.front() is now " << mydeque.front() << '\n';

  return 0;
}
```

## 8、std::deque::get_allocator

## 9、std::deque::insert

|   single element (1) | `iterator insert (const_iterator position, const value_type& val); ` |
| -------------------: | ------------------------------------------------------------ |
|             fill (2) | `iterator insert (const_iterator position, size_type n, const value_type& val); ` |
|            range (3) | `template <class InputIterator> iterator insert (const_iterator position, InputIterator first, InputIterator last); ` |
|             move (4) | `iterator insert (const_iterator position, value_type&& val); ` |
| initializer list (5) | `iterator insert (const_iterator position, initializer_list<value_type> il);` |

|                      | 参数                      | 功能                                                         |
| -------------------- | ------------------------- | ------------------------------------------------------------ |
| single element (1)   | position<br>val           | 在迭代器position插入val，原来元素往后“移动”                  |
| fill (2)             | position<br>n<br>val      | 在迭代器position插入n个val，原来元素往后“移动”               |
| range (3)            | position<br>first<br>last | 在迭代器position插入std::deque的[first,last)，原来元素往后“移动” |
| move (4)             | position<br>val           | val为右值引用，把右值插入双向队列（这是我的理解，欢迎批评指正） |
| initializer list (5) | position<br>il            | 在迭代器position插入列表元素，原来元素往后“移动”             |

### 9.1返回值

指向第一个新插入元素位置的迭代器

```cpp
// inserting into a deque
#include <iostream>
#include <deque>
#include <vector>

int main ()
{
  std::deque<int> mydeque;

  // set some initial values:
  for (int i=1; i<6; i++) mydeque.push_back(i); // 1 2 3 4 5

  std::deque<int>::iterator it = mydeque.begin();
  ++it;

  it = mydeque.insert (it,10);                  // 1 10 2 3 4 5
  // "it" now points to the newly inserted 10

  mydeque.insert (it,2,20);                     // 1 20 20 10 2 3 4 5
  // "it" no longer valid!

  it = mydeque.begin()+2;

  std::vector<int> myvector (2,30);
  mydeque.insert (it,myvector.begin(),myvector.end());
                                                // 1 20 30 30 20 10 2 3 4 5

  std::cout << "mydeque contains:";
  for (it=mydeque.begin(); it!=mydeque.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

### 10、std::deque::max_size

```cpp
size_type max_size() const noexcept;
```

返回容器可以达到的最大潜在大小，但容器绝不能保证能够达到该大小

```cpp
// deque::max_size
#include <iostream>
#include <deque>

int main ()
{
  unsigned int i;
  std::deque<int> mydeque;

  std::cout << "Enter number of elements: ";
  std::cin >> i;

  if (i<mydeque.max_size()) mydeque.resize(i);
  else std::cout << "That size exceeds the limit.\n";

  return 0;
}
```

## 11、std::deque::operator=

|             copy (1) | `deque& operator= (const deque& x); `                 |
| -------------------: | ----------------------------------------------------- |
|             move (2) | `deque& operator= (deque&& x); `                      |
| initializer list (3) | `deque& operator= (initializer_list<value_type> il);` |

`copy (1） 将x的所有元素复制到容器中（x保留其内容）。`
`move (2) 将 x 元素移动到容器中（x 处于未指定但有效的状态）。`
`initializer list (3) 将 列表il 元素复制到容器中。`

### 11.1返回值

```cpp
*this
```

```cpp
// assignment operator with deques
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> first (3);    // deque with 3 zero-initialized ints
  std::deque<int> second (5);   // deque with 5 zero-initialized ints

  second = first;
  first = std::deque<int>();

  std::cout << "Size of first: " << int (first.size()) << '\n';
  std::cout << "Size of second: " << int (second.size()) << '\n';
  return 0;
}
```

## 12、std::deque::operator[]

```cpp
reference operator[] (size_type n);
const_reference operator[] (size_type n) const;
```

见这篇文章，含义一样[C++_STL——array（C++11)](https://blog.csdn.net/asdasdde/article/details/118075244)

## 13、std::deque::pop_back、pop_front、push_back、push_front

`功能从尾部弹出，头部弹出，尾部添加、头部添加元素`

`pop_back、pop_front无参数，无返回值`

`push_back、push_front参数const value_type& val，value_type&& val（右值引用）`

```cpp
// deque::pop_back
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque;
  int sum (0);
  mydeque.push_back (10);
  mydeque.push_back (20);
  mydeque.push_back (30);

  while (!mydeque.empty())
  {
    sum+=mydeque.back();
    mydeque.pop_back();
  }

  std::cout << "The elements of mydeque add up to " << sum << '\n';

  return 0;
}
```

## 14、std::deque::resize

```cpp
void resize (size_type n);
void resize (size_type n, const value_type& val);
```

`调整容器的大小，使其包含n个元素。`

`如果n小于当前容器的大小，则内容将减少到其前n个元素，删除超出的元素（并销毁它们）。`

`如果n大于当前容器大小，则通过在末尾插入所需数量的元素来扩展内容，以达到n的大小。如果指定了val，新元素将被初始化为val的副本，否则，它们将被值初始化。`

`请注意，此函数通过插入或删除容器中的元素来更改容器的实际内容。`

### 14.1返回值

无

```cpp
// resizing deque
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque;
  std::deque<int>::iterator it;

  // set some initial content:
  for (int i=1; i<10; ++i) mydeque.push_back(i);

  mydeque.resize(5);
  mydeque.resize(8,100);
  mydeque.resize(12);

  std::cout << "mydeque contains:";
  for (std::deque<int>::iterator it = mydeque.begin(); it != mydeque.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

## 15、std::deque::shrink_to_fit

```cpp
void shrink_to_fit();
```

请求容器减少内存使用以适应其大小。

deque容器分配的内存可能多于容纳当前元素所需的内存：这是因为大多数库将deque实现为一个动态数组，它可以保留已删除元素的分配空间，或者预先分配额外的容量以允许更快的插入操作。

此函数要求将内存使用情况调整为容器的当前大小，但请求是非绑定的，否则容器实现可以自由优化其内存使用情况。

请注意，此函数不会更改容器的大小（请参阅）

`无参数无返回值`

```cpp
// deque::shrink_to_fit
#include <iostream>
#include <deque>

int main ()
{
  std::deque<int> mydeque (100);
  std::cout << "1. size of mydeque: " << mydeque.size() << '\n';

  mydeque.resize(10);
  std::cout << "2. size of mydeque: " << mydeque.size() << '\n';

  mydeque.shrink_to_fit();

  return 0;
}
```

## 16、std::deque::size、std::deque::swap

```cpp
size_type size() const noexcept;
void swap (deque& x);
```

见这篇文章，含义一样[C++_STL——array（C++11)](https://blog.csdn.net/asdasdde/article/details/118075244)

# non-member overloads

## std::relational operators (deque)、std::swap (deque)

见这篇文章，含义一样[C++_STL——array（C++11)](https://blog.csdn.net/asdasdde/article/details/118075244)



# vector（这里只说与deque不一样的部分）

```cpp
template < class T, class Alloc = allocator<T> > class vector; // generic template
```

> 就像数组一样，vector为它们的元素使用连续的存储位置，这意味着它们的元素也可以使用指向其元素的常规指针上的偏移量来访问，并且与在数组中一样有效。但与数组不同的是，它们的大小可以动态变化，它们的存储由容器自动处理。

> 在内部，vector使用动态分配的数组来存储它们的元素。这个数组可能需要重新分配，以便在插入新元素时增加大小，这意味着分配一个新数组并将所有元素移动到它。就处理时间而言，这是一项相对昂贵的任务，因此，每次将元素添加到容器时，vector都不会重新分配。
>
> 相反，矢量容器可能会分配一些额外的存储空间以适应可能的增长，因此容器的实际容量可能大于严格需要包含其元素的存储空间（即其大小）。库可以实施不同的增长策略来平衡内存使用和重新分配，但在任何情况下，重新分配应该只发生在对数增长的大小间隔，以便在向量末尾插入单个元素可以提供摊销的常数时间复杂性（参见 push_back）。
>
> 因此，与数组相比，vectors 消耗更多内存以换取以高效方式管理存储和动态增长的能力。
>
> 与其他动态序列容器（deques、lists 和 forward_lists）相比，vectors 非常高效地访问其元素（就像数组一样）并且相对高效地从其末尾添加或删除元素。对于涉及在末尾以外的位置插入或删除元素的操作，它们的性能比其他操作差，并且迭代器和引用的一致性不如列表和 forward_lists。 

> vector在尾部插入元素时如果底层内存大小不够则底层以当前已有内存的二倍增长内存，所以很耗内存

## 参数

T：元素类型

Alloc：用于定义存储分配模型的分配器对象的类型。 默认情况下使用分配器类模板，它定义了最简单的内存分配模型，并且与值无关。 

## 容器特点

序列：序列容器中的元素以严格的线性顺序排列。 单个元素按其在此序列中的位置进行访问。 

动态数组：通常作为动态数组实现，它允许直接访问序列中的任何元素，并在序列的开头或结尾提供相对快速的元素添加/删除。 

动态分配： 容器使用分配器对象来动态处理其存储需求。 

## 1、std::vector::data

```cpp
value_type* data() noexcept;
const value_type* data() const noexcept;
```

### 1.1功能

返回指向向量内部使用的内存数组的直接指针（头部指针，即可以说是数组名），以存储其拥有的元素。

因为向量中的元素保证按照向量所表示的相同顺序存储在连续的存储位置中，所以检索到的指针可以偏移以访问数组中的任何元素。 

### 1.2返回值

指向向量内部使用的数组中第一个元素的指针。如果向量对象是 const 限定的，则该函数返回一个指向 const value_type 的指针。 否则，它返回一个指向 value_type 的指针。

```cpp
// vector::data
#include <iostream>
#include <vector>

int main ()
{
  std::vector<int> myvector (5);

  int* p = myvector.data();

  *p = 10;
  ++p;
  *p = 20;
  p[2] = 100;

  std::cout << "myvector contains:";
  for (unsigned i=0; i<myvector.size(); ++i)
    std::cout << ' ' << myvector[i];
  std::cout << '\n';

  return 0;
}
```

## 2、不同点所有在deque的头插在这里都没有

参考：[vector](http://www.cplusplus.com/reference/vector/vector/)

[deque ](http://www.cplusplus.com/reference/deque/deque/)

