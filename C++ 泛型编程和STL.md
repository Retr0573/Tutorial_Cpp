# C++ 泛型编程和STL

# 1 模板

# 2 STL概述

**C++标准模板库（Standard Template Library，STL）**是C++的一个强大的特性，其设计哲学是**泛型编程**，它强调使用**模板**来实现算法和数据结构的通用性。因此它提供了一系列的模板类和函数，用于处理数据结构和算法，使得C++程序的编写更加高效和可重用。

STL是C++中模板编程的典型应用，它包括以下**六大组件**：

1. **容器（Containers）**：STL提供了多种容器，用于存储数据集合。这些容器包括：
    - **顺序容器**：如`vector`（动态数组）、`deque`（双端队列）、`list`（双向链表）等。
    - **关联容器**：通常**基于平衡二叉搜索树（如红黑树）**实现。
        - `set`（唯一元素的集合）
        - `multiset`（允许重复元素的集合）
        - `map`（键值对集合）
        - `multimap`（允许重复键的键值对集合）
    - **无序关联容器**：**基于哈希表**实现。
        - `unordered_set`
        - `unordered_multiset`
        - `unordered_map`
        - `unordered_multimap`。
2. **迭代器（Iterators）**：迭代器是STL中非常关键的概念，它允许程序员**以统一的方式访问容器中的数据，而无需关心容器的具体实现**。迭代器有多种类型，包括输入迭代器、输出迭代器、前向迭代器、双向迭代器和随机访问迭代器。
3. **算法（Algorithms）**：STL提供了大量的算法，用于对容器中的数据执行操作，如排序、搜索、复制、变换等。这些算法包括：
    - **非修改性序列操作**：如`find`、`count`、`mismatch`等。
    - **修改性序列操作**：如`copy`、`remove`、`reverse`等。
    - **排序和搜索**：如`sort`、`stable_sort`、`binary_search`等。
    - **数值算法**：如`accumulate`、`inner_product`、`partial_sum`等。
4. **仿函数（Function Object）**：在C++中，仿函数是一种特殊的对象，它的行为类似于函数，可以被当作函数来使用。仿函数通常重载了函数调用操作符 `operator()`，使得对象可以像函数一样被调用。它们在C++标准模板库（STL）中广泛使用，特别是在算法中作为参数传递，以提供自定义的行为。
5. **适配器（Adaptors）**：STL提供了一些适配器，用于改变已有容器或算法的行为。这些适配器包括：
    - **流迭代器**：如`istream_iterator`和`ostream_iterator`，它们允许使用流作为迭代器。
    - **配对迭代器**：如`pair`，它允许将两个元素组合成一个对。
    - **堆配器**：如`priority_queue`，它允许使用容器作为堆。
6. **分配器（Allocators）**：分配器是STL中用于管理内存分配和释放的组件。默认情况下，STL容器使用标准分配器，但程序员也可以定义自己的分配器。

# 3 容器

## 3.1 容器概览

一个容器就是一些特定类型对象的集合，**顺序容器**提供控制元素存储和访问顺序的能力（因为顺序容器中的元素按照插入顺序排列），而**关联容器**则根据关键字的值来存储元素。

所有的容器类都共享公共接口，这使得容器的学习与使用更容易；而不同容器按不同方式对其扩展，提供不同性能和功能的权衡。以下是公共的容器操作：

- 基本操作
    
    ```cpp
    // 1. 通过构造函数定义并初始化容器
    C c; 
    // 1.1. 构造c2的拷贝c1
    C c1(c2);  
    C c1 = c2;
    // 1.2. 构造c，将迭代器b和e指定范围内的元素拷贝到c中（array不支持）
    C c(b, e);  
    // 1.3. 列表初始化c
    C c{a, b, c, ...}; 
    C c = {a, b, c, ...};
    
    // 2. 赋值
    c1 = c2;  // 将c1中元素替换为c2中元素
    c1 = {a, b, c};  // 将c1中元素替换为列表中元素（array不支持）
    seq.assign(b, e)  // 将seq中的元素替代为迭代器b和e指定范围内的元素（array不支持）
    
    // 3. 交换
    a.swap(b);  
    swap(a, b);  // 同上
    
    // 4. 大小
    c.size();  // c中元素的数量（forward_list不支持，因为forward_list设计目的是达到与最好的手写单向链表数据结构性能相当，所以不考虑size操作以免额外开销）
    c.max_size();  // c可保存的最大元素数量
    c.empty(); 
    ```
    
- 关于迭代器
- 获取迭代器
    
    ```cpp
    c.begin();  // iterator(首元素)
    c.end();  // iterator(尾元素之后位置)
    
    c.cbegin();  // const_iterator（可读不可修改）
    c.cend(); 
    
    c.rbegin();  // reverse_iterator(尾元素)
    c.rend();   // reverse_iterator(首元素之前位置)
    
    c.crbegin();  // const_reverse_iterator
    c.crend();
    ```
    

## 3.2 顺序容器

**顺序容器**中的元素按照插入顺序排列，可以快速地顺序访问其中元素。但是在增删代价、随机访问能力这两方面，各类顺序容器的性能各有各自的取舍。

以下是STL中的顺序容器：

