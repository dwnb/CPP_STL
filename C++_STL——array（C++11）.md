# C++_STL——array（C++11）

## 1、类模板

`template < class T, size_t N > class array;`

### 1.1容器属性

| 容器属性 |                                                              |
| -------- | ------------------------------------------------------------ |
| 序列     | 序列容器中的元素以严格的线性顺序排序。单个元素按其在此序列中的位置访问。 |
| 连续存储 | 这些元素存储在连续的内存位置，允许常数时间随机访问元素。指向元素的指针可以偏移以访问其他元素。 |
| 固定大小 | 容器使用隐式构造函数和析构函数静态分配所需的空间。它的大小是编译时常量。没有内存或时间开销。 |

### 1.2模板参数

| 模板参数 |                                                            |
| -------- | ---------------------------------------------------------- |
| T        | 包含的元素的类型。<br/>别名为成员类型`array::value_type。` |
| N        | 数组的大小，以元素数表示。                                 |

### 1.3例子

```cpp
std::array<int,10> myarray;
```

## 2、std::array::at

```cpp
reference at ( size_type n );
const_reference at ( size_type n ) const;
```

Access element

### 2.1功能

返回对阵列中位置n位置的元素的引用。该函数自动检查 n 是否在容器中有效元素的范围内，`如果不是（即 n 大于或等于其大小），则抛出out_of_range例外`。
`这与成员操作员[]形成鲜明对比，该操作员不检查边界。`

### 2.2参数

| 参数 |                                                              |
| ---- | ------------------------------------------------------------ |
| n    | 数组中元素的位置。如果这大于或等于阵列大小，则抛出类型out_of_range的例外。请注意，第一个元素的位置为0（不是1）。成员类型size_type是未签名积分类型size_t的别名。 |

### 2.3返回值

数组中指定位置的元素。如果数组对象是const限定的，则函数返回一个const_reference。否则，它将返回一个引用。

```cpp
// array::at
#include <iostream>
#include <array>

int main ()
{
  std::array<int,10> myarray;

  // assign some values:
  for (int i=0; i<10; i++) myarray.at(i) = i+1;

  // print content:
  std::cout << "myarray contains:";
  for (int i=0; i<10; i++)
    std::cout << ' ' << myarray.at(i);
  std::cout << '\n';

  return 0;
}
```

## 3、std::array::back

```cpp
reference back();
const_reference back() const;
```

### 3.1功能

返回对阵列容器中最后一个元素的引用

### 3.2返回值

array中最后一个元素的引用，如果数组对象是const限定的，则函数返回一个const_reference。否则，它将返回一个引用。

```cpp
// array::back
#include <iostream>
#include <array>

int main ()
{
  std::array<int,3> myarray = {5, 19, 77};

  std::cout << "front is: " << myarray.front() << std::endl;   // 5
  std::cout << "back is: " << myarray.back() << std::endl;     // 77

  myarray.back() = 50;

  std::cout << "myarray now contains:";
  for ( int& x : myarray ) std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

## 4、std::array::begin

```cpp
iterator begin() noexcept;
const_iterator begin() const noexcept;
```

### 4.1功能

返回阵列容器中指向第一个元素的迭代器iterator，可以对迭代器指向的内容修改

### 4.2返回值

array中指向第一个元素的迭代器iterator ，如果数组对象是const限定的，则函数返回一个const_iterator。否则，它将返回一个iterator 。

```cpp
// array::begin example
#include <iostream>
#include <array>

int main ()
{
  std::array<int,5> myarray = { 2, 16, 77, 34, 50 };

  std::cout << "myarray contains:";
  for ( auto it = myarray.begin(); it != myarray.end(); ++it ){
      *it+=1;
      std::cout << ' ' << *it;
  std::cout << '\n';
      } 
  return 0;
}
```

## 5、std::array::cbegin

```cpp
const_iterator cbegin() const noexcept;
```

### 5.1功能

返回阵列容器中指向第一个元素的const_iterator，不能对迭代器指向的内容修改

### 5.2返回值

array中指向第一个元素const_iterator。

```cpp
// array::cbegin example
#include <iostream>
#include <array>

