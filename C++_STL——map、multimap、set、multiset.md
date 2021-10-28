# C++_STL——map、multimap、set、multiset

> 这里[专栏](https://blog.csdn.net/asdasdde/category_11148563.html)里其他文章提到的函数（方法）就不会再说

# 有序哈希表

## 有序不可重复哈希表（映射）map

```cpp
template < class Key,                                     // map::key_type
           class T,                                       // map::mapped_type
           class Compare = less<Key>,                     // map::key_compare
           class Alloc = allocator<pair<const Key,T> >    // map::allocator_type
           > class map;
```

> 映射是关联容器，它按照特定顺序存储由键值和映射值组合形成的元素。
>
> 在映射中，键值通常用于对元素进行排序和唯一标识，而映射值存储与该键关联的内容。键和映射值的类型可能不同，并在成员类型 value_type 中组合在一起;
>
> typedef pair<const Key, T> value_type;
>
> 在内部，映射中的元素总是按照其内部比较对象（类型比较）指示的特定严格弱排序标准按其键排序。map与multimap红黑树（高度平衡树）实现的
>
> map 容器通常比 unordered_map 容器通过它们的键访问单个元素慢，但它们允许根据它们的顺序直接迭代子集。
>
> 映射通常被实现为二叉搜索树。

### 属性：

> 关联容器中的元素通过它们的键而不是它们在容器中的绝对位置来引用。
>
> 容器中的元素始终遵循严格的顺序。 所有插入的元素都按此顺序指定一个位置。
>
> 每个元素都将一个键与映射值相关联：键用于标识主要内容是映射值的元素。
>
> 容器中没有两个元素可以具有等效键。
>
> 容器使用分配器对象来动态处理其存储需求。

### 参数：

key： 键值类型

T: 元素类型

compare：比较类型

Alloc：分配器

## 1、构造函数

参数 key_compare是比较函数

| empty (1) | `explicit map (const key_compare& comp = key_compare(),              const allocator_type& alloc = allocator_type()); ` |
| --------: | ------------------------------------------------------------ |
| range (2) | `template <class InputIterator>  map (InputIterator first, InputIterator last,       const key_compare& comp = key_compare(),       const allocator_type& alloc = allocator_type()); ` |
|  copy (3) | `map (const map& x);`                                        |

>  下表的classcomp又称伪函数

```cpp
// constructing maps
#include <iostream>
#include <map>

bool fncomp (char lhs, char rhs) {return lhs<rhs;}

struct classcomp {
  bool operator() (const char& lhs, const char& rhs) const
  {return lhs<rhs;}
};

int main ()
{
  std::map<char,int> first;

  first['a']=10;
  first['b']=30;
  first['c']=50;
  first['d']=70;

  std::map<char,int> second (first.begin(),first.end());

  std::map<char,int> third (second);

  std::map<char,int,classcomp> fourth;                 // class as Compare

  bool(*fn_pt)(char,char) = fncomp;
  std::map<char,int,bool(*)(char,char)> fifth (fn_pt); // function pointer as Compare

  return 0;
}
```

## 2、std::map::equal_range

```cpp
pair<const_iterator,const_iterator> equal_range (const key_type& k) const;
pair<iterator,iterator>             equal_range (const key_type& k);
```

### 2.1功能

返回一个范围（迭代器）的边界，该范围包括容器中具有等效于 k 的键的所有元素。如果未找到匹配项，则返回的范围长度为零，两个迭代器都指向根据容器的内部比较对象 (key_comp) 被认为在 k 之后的第一个具有键的元素。

### 2.2参数

要搜索的键。

### 2.3返回值

> ```cpp
> pair<iterator,iterator> 
> ```
>
> 第一个迭代器是范围的左边界与lower_bound一样 ，第二个迭代器是右边界upper_bound 一样[iterator,iterator)

函数返回一个pair，其成员pair::first 是范围的下界（与lower_bound 相同），pair::second 是上界（与upper_bound 相同）。如果映射对象是 const 限定的，则该函数返回一对 const_iterator。 否则，它返回一对迭代器。成员类型 iterator 和 const_iterator 是指向元素（类型 value_type）的双向迭代器类型。请注意，map 容器中的 value_type 本身也是一个对类型：pair<const key_type,mapped_type>。 

```cpp
// map::equal_range
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;

  mymap['a']=10;
  mymap['b']=20;
  mymap['c']=30;

  std::pair<std::map<char,int>::iterator,std::map<char,int>::iterator> ret;
  ret = mymap.equal_range('b');

  std::cout << "lower bound points to: ";
  std::cout << ret.first->first << " => " << ret.first->second << '\n';

  std::cout << "upper bound points to: ";
  std::cout << ret.second->first << " => " << ret.second->second << '\n';

  return 0;
}
```

## 3、std::map::erase

|  (1) | `iterator  erase (const_iterator position); `                |
| ---: | ------------------------------------------------------------ |
|  (2) | `size_type erase (const key_type& k); `                      |
|  (3) | `iterator  erase (const_iterator first, const_iterator last);` |

### 3.1功能

从地图容器中删除单个元素或一系列元素 ([first,last))。这通过移除的元素数量有效地减小了容器的大小，这些元素被销毁了。 

### 3.2参数

position：指向要从地图中删除的单个元素的迭代器。

K: 要从地图中删除的元素的键。成员类型 key_type 是容器中元素的类型，在 map 中定义为其第一个模板参数（Key）的别名。 

first, last：迭代器指定要删除的地图容器内的范围：[first,last)。 即范围包括 first 和 last 之间的所有元素，包括 first 指向的元素但不包括 last 指向的元素。成员类型 iterator 和 const_iterator 是指向元素的双向迭代器类型。 

### 3.3返回值

对于基于键的版本 (2)，该函数返回擦除的元素数。成员类型 size_type 是无符号整数类型。 其他版本返回一个迭代器，指向最后一个被移除元素（或 map::end，如果最后一个元素被移除）之后的元素。 

```cpp
// erasing from map
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;
  std::map<char,int>::iterator it;

  // insert some values:
  mymap['a']=10;
  mymap['b']=20;
  mymap['c']=30;
  mymap['d']=40;
  mymap['e']=50;
  mymap['f']=60;

  it=mymap.find('b');
  mymap.erase (it);                   // erasing by iterator

  mymap.erase ('c');                  // erasing by key

  it=mymap.find ('e');
  mymap.erase ( it, mymap.end() );    // erasing by range

  // show content:
  for (it=mymap.begin(); it!=mymap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  return 0;
}
```

## 4、std::map::find

```cpp
iterator find (const key_type& k);
const_iterator find (const key_type& k) const;
```

### 4.1功能

在容器中搜索具有与 k 等价的键的元素，如果找到则返回一个迭代器，否则返回一个迭代器到 map::end。另一个成员函数 map::count 可用于仅检查特定键是否存在。 

### 4.2参数

K:要搜索的键。成员类型 key_type 是容器中元素的键类型，在 map 中定义为其第一个模板参数 (Key) 的别名。 

### 4.3返回值

如果找到具有指定键的元素，则为元素的迭代器，否则为 map::end。

```cpp
// map::find
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;
  std::map<char,int>::iterator it;

  mymap['a']=50;
  mymap['b']=100;
  mymap['c']=150;
  mymap['d']=200;

  it = mymap.find('b');
  if (it != mymap.end())
    mymap.erase (it);

  // print content:
  std::cout << "elements in mymap:" << '\n';
  std::cout << "a => " << mymap.find('a')->second << '\n';
  std::cout << "c => " << mymap.find('c')->second << '\n';
  std::cout << "d => " << mymap.find('d')->second << '\n';

  return 0;
}
```

## 5、std::map::count

```cpp
size_type count (const key_type& k) const;
```

### 5.1功能

使用特定键计算元素 在容器中搜索键等于 k 的元素并返回匹配的数量。 由于地图容器中的所有元素都是唯一的，因此该函数只能返回 1（如果找到该元素）或零（否则）。 如果容器的比较对象反射性返回 false（即，无论键作为参数传递的顺序如何），则两个键被认为是等效的。 

```cpp
// map::count
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;
  char c;

  mymap ['a']=101;
  mymap ['c']=202;
  mymap ['f']=303;

  for (c='a'; c<'h'; c++)
  {
    std::cout << c;
    if (mymap.count(c)>0)
      std::cout << " is an element of mymap.\n";
    else 
      std::cout << " is not an element of mymap.\n";
  }

  return 0;
}
```

## 6、std::map::key_comp

```cpp
key_compare key_comp() const;
```

### 6.1功能：

> 返回容器用于比较键值的比较对象的副本。

### 6.2返回值

比较对象。 成员类型 key_compare 是与容器关联的比较对象的类型，在 map 中定义为其第三个模板参数 (Compare) 的别名。 

```cpp
// map::key_comp
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;

  std::map<char,int>::key_compare mycomp = mymap.key_comp();

  mymap['a']=100;
  mymap['b']=200;
  mymap['c']=300;

  std::cout << "mymap contains:\n";

  char highest = mymap.rbegin()->first;     // key value of last element

  std::map<char,int>::iterator it = mymap.begin();
  do {
    std::cout << it->first << " => " << it->second << '\n';
  } while ( mycomp((*it++).first, highest) );

  std::cout << '\n';

  return 0;
}
```

## 7、std::map::insert

```cpp
//single element (1)	单一元素插入
pair<iterator,bool> insert (const value_type& val);
template <class P> pair<iterator,bool> insert (P&& val);
//with hint (2)	 指定位置插入
iterator insert (const_iterator position, const value_type& val);
template <class P> iterator insert (const_iterator position, P&& val);
//range (3)	 //迭代器范围插入
template <class InputIterator>
  void insert (InputIterator first, InputIterator last);
//initializer list (4)	列表初始化插入
void insert (initializer_list<value_type> il);
```

### 7.1功能

插入新元素，并扩展容器大小

### 7.2参数

> val: 要复制到（或移动为）插入元素的值。

> position：提示插入元素的位置，这只是一个提示，并不会强制将新元素插入到地图容器内的该位置（哈希表的元素始终遵循特定的顺序，具体取决于它们的键）。

> first,last:  指定元素范围的迭代器。 范围 [first,last) 中元素的副本被插入到容器中。注意，范围包括 first 和 last 之间的所有元素，包括 first 指向的元素但不包括 last 指向的元素。函数模板参数 InputIterator 应该是一个输入迭代器类型，它指向可以构造 value_type 对象的类型的元素。

> il: 一个 initializer_list 对象。 插入这些元素的副本。这些对象是从初始化列表声明符自动构造的。成员类型 value_type 是包含在容器中的元素的类型，在 map 中定义为 pair<const key_type,mapped_type>

### 7.3返回值

>  单元素版本返回新插入的键值对，其成员 pair::first 设置为指向新插入元素或映射中具有等效键的元素的迭代器。 如果插入了新元素，则pair::second 元素设置为true，如果等效键已经存在，则设置为false。其余版本返回一个迭代器，该迭代器指向新插入的元素或映射中已经具有等效键的元素

```cpp
// map::insert (C++98)
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;

  // first insert function version (single parameter):
  mymap.insert ( std::pair<char,int>('a',100) );
  mymap.insert ( std::pair<char,int>('z',200) );

  std::pair<std::map<char,int>::iterator,bool> ret;
  ret = mymap.insert ( std::pair<char,int>('z',500) );//已经存在的会插入失败
  if (ret.second==false) {
    std::cout << "element 'z' already existed";
    std::cout << " with a value of " << ret.first->second << '\n';
  }

  // second insert function version (with hint position):
  std::map<char,int>::iterator it = mymap.begin();
  mymap.insert (it, std::pair<char,int>('b',300));  // max efficiency inserting
  mymap.insert (it, std::pair<char,int>('c',400));  // no max efficiency inserting //默认是按key从小到达排序，c比b大所以不能插入it处

  // third insert function version (range insertion):
  std::map<char,int> anothermap;
  anothermap.insert(mymap.begin(),mymap.find('c'));

  // showing contents:
  std::cout << "mymap contains:\n";
  for (it=mymap.begin(); it!=mymap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  std::cout << "anothermap contains:\n";
  for (it=anothermap.begin(); it!=anothermap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  return 0;
}
```

## 8、std::map::lower_bound、std::map::upper_bound

```cpp
iterator lower_bound (const key_type& k);
const_iterator lower_bound (const key_type& k) const;
```



### 8.1功能

>  lower_bound：返回指向小于等于K键值（KEY最大的那个）的处的迭代器，与算法的lower_bound功能一样，但用类内部的一般要比外部的快

> upper_bound:   返回指向大于K键值（KEY最小的那个）的处的迭代器，与算法的lower_bound功能一样，但用类内部的一般要比外部的快 

### 8.2参数

> k:  要查找的键。

### 8.3返回值

> 迭代器类型

```cpp
// map::lower_bound/upper_bound
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> mymap;
  std::map<char,int>::iterator itlow,itup;

  mymap['a']=20;
  mymap['b']=40;
  mymap['c']=60;
  mymap['d']=80;
  mymap['e']=100;

  itlow=mymap.lower_bound ('b');  // itlow points to b
  itup=mymap.upper_bound ('d');   // itup points to e (not d!)

  mymap.erase(itlow,itup);        // erases [itlow,itup)//删除[itlow,itup)范围的元素

  // print content:
  for (std::map<char,int>::iterator it=mymap.begin(); it!=mymap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  return 0;
}
```

## 9、std::map::operator=

```cpp
//copy (1)	
map& operator= (const map& x);
//move (2)	
map& operator= (map&& x);
//initializer list (3)	
map& operator= (initializer_list<value_type> il);
```

### 9.1功能

> 将新内容分配给容器，替换其当前内容。

### 9.2参数

> x: 要赋值的同种类型的对象（x理解为被赋值）

> y: 列表初始化对象

### 9.3返回值

> this指针的解引用

```cpp
// assignment operator with maps
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> first;
  std::map<char,int> second;

  first['x']=8;
  first['y']=16;
  first['z']=32;

  second=first;                // second now contains 3 ints
  first=std::map<char,int>();  // and first is now empty

  std::cout << "Size of first: " << first.size() << '\n';
  std::cout << "Size of second: " << second.size() << '\n';
  return 0;
}
```

## 10、std::map::operator[]

```cpp
mapped_type& operator[] (const key_type& k);
mapped_type& operator[] (key_type&& k);
```

### 10.1功能

> 通过键值直接获取value，若该键值不存在，直接插入，**如果 k 与容器中任何元素的键都不匹配，则该函数会插入一个具有该键的新元素并返回对其映射值的引用。 注意，即使没有为元素分配映射值（元素是使用其默认构造函数构造的），这始终会将容器大小增加 1**。

### 10.2参数

k：key

### 10.3返回值

> 对具有等效于 k 的键值的元素的映射值的引用。

```cpp
// accessing mapped values
#include <iostream>
#include <map>
#include <string>

int main ()
{
  std::map<char,std::string> mymap;

  mymap['a']="an element";//相当于插入，先是默认构造插入进去，在将string赋值
  mymap['b']="another element";//相当于插入
  mymap['c']=mymap['b'];//相当于插入

  std::cout << "mymap['a'] is " << mymap['a'] << '\n';
  std::cout << "mymap['b'] is " << mymap['b'] << '\n';
  std::cout << "mymap['c'] is " << mymap['c'] << '\n';
  std::cout << "mymap['d'] is " << mymap['d'] << '\n';//插入d，调用默认构造

  std::cout << "mymap now contains " << mymap.size() << " elements.\n";

```

## 11、std::map::swap

```cpp
void swap (map& x);
```
### 11.1功能
> 用 x 的内容交换容器的内容，这是另一个同类型的映射。 尺寸可能不同。调用此成员函数后，此容器中的元素为调用前x 中的元素，x 中的元素为this 中的元素。 所有迭代器、引用和指针对交换的对象仍然有效。

```cpp
// swap maps
#include <iostream>
#include <map>

int main ()
{
  std::map<char,int> foo,bar;

  foo['x']=100;
  foo['y']=200;

  bar['a']=11;
  bar['b']=22;
  bar['c']=33;

  foo.swap(bar);

  std::cout << "foo contains:\n";
  for (std::map<char,int>::iterator it=foo.begin(); it!=foo.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  std::cout << "bar contains:\n";
  for (std::map<char,int>::iterator it=bar.begin(); it!=bar.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';

  return 0;
}
```

# 有序可重复哈希表multimap

> 这里主要讲讲和map的区别部分

## 1、key不重复，value可重复，按key的的顺序存储，严格按照弱序排列，即从小到大

> 在内部，multimap 中的元素总是按照其内部比较对象（类型比较）指示的特定严格弱排序标准按其键排序。相同key值按照插入顺序排列

## 2、无[]直接访问元素、无at()

其余的就都和map类似了，只有些许不同，具体细节[multimap](http://www.cplusplus.com/reference/map/multimap/)





# 有续集set

> 这里主要讲讲和map的区别部分

## 1、相当于只有value，且value不可重复，按value的的顺序存储，严格按照弱序排列，即从小到大，且所存储的值不可修改（常量）

> 在内部，set中的元素总是按照其内部比较对象（类型比较）指示的特定严格弱排序标准按其键排序。

## 2、无[]直接访问元素、无at()

其余的就都和map类似了，只有些许不同，具体细节[set](http://www.cplusplus.com/reference/set/set/)



# 可重复有续集set

## 1、相当于只有value，且value可重复，按value的的顺序存储，严格按照弱序排列，即从小到大，且所存储的值不可修改（常量）

> 在内部，multimap 中的元素总是按照其内部比较对象（类型比较）指示的特定严格弱排序标准按其键排序。相同key值按照插入顺序排列

## 2、无[]直接访问元素、无at()

其余的就都和map类似了，只有些许不同，具体细节[multiset - C++ Reference (cplusplus.com)](http://www.cplusplus.com/reference/set/multiset/)