| 容**器**                 | 优点                                 | 缺点                                                 | 适用场景                                                       |
| ------------------------ | ------------------------------------ | ---------------------------------------------------- | -------------------------------------------------------------- |
| vector（可变大小数组）   | 支持快速随机访问                     | 尾部之外的位置增删元素比较慢（可能需要移动大量元素） | 需要随机访问，但不需要频繁在头部和中间插入和删除操作的场景     |
| deque（双端队列）        | 支持快速随机访问，                   |
| 支持在两端高效增删元素   | 首尾之外的位置的增删效率较低         | 需要在两端进行频繁插入和删除操作的场景               |
| list（双向链表）         | 任何位置增删都很快                   | 只支持双向顺序访问，不支持随机访问                   | 需要频繁在中间位置进行插入和删除操作，但不要求随机访问的场景。 |
| forward_list（单向链表） | 任何位置增删都很快                   | 只支持单向顺序访问，不支持随机访问                   |                                                                |
| array（固定大小数组）    | 支持快速随机访问                     | 不能添加或删除元素                                   |                                                                |
| string（字符串）         | 丰富的字符串操作函数，方便处理字符串 |                                                      | 需要处理字符串的场景，如文本处理、解析等                       |

### 3.2.1 顺序容器定义和初始化

顺序容器的定义和初始化基本上都遵循公共的容器构造函数，但顺序容器的构造函数还可以额外接受大小参数（`array`除外）：

```cpp
C c(n);  // 定义并初始化c为n个元素，由于初始值没指定，此时标准库会自己创建一个值初始化器来进行值初始化，具体初始值依赖数据类型。
C c(n,t);  // 定义并初始化c为n个初始值为t的元素。
C c = {a, b, ...}; // 列表初始化（C++11 及以上）
```

### 3.2.2 顺序容器访问元素

**（1）`.front()`和`.back()`**

包括array在内的每个顺序容器都有一个front成员函数函数；除了forward_list之外的所有顺序容器都有一个back成员函数。这俩个函数分别返回首元素和尾元素的**引用**。

```cpp
vector<int> v{1, 2, 3}; // v: {1,2,3}
auto v_1 = v.front(); // v_1: 1
auto v_2 = v.back();  // v_2: 3
v.front() = 3; // v: {3,2,3}
v.back() = 1; // v: {3,2,1}
auto &v_back = v.back();
v_back = 4; // v:{3,2,4}
```

**（2）迭代器**

通过迭代器，可以间接地获得首尾元素的引用。

```cpp
vector<int> v{1, 2, 3};
auto v_1 = *v.begin(); // v_1: 1
auto v_2 = *(--v.end()); // v_2: 3
*v.begin() = 3; // v: {3,2,3}
*(v.end() - 1) = 1; // v: {3,2,1} 注意end指向的是尾元素之后的位置，为了获取尾元素需要递减一次
```

**(3) 下标操作**

可以快速随机访问的容器（string、vector、deque、array）都提供下标运算符或者at成员函数。

at成员函数会判断下标越界情况并抛出out_of_range异常。

```cpp
vector<int> v; // 空vector
cout << v[0]; // 运行中错误
cout << v.at(0); // 抛出out_of_range异常
```

### 3.2.3 顺序容器添加元素

主要有push、insert、emplace三类方法。

- **`forward_list`** 有自己专有版本的insert和emplace，并且不支持 `push_back()` 和 `emplace_back()` 。
- **`vector`** 和 **`string`** 不支持 `push_front()` 和 `emplace_front()` 。
- **`array`** 无法增删元素。

**（1）push**

在容器的尾部或者头部插入元素。

```cpp
// 1. push_back()
// 除了forward_list和array外，每个顺序容器都支持push_back()，可将一个元素添加到容器尾部。
c.push_back(x);

// 2. push_front()
// list、forward_list和deque支持push_front()，可将一个元素添加到容器头部（由于可以方便地在头部增删数据）。
c.push_front(x);
```

**（2）insert**

在容器的任意位置插入元素。

```cpp
// 1. 在迭代器iter指向的元素之前创建一个值为x的元素，返回指向该元素的迭代器
c.insert(iter, x);  

// 2. 在迭代器iter指向的元素之前创建n个值为x的元素，返回指向新增第一个元素的迭代器
c.insert(iter, n, x);  

// 3. 在迭代器iter_p指向的元素之前插入iter_b和iter_e范围内的元素，返回指向新增的第一个元素的迭代器。
c.insert(iter_p, iter_b, iter_e);
```

**（3）emplace**

C++的新标准引入了`emplace_front()` 、`emplace()` 和 `emplace_back()` ，分别对应`push_front()`、`insert()`和`push_back()` 。

push、insert函数会把参数拷贝到容器中，而emplace会将参数传递给该参数元素类型的构造函数，然后在容器所管理的内存空间中直接构造元素。因此，emplac通过在容器内直接构造元素来避免不必要的拷贝或移动操作，通常比push和insert更高效。

```cpp
// 以emplace_back()为例
class Position
{
public:
    Position(int x, int y) : x(x), y(y)
    {
        std::cout << "Position constructed with x: " << x << ", y: " << y << std::endl;
    }

private:
    int x, y;
};

int main()
{

    vector<Position> position;
    // 正确，直接在vector末尾构造一个Position对象
    position.emplace_back(1, 2);
	  // 正确，创建一个临时Position对象传递给push_back
	  position.push_back(Position(1, 2));
	  // 错误，push_back不接受两个参数
    position.push_back(1, 2);
    return 0;
}

```

### 3.2.4 顺序容器删除元素

主要是pop、erase俩类方法，分别对应顺序容器添加容器时的push和insert方法。