int main ()
{
  std::array<int,5> myarray = { 2, 16, 77, 34, 50 };

  std::cout << "myarray contains:";

  for ( auto it = myarray.cbegin(); it != myarray.cend(); ++it )
    std::cout << ' ' << *it;   // cannot modify *it

  std::cout << '\n';

  return 0;
}
```

## 6、std::array::cend

```cpp
const_iterator cend() const noexcept;
```

### 6.1功能

返回阵列容器中指向超尾（*past-the-end* ）const_iteratorr。

## 7、std::array::crbegin

```cpp
const_reverse_iterator crbegin() const noexcept;
```

### 7.1功能

返回阵列容器中指向最后一个元素的const_reverse_iterator，不能对迭代器指向的内容修改，`const_reverse_iterator是指向const内容并按相反顺序迭代的迭代器。`

`auto rit=myarray.crbegin() ; rit < myarray.crend(); ++rit`不是rit--

### 7.2返回值

array中指向最后一个元素的const_reverse_iterator。

```cpp
// array::crbegin/crend
#include <iostream>
#include <array>

int main ()
{
  std::array<int,6> myarray = {10, 20, 30, 40, 50, 60} ;

  std::cout << "myarray backwards:";
  for ( auto rit=myarray.crbegin() ; rit < myarray.crend(); ++rit )//rit !=myarray.crend()也可以
    std::cout << ' ' << *rit;   // cannot modify *rit

  std::cout << '\n';

  return 0;
}
```

## 8、std::array::crend

```
const_reverse_iterator crend() const noexcept;
```

### 8.1功能

返回一个const_reverse_iterator，该迭代器指向向量中第一个元素前面的理论元素，该元素被视为其反向端点。

## 9、std::array::data

```cpp
value_type* data() noexcept;
const value_type* data() const noexcept;
```

### 9.1功能

返回一个指向头元素的指针。

`因为数组中的元素存储在相邻的存储位置，所以检索到的指针可以偏移以访问数组中的任何元素。`

```cpp
// array::data
#include <iostream>
#include <cstring>
#include <array>

int main ()
{
  const char* cstr = "Test string";
  std::array<char,12> charray;

  std::memcpy (charray.data(),cstr,12);

  std::cout << charray.data() << '\n';

  return 0;
}
```

## 10、std::array::empty

```cpp
constexpr bool empty() noexcept;
```

### 10.1功能

判断阵列是否为空

### 10.2返回值

如果数组大小为0，则为true，否则为false。

```cpp
// array::empty
#include <iostream>
#include <array>

int main ()
{
  std::array<int,0> first;
  std::array<int,5> second;
  std::cout << "first " << (first.empty() ? "is empty" : "is not empty") << '\n';
  std::cout << "second " << (second.empty() ? "is empty" : "is not empty") << '\n';
  return 0;
}
```

## 11、std::array::end

```cpp
iterator end() noexcept;
const_iterator end() const noexcept;
```

### 11.1功能

返回阵列容器中指向超尾（*past-the-end* ）iteratorr。，可以对迭代器指向的内容修改

## 12、std::array::fill

```cpp
void fill (const value_type& val);
```

### 12.1功能

将val设置为数组对象中所有元素的值。

### 12.2参数

| 参数 |                |
| ---- | -------------- |
| val  | 值来填充数组。 |

```cpp
// array::fill example
#include <iostream>
#include <array>

int main () {
  std::array<int,6> myarray;

  myarray.fill(5);

  std::cout << "myarray contains:";
  for ( int& x : myarray) { std::cout << ' ' << x; }

  std::cout << '\n';

  return 0;
}
```

## 13、std::array::front

```cpp
reference front();
const_reference front() const;
```

### 13、1功能

返回第一个值引用。

### 13.2返回值

array中第一个元素的引用，如果数组对象是const限定的，则函数返回一个const_reference。否则，它将返回一个引用。

```
// array::front
#include <iostream>
#include <array>

