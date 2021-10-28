# C++_STL——queue（and priority_queue）

# queue：

```cpp
template <class T, class Container = deque<T> > class queue;
```

## 先进先出队列

### 参数：

T:元素类型

Container：存储元素的内部底层容器对象的类型。 

## 1、构造函数std::queue::queue

|            initialize (1) | `explicit queue (const container_type& ctnr); `              |
| ------------------------: | ------------------------------------------------------------ |
|       move-initialize (2) | `explicit queue (container_type&& ctnr = container_type()); ` |
|             allocator (3) | `template <class Alloc> explicit queue (const Alloc& alloc); ` |
|      init + allocator (4) | `template <class Alloc> queue (const container_type& ctnr, const Alloc& alloc); ` |
| move-init + allocator (5) | `template <class Alloc> queue (container_type&& ctnr, const Alloc& alloc); ` |
|      copy + allocator (6) | `template <class Alloc> queue (const queue& x, const Alloc& alloc); ` |
|      move + allocator (7) | `template <class Alloc> queue (queue&& x, const Alloc& alloc);` |

### 1.1功能

> 1、初始化构造函数 ：
>
> ​				构造一个容器适配器，其内部容器被初始化为 ctnr 的副本。
>
> 2、移动初始化构造函数 ：
>
> ​				构造一个容器适配器，其内部容器通过移动构造它来获取 ctnr 的值。
>
> 3、分配器构造函数 ：
>
> ​				构造一个容器适配器，其内部容器是用 alloc 作为参数构造的。
>
> 4、用分配器构造函数初始化 ：
>
> ​				构造一个容器适配器，其内部容器是用 cntr 和 alloc 作为参数构造的。
>
> 5、使用分配器构造函数进行移动初始化: 
>
> ​				构造一个容器适配器，其内部容器是用 std::move(cntr) 和 alloc 作为参数构造的。
>
> 6、用分配器构造函数复制: 
>
> ​				构造一个容器适配器，其内部容器是用 x 的内部容器作为第一个参数和 alloc 作为第二个参数来构造的。 
>
> 7、 用分配器构造函数移动：
>
> ​				构造一个容器适配器，它的内部容器是通过移动 x 的内部容器作为第一个参数并传递 alloc 作为第二个参数来构造的。 

```cpp
// constructing queues
#include <iostream>       // std::cout
#include <deque>          // std::deque
#include <list>           // std::list
#include <queue>          // std::queue

int main ()
{
  std::deque<int> mydeck (3,100);        // deque with 3 elements
  std::list<int> mylist (2,200);         // list with 2 elements

  std::queue<int> first;                 // empty queue
  std::queue<int> second (mydeck);       // queue initialized to copy of deque

  std::queue<int,std::list<int> > third; // empty queue with list as underlying container
  std::queue<int,std::list<int> > fourth (mylist);

  std::cout << "size of first: " << first.size() << '\n';
  std::cout << "size of second: " << second.size() << '\n';
  std::cout << "size of third: " << third.size() << '\n';
  std::cout << "size of fourth: " << fourth.size() << '\n';

  return 0;
}
```

## 2、std::queue::back

```cpp
reference& back();
const_reference& back() const;
```

### 2.1功能

返回对队列中最后一个元素的引用。 这是队列中的“最新”元素（即推入队列的最后一个元素）。

### 2.2返回值

对队列中最后一个元素的引用。 

```cpp
// queue::back
#include <iostream>       // std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myqueue;

  myqueue.push(12);
  myqueue.push(75);   // this is now the back

  myqueue.back() -= myqueue.front();

  std::cout << "myqueue.back() is now " << myqueue.back() << '\n';

  return 0;
}
```

## 3、std::queue::emplace

```cpp
template <class... Args> void emplace (Args&&... args);
```

### 3.1功能

在队列的末尾添加一个新元素，在其当前最后一个元素之后。 这个新元素是通过 args 作为其构造函数的参数就地构造的。