- **`forward_list`** 有自己专有版本的erase，并且不支持 `pop_back()` 。
- **`vector`** 和 **`string`** 不支持 `pop_front()`。
- **`array`** 无法增删元素。

**（1）pop**

删除容器的首元素和尾元素。

```cpp

// 1. pop_back()
// 除了forward_list和array外，每个顺序容器都支持pop_back()，可以删除尾元素。
c.pop_back(x);

// 2. pop_front()
// list、forward_list和deque支持push_front()，可以删除首元素（由于可以方便地在头部增删数据）。
c.pop_front(x);
```

**（2）erase**

在容器的任意位置删除元素。

```cpp
// 1. 删除迭代器iter指向的元素，返回指向被删元素之后元素的迭代器（删除尾元素时返回off-the-end迭代器，即尾后迭代器
c.erase(iter);  

// 2. 删除迭代器iter_b和iter_e所指向范围内的元素，返回指向最后被删元素之后元素的迭代器（iter_e是尾后迭代器时返回尾后迭代器）
c.erase(iter_b, iter_e);
```

除此之外，`c.clear()`可以删除c中所有元素。

### 3.2.5 改变容器大小

```cpp
c.resize(n); // 调整大小为n个元素
c.resize(n,t); // 调整大小为n个元素，任何带来的新增元素都初始化为值t
```

### 3.2.6 **`forward_list` 的特殊操作**

3.2.3和3.2.4中提到，forward_list有自己特殊版本的增删操作。这是因为链表在增删元素时，需要访问其前驱（用于更新前驱的链接指针），而forward_list作为单向链表没有简单的方法获取前驱，所以需要独特的`insert_after()`、`emplace_after()`、`erase_after()` 操作。

```cpp
// TODO
```

### 3.2.7 string类型提供的额外操作

**（1）构造string的额外方法**

```cpp
std::string s1 = "hello world";
// string s(s2, pos) s是s2从pos开始的字符的拷贝
std::string s2(s1, 6);
// string s(s2, pos, len) s是s2从pos开始的len个字符的拷贝
std::string s3(s1, 0, 5);
std::cout << s1 << std::endl; // hello world
std::cout << s2 << std::endl; // world
std::cout << s3 << std::endl; // hello
```

**（2）substr**

```cpp
std::string s = "hello world";
std::string s1 = s.substr(0, 5); // string::substr(size_t __pos, size_t __n) 
std::cout << s1 << std::endl; // hello
std::string s2 = s.substr(6);
std::cout << s2 << std::endl; // world
```

**（3）insert, erase不仅接受迭代器，还额外接受下标版本**

```cpp

std::string s("hello");
// 用迭代器来调用insert, erase
std::string::iterator it = s.end();
s.insert(it, '!');           // insert(const_iterator __pos, value_type __c) 在迭代器指定位置插入元素
s.insert(it, 2, '!');        // insert(const_iterator __pos, size_type __n, value_type __c) 在迭代器指定位置插入n个元素
std::cout << s << std::endl; // hello!!!
s.erase(it, s.end());        // erase(const_iterator __first, const_iterator __last) 删除迭代器指定范围的元素
std::cout << s << std::endl; // hello

// insert, erase不仅接受迭代器，还额外接受下标版本
// insert在下标指定位置处插入元素，erase删除下标指定位置开始的元素
// 注意下标版本的insert可以插入字符串（因为string是特殊的顺序容器，这样的操作可以更简便），而迭代器版本作为顺序容器的通用用法，只能插入字符
s.insert(s.size(), "!!");    // insert(size_type __pos, const value_type* __s) 在下标指定位置插入字符串
s.insert(s.size(), 2, '!');  // insert(size_type __pos, size_type __n, value_type __c) 在下标指定位置插入n个字符元素
std::cout << s << std::endl; // hello!!!!
s.erase(s.size() - 2, 2);    // erase(size_type __pos = 0, size_type __n = npos) 删除下标指定位置开始的n个元素
std::cout << s << std::endl; // hello!!
s.erase(s.size() - 2);       // erase(size_type __pos = 0, size_type __n = npos)) 删除下标指定位置开始的n个元素，n如果不指定。默认为npos，即-1，这里表示删除从pos开始到末尾的所有元素
std::cout << s << std::endl; // hello
```

**（4）string类两个额外的成员函数：append，replace**

```cpp
std::string s1("hello"), s2 = s1;
std::cout << s1 << std::endl; // hello
std::cout << s2 << std::endl; // hello
// 用下标形式的insert在尾部插入字符串
s1.insert(s1.size(), " world");
std::cout << s1 << std::endl; // hello world
// 用append在尾部插入字符串
s2.append(" world");
std::cout << s2 << std::endl; // hello world
```

**（5）string搜索操作**

```cpp
std::string s = "hello";
int pos = 0;
// 搜索操作返回指定字符串在当前字符串中的位置，如果未找到返回std::string::npos
// 1. find
// 1.1 find(value_type __c, size_type __pos = 0)
pos = s.find('l');             // 查找s中第一个字符l的位置
std::cout << pos << std::endl; // 2
pos = s.find('l', 3);          // 从位置3开始查找s中第一个字符l的位置
std::cout << pos << std::endl; // 3
// 1.2 find(const value_type* __s, size_type __pos = 0)
pos = s.find("ll");            // 查找s中第一个子串ll的位置
std::cout << pos << std::endl; // 2
pos = s.find("ll", 3);         // 从位置3开始查找s中第一个子串ll的位置
std::cout << pos << std::endl; // std::string::npos (-1)

// 2. rfind
pos = s.rfind('l');            // 查找s中最后一个字符l的位置
std::cout << pos << std::endl; // 3
// 其余重载函数与find()类似
```

