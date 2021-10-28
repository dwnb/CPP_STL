# C++_STL——unordered_map、unordered_multimap、unordered_set、unordered_multiset

> 参考：[cplusplus](http://www.cplusplus.com/)

> 这几种都是拉链法所写的，所以放在一起他们有很多相似之处，以下只记录之前没遇到过的成员函数

> 遇到不清楚的可以看[C++_STL——map、multimap](https://blog.csdn.net/asdasdde/article/details/121015373)

## 1、std::unordered_map::bucket

```cpp
size_type bucket ( const key_type& k ) const;
```

### 1.1功能

> 返回键值k所在的桶号，即可以理解为拉链法的数组下标，桶是容器内部哈希表中的一个槽，元素根据其键的哈希值分配到该槽。 桶的编号从 0 到 (bucket_count-1)。可以通过 unordered_map::begin 和 unordered_map::end 返回的范围迭代器访问存储桶中的各个元素。
>

### 1.2参数

K: key

### 1.3返回值

> 返回键值k所在的桶号

```cpp
// unordered_map::bucket
#include <iostream>
#include <string>
#include <unordered_map>

int main ()
{
  std::unordered_map<std::string,std::string> mymap = {
    {"us","United States"},
    {"uk","United Kingdom"},
    {"fr","France"},
    {"de","Germany"}
  };

  for (auto& x: mymap) {
    std::cout << "Element [" << x.first << ":" << x.second << "]";
    std::cout << " is in bucket #" << mymap.bucket (x.first) << std::endl;
  }

  return 0;
}
```

## 2、std::unordered_map::bucket_count

```cpp
size_type bucket_count() const noexcept;
```

### 2.1功能

>  返回有多少个桶, 桶的数量直接影响容器哈希表的负载因子（从而影响冲突的概率）。 为了将负载因子保持在特定阈值（其 max_load_factor）以下，容器会自动增加桶的数量、每次需要增加桶的数量时都会导致重新哈希。

> 负载因子

$$
load\_factor= size / bucket\_count
$$

### 2.2返回值

> 桶的数量

```cpp
// unordered_map::bucket_count
#include <iostream>
#include <string>
#include <unordered_map>

int main ()
{
  std::unordered_map<std::string,std::string> mymap = {
            {"house","maison"},
            {"apple","pomme"},
            {"tree","arbre"},
            {"book","livre"},
            {"door","porte"},
            {"grapefruit","pamplemousse"}
  };

  unsigned n = mymap.bucket_count();

  std::cout << "mymap has " << n << " buckets.\n";

  for (unsigned i=0; i<n; ++i) {
    std::cout << "bucket #" << i << " contains: ";
    for (auto it = mymap.begin(i); it!=mymap.end(i); ++it)
      std::cout << "[" << it->first << ":" << it->second << "] ";
    std::cout << "\n";
  }

  return 0;
}
```

## 3、std::unordered_map::bucket_size

```cpp
size_type bucket_size ( size_type n ) const;
```

### 3.1功能

返回存储桶 n 中的元素数。

### 3.2返回值

桶 n 中的元素数。

```cpp
// unordered_map::bucket_size
#include <iostream>
#include <string>
#include <unordered_map>

int main ()
{
  std::unordered_map<std::string,std::string> mymap = {
    {"us","United States"},
    {"uk","United Kingdom"},
    {"fr","France"},
    {"de","Germany"}
  };

  unsigned nbuckets = mymap.bucket_count();

  std::cout << "mymap has " << nbuckets << " buckets:\n";

  for (unsigned i=0; i<nbuckets; ++i) {
    std::cout << "bucket #" << i << " has " << mymap.bucket_size(i) << " elements.\n";
  }

  return 0;
}
```

## 4、std::unordered_map::hash_function

```cpp
hasher hash_function() const;
```

### 4.1功能

> 获取unordered_map的哈希函数对象

> 哈希函数是一个一元函数，它接受一个 key_type 类型的对象作为参数，并基于它返回一个 size_t 类型的唯一值。 它在构造时被容器采用（有关更多信息，请参阅 unordered_map 的构造函数）。 默认情况下，它是对应键类型的默认散列函数：hash<key_type>。

```cpp
// unordered_map::hash_function
#include <iostream>
#include <string>
#include <unordered_map>

typedef std::unordered_map<std::string,std::string> stringmap;

int main ()
{
  stringmap mymap;

  stringmap::hasher fn = mymap.hash_function();

  std::cout << "this: " << fn ("this") << std::endl;
  std::cout << "thin: " << fn ("thin") << std::endl;

  return 0;
}
```

## 5、std::unordered_map::load_factor、max_bucket_count、max_load_factor

```cpp
float load_factor() const noexcept;
size_type max_bucket_count() const noexcept;
//get (1)	 获取
float max_load_factor() const noexcept;
//set (2)	设置
void max_load_factor ( float z );
```

## 5.1功能

> load_factor：返回 unordered_map 容器中的当前负载因子。

> max_bucket_count:  返回 unordered_map 容器可以拥有的最大桶数。

> max_load_factor: get:  返回 unordered_map 容器的当前最大负载因子。set : 将 z 设置为 unordered_map 容器的新最大负载因子。**默认情况下，unordered_map 容器的 max_load_factor 为 1.0。**

```cpp
// unordered_map hash table stats
#include <iostream>
#include <unordered_map>

int main ()
{
  std::unordered_map<int,int> mymap;

  std::cout << "size = " << mymap.size() << std::endl;
  std::cout << "bucket_count = " << mymap.bucket_count() << std::endl;
  std::cout << "load_factor = " << mymap.load_factor() << std::endl;
  std::cout << "max_load_factor = " << mymap.max_load_factor() << std::endl;

  return 0;
}
```

### 6、std::unordered_map::rehash、reserve

```cpp
void reserve ( size_type n );
void rehash( size_type n );
```

### 6.1功能

> reserve:设置每个桶最少含有的元素数

> rehash: 将容器中的桶数设置为 n 或更多。

```cpp
// unordered_map::reserve
#include <iostream>
#include <string>
#include <unordered_map>

int main ()
{
  std::unordered_map<std::string,std::string> mymap;

  mymap.reserve(6);

  mymap["house"] = "maison";
  mymap["apple"] = "pomme";
  mymap["tree"] = "arbre";
  mymap["book"] = "livre";
  mymap["door"] = "porte";
  mymap["grapefruit"] = "pamplemousse";

  for (auto& x: mymap) {
    std::cout << x.first << ": " << x.second << std::endl;
  }

  return 0;
}
```

```cpp
// unordered_map::rehash
#include <iostream>
#include <string>
#include <unordered_map>

int main ()
{
  std::unordered_map<std::string,std::string> mymap;

  mymap.rehash(20);

  mymap["house"] = "maison";
  mymap["apple"] = "pomme";
  mymap["tree"] = "arbre";
  mymap["book"] = "livre";
  mymap["door"] = "porte";
  mymap["grapefruit"] = "pamplemousse";

  std::cout << "current bucket_count: " << mymap.bucket_count() << std::endl;

  return 0;
}
```





# 无序可重复哈希表unordered_multimap

> 这里主要讲讲和unordered_map的区别部分

## 1、key不重复，value可重复

## 2、无[]直接访问元素、无at()

其余的就都和unordered_map类似了，只有些许不同，具体细节[unordered_multimap)](http://www.cplusplus.com/reference/unordered_map/unordered_multimap/)



# 无续集unordered_set

> 这里主要讲讲和map的区别部分

## 1、key和value是同一个，或者说只有key，且不可重复，且为常量

## 2、无[]直接访问元素、无at()

其余的就都和unordered_map类似了，只有些许不同，具体细节[unordered_set](http://www.cplusplus.com/reference/unordered_set/unordered_set/)





# 可重复无续集unordered_multiset

## 1、key和value是同一个，或者说只有key，且可重复，且为常量

## 2、无[]直接访问元素、无at()

其余的就都和unordered_map类似了，只有些许不同，具体细节[unordered_multiset )](http://www.cplusplus.com/reference/unordered_set/unordered_multiset/)