### 3.2参数

构造函数参数

```cpp
// queue::emplace
#include <iostream>       // std::cin, std::cout
#include <queue>          // std::queue
#include <string>         // std::string, std::getline(string)

int main ()
{
  std::queue<std::string> myqueue;

  myqueue.emplace ("First sentence");
  myqueue.emplace ("Second sentence");

  std::cout << "myqueue contains:\n";
  while (!myqueue.empty())
  {
    std::cout << myqueue.front() << '\n';
    myqueue.pop();
  }

  return 0;
}
```

## 4、std::queue::empty

```cpp
bool empty() const;
```

### 4.1功能

判断队列是否为空

### 4.2返回值

如果底层容器的大小为 0，则为 true，否则为 false。 

```cpp
// queue::empty
#include <iostream>       // std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myqueue;
  int sum (0);

  for (int i=1;i<=10;i++) myqueue.push(i);

  while (!myqueue.empty())
  {
     sum += myqueue.front();
     myqueue.pop();
  }

  std::cout << "total: " << sum << '\n';

  return 0;
}
```

## 5、std::queue::front

```cpp
 reference& front();
const_reference& front() const;
```

### 5.1功能

返回队头元素的引用

### 5.2返回值

队头元素的引用

```cpp
// queue::front
#include <iostream>       // std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myqueue;

  myqueue.push(77);
  myqueue.push(16);

  myqueue.front() -= myqueue.back();    // 77-16=61

  std::cout << "myqueue.front() is now " << myqueue.front() << '\n';

  return 0;
}
```

## 6、std::queue::pop

```cpp
void pop();
```

### 6.1功能

弹出队头元素，删除队列中的队头元素，有效地将其大小减一。 

```cpp
// queue::push/pop
#include <iostream>       // std::cin, std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myqueue;
  int myint;

  std::cout << "Please enter some integers (enter 0 to end):\n";

  do {
    std::cin >> myint;
    myqueue.push (myint);
  } while (myint);

  std::cout << "myqueue contains: ";
  while (!myqueue.empty())
  {
    std::cout << ' ' << myqueue.front();
    myqueue.pop();
  }
  std::cout << '\n';

  return 0;
}
```

## 7、std::queue::push

```cpp
void push (const value_type& val);
void push (value_type&& val);
```

### 7.1功能

在队列的末尾插入一个新元素，在其当前最后一个元素之后。 这个新元素的内容被初始化为 val。 这个成员函数有效地调用了底层容器对象的成员函数 push_back。

### 7.2参数

插入元素的初始化值。 

```cpp
// queue::push/pop
#include <iostream>       // std::cin, std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myqueue;
  int myint;

  std::cout << "Please enter some integers (enter 0 to end):\n";

  do {
    std::cin >> myint;
    myqueue.push (myint);
  } while (myint);

  std::cout << "myqueue contains: ";
  while (!myqueue.empty())
  {
    std::cout << ' ' << myqueue.front();
    myqueue.pop();
  }
  std::cout << '\n';

  return 0;
}
```

## 8、std::queue::size

```cpp
size_type size() const;
```

### 8.1功能

返回底层容器的大小

### 8.2返回值

底层容器的大小

```cpp
// queue::size
#include <iostream>       // std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> myints;
  std::cout << "0. size: " << myints.size() << '\n';

  for (int i=0; i<5; i++) myints.push(i);
  std::cout << "1. size: " << myints.size() << '\n';

  myints.pop();
  std::cout << "2. size: " << myints.size() << '\n';

  return 0;
}
```

## 9、std::queue::swap

```cpp
void swap (queue& x) noexcept(/*see below*/);
```

### 9.1功能

交换本底层容器与x底层容器的内容