**（6）compare函数**

```cpp
// compare函数返回值：
// 0：两个字符串相等
// >0：s1 > s2
// <0：s1 < s2

std::string s1 = "1234";
std::string s2 = "23";

std::cout << s1.compare(s2) << std::endl;             // -1 "1234" < "23"
std::cout << s1.compare(1, 2, s2) << std::endl;       // 0 "23" == "23"
std::cout << s1.compare(2, 2, s2, 1, 2) << std::endl; // 1 "34" > "3"
```

**（7）数值转化**

```cpp
// std::to_string() 函数，它可以将整数、浮点数和其他类型转换为字符串。
int i = 42;
double d = 3.14;
std::string s = "";
s = std::to_string(i);
std::cout << s << std::endl; // "42"
s = std::to_string(d);
std::cout << s << std::endl; // "3.140000"

// std::stoi() 函数，它可以将字符串转换为整数。
std::string str = "42";
int i2 = std::stoi(str);
std::cout << i2 << std::endl; // 42

// std::stod() 函数，它可以将字符串转换为浮点数。
std::string str2 = "3.14";
double d2 = std::stod(str2);
std::cout << d2 << std::endl; // 3.14
```

### 3.2.8 顺序容器适配器

容器适配器（Container Adapters）是 C++ 标准库中的一种特殊容器，它们并不是完全独立的数据结构，而是对已有的序列式容器（如 std::vector、std::deque、std::list）进行封装，提供特定的功能接口。

C++ 标准库提供了三种主要的容器适配器：

- **std::stack**（栈）：后进先出（LIFO）结构。
- **std::queue**（队列）：先进先出（FIFO）结构。
- **std::priority_queue**（优先队列）：基于堆实现的优先级队列，默认情况下最大元素优先。

**（1）stack**

std::stack 适配的是 **后进先出（LIFO）** 结构，通常基于 std::deque 实现（默认情况下），也可以使用 std::vector 或 std::list 作为底层容器，可以在创建适配器时，将一个命名的顺序容器作为第二个类型参数，来充值改默认容器类型。

常用操作：push, pop, top。

```cpp
#include <iostream>
#include <stack>

int main() {
    std::stack<int> s;  // 默认使用 std::deque 作为底层容器
    // std::stack<int, std::vector<int>> s; // 使用vector<int>作为第二个参数，从而重载默认的容器类型
    s.push(10);
    s.push(20);
    s.push(30);

    std::cout << "栈顶元素: " << s.top() << std::endl;  // 30
    s.pop();  // 移除栈顶元素
    std::cout << "新的栈顶元素: " << s.top() << std::endl;  // 20

    return 0;
}
```

**（2）queue**

std::queue 适配的是 **先进先出（FIFO）** 结构，默认基于 std::deque 实现，也可以用 std::list 作为底层容器。

常用操作：push, pop, front, back。

```cpp
#include <iostream>
#include <queue>

int main() {
    std::queue<int> q;

    q.push(10);
    q.push(20);
    q.push(30);

    std::cout << "队首元素: " << q.front() << std::endl;  // 10
    std::cout << "队尾元素: " << q.back() << std::endl;  // 30
    q.pop();  // 移除队首元素
    std::cout << "新的队首元素: " << q.front() << std::endl;  // 20

    return 0;
}
```

**（3）priority_queue**

std::priority_queue 是 **优先级队列**，其内部使用 **最大堆**，默认情况下，较大的元素优先（可使用自定义比较函数修改优先级规则）。它的底层默认基于 std::vector 并使用 std::make_heap 进行管理。

常用操作：push, pop, top。

```cpp
#include <iostream>
#include <queue>
#include <vector>

int main() {
    std::priority_queue<int> pq;  // 默认是最大堆

    pq.push(10);
    pq.push(30);
    pq.push(20);

    std::cout << "当前最大元素: " << pq.top() << std::endl;  // 30
    pq.pop();
    std::cout << "新的最大元素: " << pq.top() << std::endl;  // 20

    return 0;
}
```

如果想让 priority_queue 变成 **最小堆**，可以使用 std::greater<int>：

```cpp
std::priority_queue<int, std::vector<int>, std::greater<int>> min_heap;
```

### 3.2.9 关于vector容器如何增长

由于vector中连续存储的，且vector的容器大小是可变的，所以vector被分配到的内存空间也是会动态变化的。当vector容器不得不获取新的内存空间时，vector的实现往往会分配比新的空间需求更大的内存空间，多余空间留作备用，防止每次添加元素都要重新分配空间（可能需要移动所有元素，麻烦）。

vector类型提供一些成员函数来帮助程序员来主动作内存分配管理，.capacity()告诉容器当前被分配到的内存空间内可以容纳多少元素，而.reserve()允许程序员通知容器应该准备多大的内存空间来保存对应大小的元素数量。（注意，reserve只是通知，容器可能会被分配更大的内存空间，或者相等，但是不会减少容器占用的内存数量）

这里与.size()区分，size只是返回该容器已经保存了多少元素，而capacity是当前分配的内存空间能存储多少元素。

## 3.3 关联容器

关联容器是 C++ 标准库中非常重要的一部分，用于存储键值对（key-value pairs）或单独的键，它们的主要特点是内部采用平衡二叉搜索树（如红黑树）或哈希表实现，从而支持高效的查找、插入和删除操作，