int main ()
{
  std::array<int,3> myarray = {2, 16, 77};

  std::cout << "front is: " << myarray.front() << std::endl;   // 2
  std::cout << "back is: " << myarray.back() << std::endl;     // 77

  myarray.front() = 100;

  std::cout << "myarray now contains:";
  for ( int& x : myarray ) std::cout << ' ' << x;

  std::cout << '\n';

  return 0;
}
```

## 14、std::array::max_size

```cpp
constexpr size_type max_size() noexcept;
```

### 14.1功能

返回数组容器可以容纳的最大元素数。

数组对象的最大大小和它的大小一样，总是等于用于实例化数组模板类的第二个模板参数。

```cpp
// array::max_size
#include <iostream>
#include <array>

int main ()
{
  std::array<int,10> myints;
  std::cout << "size of myints: " << myints.size() << '\n';
  std::cout << "max_size of myints: " << myints.max_size() << '\n';

  return 0;
}
```

## 15、std::array::operator[]

```cpp
reference operator[] (size_type n);
const_reference operator[] (size_type n) const;
```

### 15.1功能

返回数组容器中位置n处元素的引用。类似的成员函数array::at的行为与此运算符函数相同，只是array::at检查数组边界并通过抛出异常来指示n是否超出范围。此成员函数超出边界会出错。

### 15.2返回值

| 参数 |                    |
| ---- | ------------------ |
| n    | 数组中元素的位置。 |

```cpp
// array::operator[]
#include <iostream>
#include <array>

int main ()
{
  std::array<int,10> myarray;
  unsigned int i;

  // assign some values:
  for (i=0; i<10; i++) myarray[i]=i;

  // print content
  std::cout << "myarray contains:";
  for (i=0; i<10; i++)
    std::cout << ' ' << myarray[i];
  std::cout << '\n';

  return 0;
}
```

## 16、std::array::rbegin

```cpp
reverse_iterator rbegin() noexcept;
const_reverse_iterator rbegin() const noexcept;
```

### 16.1功能

返回阵列容器中指向最后一个元素的迭代器，可以对迭代器指向的内容修改，const限定则不能。

### 16.2返回值

一个反向迭代器，用于反转序列的开头。如果数组对象是const限定的，则函数返回一个const_iterator。否则，它将返回一个迭代器。

成员类型reverse_iterator和const_reverse_iterator是反向随机访问迭代器类型（分别指向元素和常量元素）。

```cpp
// array::rbegin/rend
#include <iostream>
#include <array>

int main ()
{
  std::array<int,4> myarray = {4, 26, 80, 14} ;

  std::cout << "myarray contains:";
  for ( auto rit=myarray.rbegin() ; rit < myarray.rend(); ++rit )
    std::cout << ' ' << *rit;

  std::cout << '\n';

  return 0;
}
```

## 17、std::array::rend

```cpp
reverse_iterator rend() noexcept;
const_reverse_iterator rend() const noexcept;
```

### 17.1功能

返回一个反向迭代器，该迭代器指向数组中第一个元素前面的理论元素（该元素被视为其反向端）。

### 17.2返回值

序列的反向末端的反向迭代器。如果数组对象是const限定的，则函数返回一个const_iterator。否则，它将返回一个迭代器。

成员类型reverse_iterator和const_reverse_iterator是反向随机访问迭代器类型（分别指向元素和常量元素）。

## 18、std::array::size

```cpp
constexpr size_type size() noexcept;
```

### 18.1功能

返回数组容器中的元素数。数组对象的大小始终等于用于实例化数组模板类（N）的第二个模板参数。与以字节为单位返回大小的语言运算符sizeof不同，此成员函数以元素数为单位返回数组的大小。

```cpp
// array::size
#include <iostream>
#include <array>