```cpp
// queue::swap
#include <iostream>       // std::cout
#include <queue>          // std::queue

int main ()
{
  std::queue<int> foo,bar;
  foo.push (10); foo.push(20); foo.push(30);
  bar.push (111); bar.push(222);

  foo.swap(bar);

  std::cout << "size of foo: " << foo.size() << '\n';
  std::cout << "size of bar: " << bar.size() << '\n';

  return 0;
}
```

# priority_queue：

```cpp
template <class T, class Container = vector<T>,
  class Compare = less<typename Container::value_type> > class priority_queue;
```

# 优先队列

>  经过专门设计，根据一些严格的弱排序标准，它的第一个元素始终是它包含的元素中最大的元素。满足Compare的在队尾，不满足的在队头，所以默认Compare = less，即a<b b在队头，a在队尾

## 参数：

T:元素的类型。

Container：存储元素的内部底层容器对象的类型。

Compare：比较函数

> 注意不要和构造函数参数搞混

## 1、构造函数

|         initialize (1) | `priority_queue (const Compare& comp, const Container& ctnr); ` |
| ---------------------: | ------------------------------------------------------------ |
|              range (2) | `template <class InputIterator>  priority_queue (InputIterator first, InputIterator last,                  const Compare& comp, const Container& ctnr); ` |
|    move-initialize (3) | explicit priority_queue (const Compare& comp = Compare(),<br>                      Container&& ctnr = Container()); |
|         move-range (4) | `template <class InputIterator>  priority_queue (InputIterator first, InputIterator last,                  const Compare& comp, Container&& ctnr = Container()); ` |
| allocator versions (5) | template <class Alloc> explicit priority_queue (const Alloc& alloc); <br>template <class Alloc> priority_queue (const Compare& comp, const Alloc& alloc); <br>template <class Alloc> priority_queue (const Compare& comp, const Container& ctnr,                                       const Alloc& alloc);<br> template <class Alloc> priority_queue (const Compare& comp, Container&& ctnr,                                       const Alloc& alloc); <br>template <class Alloc> priority_queue (const priority_queue& x, const Alloc& alloc);<br> template <class Alloc> priority_queue (priority_queue&& x, const Alloc& alloc); |

### 1.1参数

comp:用于对堆排序的比较对象。 

ctnr :容器对象。

first,last: 将迭代器输入到序列中的初始位置和最终位置。 这个序列中的元素在排序之前被插入到底层容器中。

```cpp
// constructing priority queues
#include <iostream>       // std::cout
#include <queue>          // std::priority_queue
#include <vector>         // std::vector
#include <functional>     // std::greater

class mycomparison
{
  bool reverse;
public:
  mycomparison(const bool& revparam=false)
    {reverse=revparam;}
  bool operator() (const int& lhs, const int&rhs) const
  {
    if (reverse) return (lhs>rhs);
    else return (lhs<rhs);
  }
};

int main ()
{
  int myints[]= {10,60,50,20};

  std::priority_queue<int> first;
  std::priority_queue<int> second (myints,myints+4);
  std::priority_queue<int, std::vector<int>, std::greater<int> >
                            third (myints,myints+4);
  // using mycomparison:
  typedef std::priority_queue<int,std::vector<int>,mycomparison> mypq_type;

  mypq_type fourth;                       // less-than comparison
  mypq_type fifth (mycomparison(true));   // greater-than comparison

  return 0;
}
```

> ##  queue有的priority_queue都有，除了front

## 2、std::priority_queue::top

```cpp
const_reference top() const;
```

### 2.1功能

返回对 priority_queue 中顶部元素的常量引用。

### 2.2返回值

顶部元素的常量引用。

```cpp
// priority_queue::top
#include <iostream>       // std::cout
#include <queue>          // std::priority_queue

int main ()
{
  std::priority_queue<int> mypq;

  mypq.push(10);
  mypq.push(20);
  mypq.push(15);

  std::cout << "mypq.top() is now " << mypq.top() << '\n';

  return 0;
}
```