C++标准库提供了两大类关联容器：

1. **有序关联容器**：
    - `std::map`：存储唯一的键值对，按键排序。
    - `std::set`：存储唯一的键，按键排序。
    - `std::multimap`：存储允许重复的键值对，按键排序。
    - `std::multiset`：存储允许重复的键，按键排序。
2. **无序关联容器**：
    - `std::unordered_map`：存储唯一的键值对，不排序。
    - `std::unordered_set`：存储唯一的键，不排序。
    - `std::unordered_multimap`：存储允许重复的键值对，不排序。
    - `std::unordered_multiset`：存储允许重复的键，不排序。

### 3.3.0 关于pair

由于关联容器中的键值对是由pair存储的，所以这里简单地介绍下pair。

std::pair 是 C++ 标准库中的一个模板类，它用于存储两个不同类型的值作为一个单元。pair 主要用于需要临时存储两个相关值的情况，比如返回函数的两个结果，或者在容器（如 map）中存储键值对。

**（1）常见定义方式**

```cpp
#include <iostream>
#include <utility>  // pair 需要包含的头文件 (但可能很多其他头文件都会间接包含)

int main() {
    std::pair<int, double> p1;  // 默认构造，两个值都初始化为默认值（0 和 0.0）
    std::pair<int, std::string> p2(1, "Hello");  // 直接初始化
    std::pair<int, char> p3 = std::make_pair(2, 'A'); // 使用 make_pair 进行初始化

    std::cout << "p2: (" << p2.first << ", " << p2.second << ")\n"; // p2: (1, Hello)
    std::cout << "p3: (" << p3.first << ", " << p3.second << ")\n"; // p3: (2, A)

    return 0;
}
```

- first 成员变量表示 pair 的第一个值
- second 成员变量表示 pair 的第二个值
- std::make_pair(x, y) 是一个方便的用来创建pair的函数，它可以自动推导类型

**（2）pair比较**

除此之外，pair 支持 **比较运算符** (==, !=, <, >, <=, >=)，比较规则是 **按字典序**：

1.	先比较 first，first 小的 pair 就小。

2.	如果 first 相同，则比较 second。

**（3）pair在STL中的应用**

std::pair 常用于 std::map，因为 map 内部存储的是 **键值对（key-value pair）：**

```cpp
#include <iostream>
#include <map>

int main() {
    std::map<int, std::string> myMap;
    myMap.insert(std::make_pair(1, "Apple"));
    myMap.insert({2, "Banana"});  // C++11 允许直接用 { }

    for (const auto &p : myMap) {
        std::cout << p.first << " -> " << p.second << "\n";
    }

    return 0;
}
```

也可以在 std::vector 中存储一组 pair：

```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<std::pair<int, std::string>> v = {{1, "Alice"}, {2, "Bob"}, {3, "Charlie"}};

    for (const auto &p : v) {
        std::cout << "ID: " << p.first << ", Name: " << p.second << "\n";
    }

    return 0;
}
```

### 3.3.1 有序关联容器之map

`std::map` 是一个有序关联容器，存储唯一的键值对，按键排序（默认按升序）。

基本操作如下：

```cpp
#include <iostream>
#include <map>

int main()
{
    // 创建一个map
    std::map<std::string, int> ageMap;

    // 插入元素
    ageMap["Alice"] = 30;
    ageMap.insert({"Bob", 25});

    // 查找元素
    auto it = ageMap.find("Alice"); // 返回一个迭代器，指向key为"Alice"的元素，如果没找到则返回end()
    if (it != ageMap.end())
    {
        // it是一个指向std::pair<const std::string, int>的迭代器，it->first是key，it->second是value
        std::cout << "Alice's age: " << it->second << std::endl;
    }

    // 删除元素
    ageMap.erase("Bob"); // 删除key为"Bob"的元素，如果没找到则不做任何操作

    // 遍历元素
    for (const auto &pair : ageMap)
    {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```

### 3.3.2 有序关联容器之set

`std::set` 是一个有序关联容器，存储唯一的键，按键排序。

基本操作如下：

```cpp
#include <iostream>
#include <set>

int main() {
    std::set<int> numbers;

    // 插入元素
    numbers.insert(10);
    numbers.insert(20);

    // 查找元素
    auto it = numbers.find(10);
    if (it != numbers.end()) {
        std::cout << "Found: " << *it << std::endl;
    }

    // 删除元素
    numbers.erase(20);

    // 遍历元素
    for (const auto& num : numbers) {
        std::cout << num << std::endl;
    }

    return 0;
}
```

### 3.3.3 有序关联容器之multimap

`std::multimap` 是一个有序关联容器，存储允许重复的键值对，按键排序。

```cpp
#include <iostream>
#include <map>

int main() {
    std::multimap<std::string, int> scoreMap;

    // 插入元素
    scoreMap.insert({"Alice", 85});
    scoreMap.insert({"Alice", 92});

    // 查找元素
    auto range = scoreMap.equal_range("Alice");
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << it->first << ": " << it->second << std::endl;
    }

    // 删除元素
    scoreMap.erase("Alice");

    return 0;
}
```

### 3.3.4 有序关联容器之multiset

`std::multiset` 是一个有序关联容器，存储允许重复的键，按键排序。

```cpp
#include <iostream>
#include <set>

int main() {
    std::multiset<int> numbers;

    // 插入元素
    numbers.insert(10);
    numbers.insert(10);

    // 查找元素
    auto range = numbers.equal_range(10);
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << *it << std::endl;
    }

    // 删除元素
    numbers.erase(10);

    return 0;
}
```

