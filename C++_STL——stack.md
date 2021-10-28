# C++_STL——stack

```cpp
template <class T, class Container = deque<T> > class stack;
```

# 堆栈

后进先出堆栈

堆栈是一种容器适配器，专门设计用于在 LIFO 上下文（后进先出）中操作，其中元素仅从容器的一端插入和提取。

堆栈被实现为容器适配器，这些类使用特定容器类的封装对象作为其底层容器，提供一组特定的成员函数来访问其元素。 元素从特定容器的“后部”（称为堆栈的顶部）推送/弹出。

标准容器类 vector、deque 和 list 满足这些要求。 默认情况下，如果没有为特定堆栈类实例化指定容器类，则使用标准容器双端队列。 

# 参数

T:元素类型

Container：存储元素的内部底层容器对象的类型。 

具体用法参考[priority_queue](https://blog.csdn.net/asdasdde/article/details/120289625)二者用法相同，区别是priority_queue是用大小堆实现的，是满足条件的最后弹出，stack是先进后出

参考：[stack](http://www.cplusplus.com/reference/stack/)