int main ()
{
  std::array<int,5> myints;
  std::cout << "size of myints: " << myints.size() << std::endl;
  std::cout << "sizeof(myints): " << sizeof(myints) << std::endl;

  return 0;
}
```

## 19、std::array::swap

```cpp
void swap (array& x) noexcept(noexcept(swap(declval<value_type&>(),declval<value_type&>())));
```

### 19.1功能

通过x的内容交换数组的内容，x是另一个相同类型（包括相同大小）的数组对象。在调用这个成员函数之后，这个容器中的元素是调用之前在x中的元素，而x的元素是这个容器中的元素。与其他容器的swap成员函数不同，该成员函数通过在单个元素之间执行与其大小相同的单个swap操作，以线性时间运行（请参见swap）。

### 19.2复杂度

Linear in size of the container.
$$
O(n)
$$
n指容器大小

```cpp
// swap arrays
#include <iostream>
#include <array>

int main ()
{
  std::array<int,5> first = {10, 20, 30, 40, 50};
  std::array<int,5> second = {11, 22, 33, 44, 55};

  first.swap (second);

  std::cout << "first:";
  for (int& x : first) std::cout << ' ' << x;
  std::cout << '\n';

  std::cout << "second:";
  for (int& x : second) std::cout << ' ' << x;
  std::cout << '\n';

  return 0;
}
```

# non-member overloads:

## std::get (array)

```cpp
template <size_t I, class T, size_t N> T& get (array<T,N>& arr) noexcept;
template <size_t I, class T, size_t N> T&& get (array<T,N>&& arr) noexcept;
template <size_t I, class T, size_t N> const T& get (const array<T,N>& arr) noexcept;
```

### 1.功能

返回对数组arr的第i个元素的引用。

### 2.参数

An array container.

### 3.返回值

对数组中指定位置的元素的引用。

```cpp
// arrays as tuples
#include <iostream>
#include <array>
#include <tuple>

int main ()
{
  std::array<int,3> myarray = {10, 20, 30};
  std::tuple<int,int,int> mytuple (10, 20, 30);

  std::tuple_element<0,decltype(myarray)>::type myelement;  // int myelement

  myelement = std::get<2>(myarray);
  std::get<2>(myarray) = std::get<0>(myarray);
  std::get<0>(myarray) = myelement;

  std::cout << "first element in myarray: " << std::get<0>(myarray) << "\n";
  std::cout << "first element in mytuple: " << std::get<0>(mytuple) << "\n";

  return 0;
}
```

## std::relational operators (array)

|  (1) | `template <class T, size_T N>  bool operator== ( const array<T,N>& lhs, const array<T,N>& rhs ); ` |
| ---: | ------------------------------------------------------------ |
|  (2) | `template <class T, size_T N>  bool operator!= ( const array<T,N>& lhs, const array<T,N>& rhs ); ` |
|  (3) | `template <class T, size_T N>  bool operator<  ( const array<T,N>& lhs, const array<T,N>& rhs ); ` |
|  (4) | `template <class T, size_T N>  bool operator<= ( const array<T,N>& lhs, const array<T,N>& rhs ); ` |
|  (5) | `template <class T, size_T N>  bool operator>  ( const array<T,N>& lhs, const array<T,N>& rhs ); ` |
|  (6) | `template <class T, size_T N>  bool operator>= ( const array<T,N>& lhs, const array<T,N>& rhs );` |

## 1.功能

比较大小，相同==，不相同!=，大于> T与N的第一个不同的地方是否大于N，小于<T与N的第一个不同的地方是否小于N

## 2.参数

two array container.

### 3.返回值

true or false。

```cpp
// array comparisons
#include <iostream>
#include <array>

int main ()
{
  std::array<int,5> a = {10, 20, 30, 40, 50};
  std::array<int,5> b = {10, 20, 30, 40, 50};
  std::array<int,5> c = {50, 40, 30, 20, 10};

  if (a==b) std::cout << "a and b are equal\n";
  if (b!=c) std::cout << "b and c are not equal\n";
  if (b<c) std::cout << "b is less than c\n";
  if (c>b) std::cout << "c is greater than b\n";
  if (a<=b) std::cout << "a is less than or equal to b\n";
  if (a>=b) std::cout << "a is greater than or equal to b\n";

  return 0;
}
```

## std::swap(std::array)

### 1.功能

同std::array::swap一样，复杂度一样

### 2.参数

two array container.