### 3.3.5 **无序关联容器之unordered_map**

`std::unordered_map` 是一个无序关联容器，存储唯一的键值对，基于哈希表实现。

基本操作如下：

```cpp
#include <iostream>
#include <unordered_map>

int main() {
    std::unordered_map<std::string, int> ageMap;

    // 插入元素
    ageMap["Alice"] = 30;
    ageMap.insert({"Bob", 25});

    // 查找元素
    auto it = ageMap.find("Alice");
    if (it != ageMap.end()) {
        std::cout << "Alice's age: " << it->second << std::endl;
    }

    // 删除元素
    ageMap.erase("Bob");

    // 遍历元素
    // 由于unordered_map是无序的，所以输出的顺序可能不一样
    for (const auto &pair : ageMap)
    {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }

    return 0;
}
```

### 3.3.6 **无序关联容器之unordered_set**

`std::unordered_set` 是一个无序关联容器，存储唯一的键，基于哈希表实现。

基本操作如下：

```cpp
#include <iostream>
#include <unordered_set>

int main() {
    std::unordered_set<int> numbers;

    // 插入元素
    numbers.insert(10);
    numbers.insert(20);

    // 查找元素
    auto it = numbers.find(10);
    if (it != numbers.end()) {
        std::cout << "Found: " << *it << std::endl;
    }

    // 删除元素
    numbers.erase(20);

    // 遍历元素
    for (const auto& num : numbers) {
        std::cout << num << std::endl;
    }

    return 0;
}
```

### 3.3.7 关联容器的选择和总结

**（1）选择关联容器的依据**

- 是否需要排序：
    - 需要排序：选择有序关联容器（`std::map`、`std::set` 等）。
    - 不需要排序：选择无序关联容器（`std::unordered_map`、`std::unordered_set` 等）。
- 键是否唯一：
    - 键唯一：选择 `std::map` 或 `std::set`。
    - 键可以重复：选择 `std::multimap` 或 `std::multiset`。

**（2）性能对比**

| **容器**             | **查找时间复杂度** | **插入时间复杂度** | **删除时间复杂度** | **是否排序** |
| -------------------- | ------------------ | ------------------ | ------------------ | ------------ |
| `std::map`           | O(log n)           | O(log n)           | O(log n)           | 是           |
| `std::set`           | O(log n)           | O(log n)           | O(log n)           | 是           |
| `std::multimap`      | O(log n)           | O(log n)           | O(log n)           | 是           |
| `std::multiset`      | O(log n)           | O(log n)           | O(log n)           | 是           |
| `std::unordered_map` | O(1) ~ O(n)        | O(1) ~ O(n)        | O(1) ~ O(n)        | 否           |
| `std::unordered_set` | O(1) ~ O(n)        | O(1) ~ O(n)        | O(1) ~ O(n)        | 否           |

# 4 泛型算法

C++ 的泛型编程是一种强大的编程范式，允许编写与数据类型无关的代码。泛型算法是泛型编程的核心部分，它们通过模板实现，能够处理各种数据类型。本章将系统地介绍 C++ 中的泛型算法，帮助你从基础到高级逐步掌握这一重要概念。

大部分算法都定义在头文件algorithm中。

## 4.0 附加技术点——泛型算法的定制操作

标准库为部分算法函数定义了一些默认的函数或者lambda表达式，我们可以提供自定义的函数或者lambda表达式来定制这些算法的调用。

### 4.0.1 向算法传递函数

标准库算法（如 `std::sort`、`std::transform` 等）通常是通用的，它们不知道具体的业务逻辑。通过传递函数，我们可以自定义算法的行为

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // sort

// 自定义比较函数
bool compare(int a, int b) {
    return a > b; // 降序排序
}

int main() {
    std::vector<int> v = {5, 3, 1, 4, 2};

    // 传递函数指针
    std::sort(v.begin(), v.end(), compare);

    // 打印排序后的容器
    for (int i : v) {
        std::cout << i << " ";
    }

    return 0;
}
```

### 4.0.2 lambda表达式

Lambda 表达式是 C++11 引入的强大特性，它允许我们在代码中定义匿名函数，从而简化代码并提高灵活性。通过捕获列表、参数列表和返回类型的组合，lambda 表达式可以满足各种复杂的需求。所以在使用泛型算法时，可以传递lambda表达式来达到定制操作，效果和传递函数类似。

Lambda 表达式的基本语法如下：

```cpp
[capture-list] (parameters) -> return-type {
    // 函数体
}
```

- **`capture-list`**：捕获列表，用于指定 lambda 表达式如何访问外部变量。
    - 值捕获：使用 `[=]` 捕获所有外部变量的值；使用 `[var]` 捕获特定变量的值。
        
        ```cpp
        int x = 10;
        auto lambda = [x]() {
            std::cout << "x = " << x << std::endl;
        };
        lambda(); // 输出：x = 10
        ```
        
    - **引用捕获：**使用 `[&]` 捕获所有外部变量的引用；使用 `[&var]` 捕获特定变量的引用。
        
        ```cpp
        int x = 10;
        auto lambda = [&x]() {
            x = 20;
        };
        lambda();
        std::cout << "x = " << x << std::endl; // 输出：x = 20
        ```
        
    - **混合捕获**
        
        ```cpp
        int x = 10, y = 20;
        auto lambda = [x, &y]() {
            y = 30;
            std::cout << "x = " << x << ", y = " << y << std::endl;
        };
        lambda(); // 输出：x = 10, y = 30
        ```
        
- **`parameters`**：参数列表，与普通函数的参数列表类似。
- **`return-type`**：返回类型，可以省略，编译器会自动推导。
- **`函数体`**：lambda 表达式的实现逻辑。

## 4.1 只读算法

这些算法不会修改容器中的元素，主要用于遍历、查找和统计。

### **4.1.1 std::for_each**

**功能**：对容器中的每个元素执行指定的操作。

**参数**：

- `first`：容器的起始迭代器。
- `last`：容器的结束迭代器。
- `f`：一个可调用对象（函数、lambda 表达式等），用于处理每个元素。

```cpp
_Function for_each(_InputIterator __first,
                   _InputIterator __last,
                   _Function __f)
{
    for (; __first != __last; ++__first)
        __f(*__first);
    return __f;
}
```

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // for_each

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};

    // 使用 lambda 表达式打印每个元素
    std::for_each(v.begin(), v.end(), [](int i) {
        std::cout << i << " ";
    }); // 1 2 3 4 5

    return 0;
}
```

### **4.1.2 std::find**

**功能**：在容器中查找特定值，返回指向该元素（第一个匹配的元素）的迭代器。如果未找到，返回 `end()`。

**参数**：

- `first`：容器的起始迭代器。
- `last`：容器的结束迭代器。
- `value`：要查找的值。

**返回值**：指向找到元素的迭代器，如果未找到则返回 `end()`。

```cpp
find(_InputIterator __first, _InputIterator __last, const _Tp& __value_) {
  for (; __first != __last; ++__first)
    if (*__first == __value_)
      break;
  return __first;
}
```

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // find

int main() {
    std::vector<int> v = {10, 20, 30, 40, 50};

    // 查找值为 30 的元素
    auto it = std::find(v.begin(), v.end(), 30);

    if (it != v.end()) {
        std::cout << "Found: " << *it << std::endl; // Found: 30
    } else {
        std::cout << "Not found" << std::endl;
    }

    return 0;
}
```

### **4.1.3 std::count**

**功能**：统计容器中等于某个值的元素个数。

**参数**：

- `first`：容器的起始迭代器。
- `last`：容器的结束迭代器。
- `value`：要统计的值。

**返回值**：等于 `value` 的元素个数。

```cpp
count(_InputIterator __first, _InputIterator __last, const _Tp &__value_)
{
    typename iterator_traits<_InputIterator>::difference_type __r(0);
    for (; __first != __last; ++__first)
        if (*__first == __value_)
            ++__r;
    return __r;
}
```

 Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // count

int main() {
    std::vector<int> v = {1, 2, 3, 2, 4, 2, 5};

    // 统计值为 2 的元素个数
    int cnt = std::count(v.begin(), v.end(), 2);

    std::cout << "Number of 2s: " << cnt << std::endl; // Number of 2s: 3

    return 0;
}
```

### **4.1.4 二分查找**

二分查找要求序列必须是**已排序的**（默认升序）。如果序列未排序，二分查找的结果是未定义的。

**（1）std::binary_search**

**功能**：检查序列中是否存在某个值。

**参数**：

- `first`：序列的起始迭代器。
- `last`：序列的结束迭代器。
- `value`：要查找的值。
- `comp`（可选）：自定义比较函数。

**返回值**：

- `true`：如果找到目标值。
- `false`：如果未找到目标值。

**Example:**

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // binary_search, sort

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};

    // 必须先排序
    std::sort(v.begin(), v.end());

    // 查找值为 3 的元素
    bool found = std::binary_search(v.begin(), v.end(), 3);

    if (found) {
        std::cout << "Found!" << std::endl;
    } else {
        std::cout << "Not found!" << std::endl;
    }
    // Found!

    return 0;
}
```

**（2）std::lower_bound**

**功能**：返回序列中**第一个不小于**目标值的元素的迭代器。

**参数**：

- `first`：序列的起始迭代器。
- `last`：序列的结束迭代器。
- `value`：要查找的值。
- `comp`（可选）：自定义比较函数。

**返回值**：

- 指向第一个不小于目标值的元素的迭代器。
- 如果所有元素都小于目标值，返回 `last`。

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // lower_bound, sort

int main() {
    std::vector<int> v = {1, 2, 4, 4, 5};

    // 必须先排序
    std::sort(v.begin(), v.end());

    // 查找第一个不小于 3 的元素
    auto it = std::lower_bound(v.begin(), v.end(), 3);

    if (it != v.end()) {
        std::cout << "First element not less than 3 is: " << *it << std::endl;
    } else {
        std::cout << "All elements are less than 3" << std::endl;
    }
		// First element not less than 3 is: 4
    return 0;
}
```

**（3）std::upper_bound**

**功能**：返回序列中**第一个大于**目标值的元素的迭代器。

**参数**：

- `first`：序列的起始迭代器。
- `last`：序列的结束迭代器。
- `value`：要查找的值。
- `comp`（可选）：自定义比较函数。

**返回值**：

- 指向第一个大于目标值的元素的迭代器。
- 如果所有元素都不大于目标值，返回 `last`。

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // upper_bound, sort

int main() {
    std::vector<int> v = {1, 2, 4, 4, 5};

    // 必须先排序
    std::sort(v.begin(), v.end());

    // 查找第一个大于 4 的元素
    auto it = std::upper_bound(v.begin(), v.end(), 4);

    if (it != v.end()) {
        std::cout << "First element greater than 4 is: " << *it << std::endl;
    } else {
        std::cout << "All elements are not greater than 4" << std::endl;
    }
		// First element greater than 4 is: 5
    return 0;
}
```

**（4）std::equal_range**

**功能**：返回一个范围，表示所有等于目标值的元素。

**参数**：

- `first`：序列的起始迭代器。
- `last`：序列的结束迭代器。
- `value`：要查找的值。
- `comp`（可选）：自定义比较函数。

**返回值**：

- 一个 `std::pair`，包含两个迭代器：
    - `first`：指向第一个等于目标值的元素。
    - `second`：指向第一个大于目标值的元素。

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // equal_range, sort

int main() {
    std::vector<int> v = {1, 2, 4, 4, 5};

    // 必须先排序
    std::sort(v.begin(), v.end());

    // 查找所有等于 4 的元素
    auto range = std::equal_range(v.begin(), v.end(), 4);

    std::cout << "Elements equal to 4 are: ";
    for (auto it = range.first; it != range.second; ++it) {
        std::cout << *it << " ";
    }
		// Elements equal to 4 are: 4 4
    return 0;
}
```

## 4.2 写算法

### 4.2.1  **std::copy**

**功能**：将一个容器的元素复制到另一个容器。

**参数**：

- `_InputIterator __first`：源容器的起始迭代器。
- `_InputIterator __last`：源容器的结束迭代器。
- `_OutputIterator __result`：目标容器的起始迭代器。

**返回值**：指向目标容器最后一个复制元素的下一个位置的迭代器。

```cpp
copy(_InputIterator __first, _InputIterator __last, _OutputIterator __result)
{
    if (__libcpp_is_constant_evaluated()) {
        return _VSTD::__copy_constexpr(__first, __last, __result);
    } else {
        return _VSTD::__rewrap_iter(__result,
            _VSTD::__copy(_VSTD::__unwrap_iter(__first),
                          _VSTD::__unwrap_iter(__last),
                          _VSTD::__unwrap_iter(__result)));
    }
}
```

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // copy

int main()
{
    std::vector<int> src = {1, 2, 3, 4, 5};
    std::vector<int> dst(src.size()); // 目标容器需要预先分配空间

    // 将 src 的元素复制到 dst
    std::copy(src.begin(), src.end(), dst.begin());

    // 打印 dst 的内容
    for (int i : dst)
    {
        std::cout << i << " ";
    }
		// 1 2 3 4 5
    return 0;
}
```

### 4.2.2 std::transform

**功能**：对容器中的每个元素进行转换，并将结果存储到另一个容器中。

**参数**：

- `_InputIterator __first`：源容器的起始迭代器。
- `_InputIterator __last`：源容器的结束迭代器。
- `_OutputIterator __result`：目标容器的起始迭代器。
- `_UnaryOperation __op`：一个可调用对象，用于对每个元素进行转换。

**返回值**：指向目标容器最后一个转换元素的下一个位置的迭代器。

```cpp
transform(_InputIterator __first, _InputIterator __last, _OutputIterator __result, _UnaryOperation __op)
{
    for (; __first != __last; ++__first, (void) ++__result)
        *__result = __op(*__first);
    return __result;
}
```

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // transform

int main()
{
    std::vector<int> src = {1, 2, 3, 4, 5};
    std::vector<int> dst(src.size());

    // 将 src 中的每个元素乘以 2，存储到 dst
    std::transform(src.begin(), src.end(), dst.begin(), [](int i)
                   { return i * 2; });

    // 打印 dst 的内容
    for (int i : dst)
    {
        std::cout << i << " ";
    }
    // 2 4 6 8 10

    return 0;
}
```

### 4.2.3 std::replace

**功能**：将容器中等于某个值的元素替换为另一个值。

**参数**：

- `_ForwardIterator __first`：容器的起始迭代器。
- `_ForwardIterator __last`：容器的结束迭代器。
- `const _Tp& __old_value`：要被替换的值。
- `const _Tp& __new_value`：替换后的值。

**返回值**：无。

```cpp
replace(_ForwardIterator __first, _ForwardIterator __last, const _Tp& __old_value, const _Tp& __new_value)
{
    for (; __first != __last; ++__first)
        if (*__first == __old_value)
            *__first = __new_value;
}
```

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // replace

int main()
{
    std::vector<int> v = {1, 2, 3, 2, 4, 2, 5};

    // 将所有值为 2 的元素替换为 99
    std::replace(v.begin(), v.end(), 2, 99);

    // 打印修改后的容器
    for (int i : v)
    {
        std::cout << i << " ";
    }
		// 1 99 3 99 4 99 5
    return 0;
}
```

## 4.3 排序算法

### 4.3.1 std::sort

**功能**：对容器中的元素进行排序。

**参数**：

- `_RandomAccessIterator __first`：容器的起始迭代器。
- `_RandomAccessIterator __last`：容器的结束迭代器。
- `_Compare __comp`（可选）：自定义比较函数，默认为升序。

**返回值**：无。

Example：

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // sort

int main()
{
    std::vector<int> v = {5, 3, 1, 4, 2};

    // 默认升序排序
    std::sort(v.begin(), v.end(), [](int a, int b)
              { return a > b; });

    // 打印排序后的容器
    for (int i : v)
    {
        std::cout << i << " ";
    }
    // 5 4 3 2 1

    return 0;
}
```