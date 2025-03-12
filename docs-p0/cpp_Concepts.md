## 前言
Hello，world！今天是2025年3月12日，这是我站的第一篇博客，也是我重新踏上自学计算机科学（CS）之路的起点！这次，我要为实习和求职铺一条坚实的“代码之路”。

回想已流逝的两年半多本科时光，很多课程因为时间紧、任务重，学得像个“快餐式”程序员——知识点匆匆下肚，消化得却不咋样；ppt上的知识点背了一打，实践起来“一问三不知”。猛然发现，自己像个“半成品”，代码写得像“面条”，bug多得像“打地鼠”。

恰逢找到第一个日常实习，初尝真实的工作环境。实习之余决定重启学习，重新修炼“代码内功”。

从哪里开始呢？第一站是`C++`的基本语法。之前刷算法题时用过C++，每次没有类、没有封装、没有 unit test、没有 Makefile、没有 Git，唯一的优点是它确实能跑，缺点是“能跑”的补集；大三上做过一个4000行代码的Qt小项目，实现简单图像识别与参量的自动化计算（可见：https://github.com/WenLiuyi/fiber_Analysis ）现已经成功打包成exe文件，可在Windows系统执行。但那过程简直像“渡劫”——装了几十个 G 的 Visual Studio，每次打开那笨重的 IDE，配置环境像“解谜游戏”，编译错误像“天书”，代码结构像“迷宫”。每次debug都像在玩“找茬”，头大得能顶个西瓜！但这些“坑”让我明白，自己的知识储备还像个“小池塘”，远远不够用。

所以，我决定从现在开始，重启学习！接下来的日子，我要复习本科的专业课，重启那些被遗忘的自学课程（超级感谢[CS自学指南](https://csdiy.wiki/)）。
这条路不会像“Hello, world!”那么简单，但最好的时机，就是现在！从“零”开始，走向“无穷大”！

# 15445-bootcamp中的C++预备知识
* [References](https://en.cppreference.com/w/cpp/language/reference)
* [std::move](https://en.cppreference.com/w/cpp/utility/move)
* [Move Constructors](https://en.cppreference.com/w/cpp/language/move_constructor)和[Move Assignment Operators](https://en.cppreference.com/w/cpp/language/move_assignment)

## 1. 引用和移动
### 1.1 引用
#### 概述
引用是 C++ 中的一种机制，用来创建变量的别名。通过引用，**多个名字可以指向同一块内存区域**。引用通常用于函数参数的传递、改动数据的追踪以及提升性能等场景。
* 初始化：引用必须在声明时初始化，并且一旦绑定到一个变量后不能改变指向另一个变量。
* 参考传递（Pass by Reference）：引用是一种传递方式，允许函数修改调用者的变量值。
* 性能优化：通过引用避免了数据的复制，提高了效率，尤其是在传递大数据结构时。

#### 引用的声明
引用的声明使用的是单个 & 符号。例如：
```cpp
int a = 10;
int &b = a;  // b 是 a 的引用
```
这意味着 b 只是 a 的另一个名字，a 和 b 都指向同一个内存位置。如果我们修改 b，实际上就是修改 a 的值。

#### 示例代码
```cpp
#include <iostream>

// 一个接受 int 引用并将其值增加 3 的函数
void add_three(int &a) { 
    a = a + 3; 
}

int main() {
    int a = 10;   // 定义一个整型变量 a
    int &b = a;   // b 是 a 的引用，它指向 a

    // 输出 b 的值，此时 b 的值为 10
    std::cout << "b is " << b << std::endl;  

    // 调用 add_three 函数，传入 a 的引用，a 的值将被修改
    add_three(a);

    // 输出 a 的新值，add_three 函数已将 a 增加了 3
    std::cout << "a is " << a << std::endl;

    return 0;


}
```

### 1.2 C++移动语义
#### 概述
移动语义是 C++ 中的一种重要特性，旨在提高程序的性能，特别是在处理大型数据结构时。移动语义通过避免不必要的深拷贝，使得对象之间的数据转移更加高效。与传统的拷贝操作不同，移动操作**通过“转移所有权”的方式，使得对象的资源能够直接从一个对象转移到另一个对象，而不是复制数据**。
移动语义的核心概念在于区分 左值（lvalue） 和 右值（rvalue）：
* 左值（lvalue）： 是指向**内存中某个位置的对象**，可以持久存在。
* 右值（rvalue） 是**临时对象**，通常用于表示临时的、不再需要的对象。
* 性能提升：使用 `std::move` 可以避免不必要的深拷贝。特别是当我们操作如 `std::vector`、`std::string` 等需要大量内存的容器时，移动比拷贝要高效得多。

#### 主要概念
1. `std::move`： `std::move` 不是“移动”操作本身，而是将一个对象标记为可以被移动的状态，实际上它只是一个类型转换操作，**将左值转换为右值**。之后，移动构造函数或移动赋值操作符会将对象的所有权，从一个对象转移到另一个对象。
2. 右值引用（Rvalue Reference）： 右值引用是使用 `&&` 语法声明的引用，它绑定到一个右值上。通过右值引用，我们可以通过移动语义避免不必要的拷贝。

#### 示例代码
```cpp
#include <iostream>
#include <utility>
#include <vector>

// 一个接收右值引用作为参数的函数
void move_add_three_and_print(std::vector<int> &&vec) {
  std::vector<int> vec1 = std::move(vec);  // 使用 std::move 转移 vec 的所有权
  vec1.push_back(3);  // 向 vec1 添加一个元素
  for (const int &item : vec1) {
    std::cout << item << " ";  // 打印 vec1 中的元素
  }
  std::cout << "\n";
}

// 一个接收右值引用的函数，但不获取所有权，仅修改数据
void add_three_and_print(std::vector<int> &&vec) {
  vec.push_back(3);
  for (const int &item : vec) {
    std::cout << item << " ";
  }
  std::cout << "\n";
}

int main() {
  int a = 10;  // 'a' 是左值

  std::vector<int> int_array = {1, 2, 3, 4};  // 定义一个整数向量

  // 将 int_array 的数据转移到另一个向量
  std::vector<int> stealing_ints = std::move(int_array);  // 通过 std::move 将所有权转移

  std::vector<int> &&rvalue_stealing_ints = std::move(stealing_ints);  // 使用右值引用

  // 虽然所有权已转移，但仍然可以访问原来数据（不过此时的行为不可预测）
  std::cout << "Printing from stealing_ints: " << stealing_ints[1] << std::endl;

  // 将右值引用传入函数，转移所有权
  std::vector<int> int_array2 = {1, 2, 3, 4};
  std::cout << "Calling move_add_three_and_print...\n";
  move_add_three_and_print(std::move(int_array2));  // 使用 std::move 转移 int_array2 的所有权

  // 注意：一旦调用了 move_add_three_and_print，int_array2 中的数据就不再属于它，不能再访问
  // std::cout << int_array2[1] << std::endl;  // 访问已转移数据的行为未定义（可能崩溃）

  // 另一种情况，使用右值引用，但不获取所有权
  std::vector<int> int_array3 = {1, 2, 3, 4};
  std::cout << "Calling add_three_and_print...\n";
  add_three_and_print(std::move(int_array3));  // 这里只是修改数据，不转移所有权

  // 由于没有转移所有权，int_array3 仍然有效
  std::cout << "Printing from int_array3: " << int_array3[1] << std::endl;

  return 0;
}
```

### 1.3 移动构造函数和移动赋值运算符
#### 移动构造函数
移动构造函数用于在**创建新对象**时，将一个已有对象的资源从源对象“移动”到目标对象。通过 std::move，源对象的资源将被转移给新对象，而源对象的状态会被置为无效。

#### 移动赋值运算符
移动赋值运算符用于将一个已有对象的资源从一个对象转移到另一个**已存在的对象**中。在此过程中，目标对象会接管源对象的资源，而源对象则失去对这些资源的所有权。

#### 示例代码
1. `Person`类包含3个构造函数：一个默认构造函数；一个带有右值引用参数的构造函数用于通过移动资源来初始化对象；一个移动构造函数
2. 移动构造函数`Person(Person &&person)`：接受一个右值引用 `Person &&person`，通过 `std::move` 转移 `nicknames_` 和 `age_` 的所有权来创建新对象；
3. 移动赋值运算符`Person &operator=(Person &&other)`：将 `other` 对象的资源转移到当前对象


```cpp
#include <iostream>
#include <utility>
#include <string>
#include <cstdint>
#include <vector>

class Person {
public:
  Person() : age_(0), nicknames_({}), valid_(true) {}

  // 移动构造函数：接受右值引用，转移资源
  Person(uint32_t age, std::vector<std::string> &&nicknames)
      : age_(age), nicknames_(std::move(nicknames)), valid_(true) {}

  // 复制构造函数已删除，不允许复制
  Person(const Person &) = delete;
  Person &operator=(const Person &) = delete;

  // 移动构造函数：从另一个 Person 对象移动资源
  Person(Person &&person)
      : age_(person.age_), nicknames_(std::move(person.nicknames_)),
        valid_(true) {
    std::cout << "调用 Person 类的移动构造函数。\n";
    person.valid_ = false; // 移动后，源对象的状态变为无效
  }

  // 移动赋值运算符：转移资源
  Person &operator=(Person &&other) {
    std::cout << "调用 Person 类的移动赋值运算符。\n";
    age_ = other.age_;
    nicknames_ = std::move(other.nicknames_);
    valid_ = true;
    other.valid_ = false; // 源对象的状态变为无效
    return *this;
  }

  uint32_t GetAge() { return age_; }

  // 返回 nicknames_ 中的某个字符串的引用
  std::string &GetNicknameAtI(size_t i) { return nicknames_[i]; }

  void PrintValid() {
    if (valid_) {
      std::cout << "对象有效。\n";
    } else {
      std::cout << "对象无效。\n";
    }
  }

private:
  uint32_t age_;
  std::vector<std::string> nicknames_;
  bool valid_; // 追踪对象的有效性
};

int main() {
  // 创建一个 Person 对象 andy，并初始化其昵称和年龄
  Person andy(15445, {"andy", "pavlo"});
  std::cout << "打印 andy 的有效性: ";
  andy.PrintValid();

  // 使用 std::move 调用移动赋值运算符，将 andy 的内容转移到 andy1
  Person andy1;
  andy1 = std::move(andy);  // 调用移动赋值运算符

  std::cout << "打印 andy1 的有效性: ";
  andy1.PrintValid();
  std::cout << "打印 andy 的有效性: ";
  andy.PrintValid();

  // 使用 std::move 调用移动构造函数，将 andy1 的内容转移到 andy2
  Person andy2(std::move(andy1));  // 调用移动构造函数

  std::cout << "打印 andy2 的有效性: ";
  andy2.PrintValid();
  std::cout << "打印 andy1 的有效性: ";
  andy1.PrintValid();

  // 以下代码会因为复制构造函数被删除而无法编译
  // Person andy3;
  // andy3 = andy2;  // 编译错误，不能使用复制赋值运算符
  // Person andy4(andy2);  // 编译错误，不能使用复制构造函数

  return 0;
}
```

## 2.模板
模板是C++中的一种语言特性，它允许你编写可以与多种数据类型一起工作的代码，而无需指定具体的类型。C++中既可以创建模板函数，也可以创建模板类。
### 2.1 模板类
* 模板类使得类能够与不同的数据类型一起工作，而不需要为每个数据类型编写不同的类实现。
#### 基本模板类
下面是一个基本的模板类 Foo，它可以存储一个模板类型的元素，并在调用 print 函数时打印该元素的值：
* T 是模板参数，表示类 Foo 可以存储任何类型的值。var_ 是存储该值的成员变量，print 函数用来打印该值。
```cpp
template<typename T>
class Foo {
public:
    Foo(T var) : var_(var) {}
    void print() {
        std::cout << var_ << std::endl;
    }
private:
    T var_;
};
```
#### 使用多个模板参数
模板类不仅支持单一模板参数，还可以支持多个模板参数。以下是一个接受两个不同类型参数的模板类 Foo2：
* Foo2 类存储了两个不同类型的元素 var1_ 和 var2_，并在 print 函数中打印它们。
```cpp
template<typename T, typename U> 
class Foo2 {
public:
    Foo2(T var1, U var2) : var1_(var1), var2_(var2) {}
    void print() {
        std::cout << var1_ << " and " << var2_ << std::endl;
    }
private:
    T var1_;
    U var2_;
};
```
#### 特化模板类
C++允许为特定类型提供模板类的专门实现，这称为模板类特化。以下是一个模板类 `FooSpecial`，它的 `print` 函数根据模板参数类型的不同执行不同的操作：
* 在这个例子中，`FooSpecial` 是一个模板类，当它的类型是 `float` 时，`print` 函数会输出不同的信息（"hello float!"），而其他类型则按照常规输出存储的值。

```cpp
template<typename T>
class FooSpecial {
public:
    FooSpecial(T var) : var_(var) {}
    void print() {
        std::cout << var_ << std::endl;
    }
private:
    T var_;
};
// 针对float类型的模板特化
template<>
class FooSpecial<float> {
public:
    FooSpecial(float var) : var_(var) {}
    void print() {
        std::cout << "hello float! " << var_ << std::endl;
    }
private:
    float var_;
};
```

#### 使用非类型模板参数
除了类型作为模板参数外，还可以使用常量（如整数）作为模板参数。以下是一个例子，其中 `Bar` 类接受一个整数作为模板参数：
* `Bar` 类接受一个整数模板参数 T，并在 `print_int` 函数中输出该常量值。
```cpp
template<int T>
class Bar {
public:
    Bar() {}
    void print_int() {
        std::cout << "print int: " << T << std::endl;
    }
};
```
#### 示例
```cpp
// 使用模板类型 int 实例化 Foo 类
Foo<int> a(3);
std::cout << "Calling print on Foo<int> a(3): ";
a.print();

// 使用模板类型 float 实例化 Foo 类
Foo b(3.4f);
std::cout << "Calling print on Foo b(3.4f): ";
b.print();

// 使用两个不同模板类型实例化 Foo2 类
Foo2<int, float> c(3, 3.2f);
std::cout << "Calling print on Foo2<int, float> c(3, 3.2f): ";
c.print();

// 实例化 FooSpecial 类，分别使用 int 和 float 类型
FooSpecial<int> d(5);
std::cout << "Calling print on FooSpecial<int> d(5): ";
d.print();

FooSpecial<float> e(4.5);
std::cout << "Calling print on FooSpecial<float> e(4.5): ";
e.print();

// 使用非类型模板参数实例化 Bar 类
Bar<150> f;
std::cout << "Calling print_int on Bar<150> f: ";
f.print_int();
```

### 2.2 模板函数
#### 基本模板函数
C++模板函数的语法允许函数接受**任何类型的数据**作为参数，而不需要显式地定义数据类型。可以使用 `template` 关键字来定义模板函数：
```cpp
template <typename T> 
T add(T a, T b) { 
    return a + b; 
}
```
这个函数接受两种相同类型的参数，返回它们的和。`typename T` 是模板参数，表示函数可以接受任意类型的数据。

#### 多个模板参数
模板函数可以接受多个模板参数。例如，下面的 `print_two_values` 函数接受两种不同类型的参数：
```cpp
template<typename T, typename U>
void print_two_values(T a, U b) {
    std::cout << a << " and " << b << std::endl;
}
```
该函数输出两个不同类型的参数。

#### 特化模板函数
C++允许**为特定类型提供模板函数的专门实现**，这称为模板特化。在下面的例子中，`print_msg` 函数通常打印“Hello world!”，但当类型为 `float` 时，打印不同的信息：
```cpp
template <typename T> 
void print_msg() { 
    std::cout << "Hello world!\n"; 
}

// 针对float类型的模板特化
template <> 
void print_msg<float>() {
    std::cout << "print_msg called with float type!\n";
}
```

#### 使用非类模板参数
模板的参数不一定非要是类型。你也可以使用常量表达式作为模板参数，例如下面的 add3 函数，它根据传入的布尔值决定如何修改参数：
```cpp
template <bool T> 
int add3(int a) {
    if (T) {
        return a + 3;
    }
    return a;
}
```
这个函数通过模板参数 T 决定是将 a 加上3还是不变。

#### 调用模板函数
下面是如何调用模板函数的示例：
```cpp
// 调用 add<int> 和 add<float>
std::cout << "Printing add<int>(3, 5): " << add<int>(3, 5) << std::endl;
std::cout << "Printing add<float>(2.8, 3.7): " << add<float>(2.8, 3.7) << std::endl;

// 类型推断（函数根据传入的参数类型推断模板类型）
std::cout << "Printing add(3, 5): " << add(3, 5) << std::endl;

// 调用 print_two_values
std::cout << "Printing print_two_values<int, float>(3, 3.2): ";
print_two_values<int, float>(3, 3.2);

// 调用 print_msg
std::cout << "Calling print_msg<int>(): ";
print_msg<int>();
std::cout << "Calling print_msg<float>(): ";
print_msg<float>();

// 调用 add3
std::cout << "Printing add3<true>(3): " << add3<true>(3) << std::endl;
std::cout << "Printing add3<false>(3): " << add3<false>(3) << std::endl;

```

## 3. 包装类
在C++中，包装类是一种**管理资源的类**。资源可以是内存、文件句柄、网络连接等。包装类通常采用RAII（Resource Acquisition Is Initialization，资源获取即初始化）编程技巧，这意味着**资源的生命周期与类实例的生命周期绑定**。当包装类的实例被构造时，它会获取相应的资源；而当实例被销毁时，资源会被释放。
### `IntPtrManager`实现
#### 1. 构造函数
```cpp
IntPtrManager() {
  ptr_ = new int;   // 分配内存
  *ptr_ = 0;        // 初始化值为 0
}

IntPtrManager(int val) {
  ptr_ = new int;   // 分配内存
  *ptr_ = val;      // 初始化为给定的值
}
```
#### 2. 析构函数
* 析构函数通过 `delete` 释放 `ptr_` 指向的内存。为了防止在移动语义中出现悬挂指针，它会检查 `ptr_ `是否为 `nullptr`。
```cpp
~IntPtrManager() {
  if (ptr_) {
    delete ptr_;  // 释放资源
  }
}
```

#### 3. 移动构造函数和移动赋值操作符
* 由于**包装类通常不允许复制**，因为复制可能会导致双重删除资源（即两个对象管理同一资源），因此：删除了拷贝构造函数和拷贝赋值操作符，并实现了移动构造函数和移动赋值操作符。
* 移动构造函数：
```cpp
IntPtrManager(IntPtrManager&& other) {
  ptr_ = other.ptr_;  // 转移资源
  other.ptr_ = nullptr;  // 设置源对象为无效状态
}
```
* 移动赋值操作符：
```cpp
IntPtrManager &operator=(IntPtrManager&& other) {
  if (ptr_ == other.ptr_) {
    return *this;
  }
  if (ptr_) {
    delete ptr_;  // 释放当前资源
  }
  ptr_ = other.ptr_;  // 转移资源
  other.ptr_ = nullptr;  // 设置源对象为无效状态
  return *this;
}
```
#### 4. 删除拷贝构造函数和拷贝赋值操作符
* 为了避免两个对象管理同一资源，IntPtrManager 类的拷贝构造函数和拷贝赋值操作符被删除：
```cpp
IntPtrManager(const IntPtrManager&) = delete;  // 禁止拷贝构造
IntPtrManager& operator=(const IntPtrManager&) = delete;  // 禁止拷贝赋值
```

## 4. 迭代器(Iterator)
C++ 迭代器是**指向容器中元素的对象**，可以用来遍历容器中的元素。迭代器在 C++ STL 中被广泛使用，通常用来访问和修改容器中的元素。指针就是一种常见的迭代器，它可以用来遍历 C 风格数组。
### 4.1 基本操作
1. **解引用运算符（*）**：返回当前迭代器指向元素的值；
2. **自增运算符（++）**：将迭代器指向下一个元素。
STL 中的容器（如 vector, set, unordered_map）都支持迭代器。

### 4.2 自定义迭代器
实现自定义双向链表（DLL）迭代器的示例。
1. 双向链表节点（`Node`）
定义一个节点结构体 Node，它包含指向前一个节点和后一个节点的指针，以及存储的值。
```cpp
struct Node {
  Node(int val) 
    : next_(nullptr), prev_(nullptr), value_(val) {}
  Node* next_;
  Node* prev_;
  int value_;
};
```
2. 自定义迭代器（`DLLIterator`）
实现自定义迭代器，用于遍历双向链表。该迭代器类实现了以下操作符：
* 前缀自增运算符 `++iter`：将迭代器指向下一个节点。
* 后缀自增运算符 `iter++`：类似于前缀自增运算符，但**返回值是递增前的迭代器**。
* 等于运算符 `==` 和不等于运算符 `!=`：判断两个迭代器是否指向同一个节点。
* 解引用运算符 `*`：返回当前节点的值。
```cpp
class DLLIterator {
  public:
    DLLIterator(Node* head) 
      : curr_(head) {}

    DLLIterator& operator++() {     // 前缀自增
      curr_ = curr_->next_;
      return *this;
    }

    DLLIterator operator++(int) {    // 后缀自增
      DLLIterator temp = *this;
      ++*this;
      return temp;
    }

    bool operator==(const DLLIterator &itr) const {
      return itr.curr_ == this->curr_;
    }

    bool operator!=(const DLLIterator &itr) const {
      return itr.curr_ != this->curr_;
    }

    int operator*() {
      return curr_->value_;
    }

  private:
    Node* curr_;
};
```

3. 双向链表（`DLL`）
* `DLL` 类实现了一个基本的双向链表，并且提供了 `Begin` 和 `End` 函数返回迭代器，用于遍历链表。
  `Begin()` 返回指向链表头部的迭代器。
  `End()` 返回指向链表末尾之后位置的迭代器（即 `nullptr`）。
```cpp
class DLL {
  public:
    DLL() : head_(nullptr), size_(0) {}

    ~DLL() {      // 析构函数
      Node *current = head_;
      while(current != nullptr) {
        Node *next = current->next_;
        delete current;
        current = next;
      }
      head_ = nullptr;
    }

    void InsertAtHead(int val) {
      Node *new_node = new Node(val);
      new_node->next_ = head_;

      if (head_ != nullptr) {
        head_->prev_ = new_node;
      }

      head_ = new_node;
      size_ += 1;
    }

    DLLIterator Begin() {
      return DLLIterator(head_);
    }

    DLLIterator End() {
      return DLLIterator(nullptr);
    }

    Node* head_;
    size_t size_;
};
```
4. 使用迭代器遍历双向链表
* 在 `main` 函数中，我们演示了如何使用自定义的双向链表迭代器来遍历链表。
  * 插入元素：通过 `InsertAtHead` 插入元素。
  * 遍历链表：通过前缀和后缀自增运算符来遍历链表。
```cpp
int main() {
  DLL dll;
  dll.InsertAtHead(6);
  dll.InsertAtHead(5);
  dll.InsertAtHead(4);
  dll.InsertAtHead(3);
  dll.InsertAtHead(2);
  dll.InsertAtHead(1);

  // 使用前缀自增运算符遍历
  std::cout << "Printing elements of the DLL dll via prefix increment operator\n";
  for (DLLIterator iter = dll.Begin(); iter != dll.End(); ++iter) {
    std::cout << *iter << " ";
  }
  std::cout << std::endl;

  // 使用后缀自增运算符遍历
  std::cout << "Printing elements of the DLL dll via postfix increment operator\n";
  for (DLLIterator iter = dll.Begin(); iter != dll.End(); iter++) {
    std::cout << *iter << " ";
  }
  std::cout << std::endl;

  return 0;
}
```
## 5. 命名空间
命名空间（namespace） 用于将标识符（如函数、类型、变量等）组织成逻辑上的组，并避免不同标识符之间的命名冲突。命名空间通过**限定作用域**，防止命名冲突。例如，C++ 标准库使用 std 命名空间，因此我们通过 std::cout 来访问输出流对象 cout。:: 操作符用于指定作用域，帮助区分不同命名空间中的标识符。
### 5.1 函数调用
* 若在同一命名空间中调用，可以直接使用函数名；而如果在其他命名空间中调用，需要通过作用域解析运算符 `::` 指定完整的命名空间路径。
```cpp
namespace ABC {
  namespace DEF {
    void uses_spam(int a) {
      std::cout << "Hello from uses_spam: ";
      ABC::spam(a);  // 必须通过 ABC::spam 来调用
    }
  }
}
```
### 5.2 命名空间冲突
* 如果多个命名空间中有相同名字的函数或变量，它们依然可以共存，因为它们的全名（即带有命名空间的名字）是不同的。

### 5.3 using 关键字的使用
`using` 关键字可以将命名空间或命名空间中的特定成员引入当前作用域。它有两个常见用法：
* **引入整个命名空间**：使得命名空间中的所有成员在当前作用域内可以直接使用，而不需要指定命名空间名。
* **引入特定成员**：仅将命名空间中的某个成员引入当前作用域。
```cpp
using namespace B;  // 引入整个 B 命名空间
using C::eggs;  // 引入 C 命名空间中的 eggs 函数
```

## 6. C++标准库容器
### 6.1 `std::vector`
* `std::vector` 是一种动态数组，可以根据需要自动调整大小，能够存储任意类型的元素。
#### 向向量中添加元素
* `push_back` 和 `emplace_back` 都用于向向量中添加元素。
  * `emplace_back` 通常比 `push_back` 更高效，因为它直接在内存中创建元素，而不需要先构造一个临时对象然后再移动。
```cpp
point_vector.push_back(Point(35, 36));  // 使用 push_back 添加元素
point_vector.emplace_back(37, 38);  // 使用 emplace_back 添加元素
```
#### 遍历向量
* 直接/**用下标**遍历元素
```cpp
std::cout << "Printing the items in point_vector:\n";
for (size_t i = 0; i < point_vector.size(); ++i) {
  point_vector[i].PrintPoint();
}

```
#### 删除向量中的元素
```cpp
int_vector.erase(int_vector.begin() + 2);  // 删除索引为 2 的元素
int_vector.erase(int_vector.begin() + 1, int_vector.end());  // 删除索引从 1 到末尾的元素
```
#### 使用 `std::remove_if` 删除符合条件的元素
* `remove_if` 会将符合条件的元素移动到容器末尾，返回一个指向第一个符合条件元素的迭代器，然后通过 `erase` 实际删除这些元素。
```cpp
point_vector.erase(
    std::remove_if(point_vector.begin(), point_vector.end(),
                   [](const Point &point) { return point.GetX() == 37; }),
    point_vector.end());
```
### 6.2 `std::set`
* `std::set` 是一个存储**唯一元素**的容器，且元素会自动按顺序排列。通常，`std::set` 使用**红黑树**来实现，确保元素在插入时能够保持**有序**。
* 支持插入、查找、删除等基本操作；
* **不支持按索引访问，只能通过迭代器遍历**
#### 插入元素
* 通过 `insert` 和 `emplace` 两种方式向 `int_set` 集合中插入元素。
  * 其中 emplace 允许直接构造对象，而不需要先创建临时对象。
```cpp
  // 使用 insert 插入元素 1 到 5
  for (int i = 1; i <= 5; ++i) {
    int_set.insert(i);
  }

  // 使用 emplace 插入元素 6 到 10
  for (int i = 6; i <= 10; ++i) {
    int_set.emplace(i);
  }
```
#### 查找元素
* 使用 `find` 函数查找元素，返回的是一个迭代器：如果找到了元素，返回的迭代器不等于 `end()`，否则等于 `end()`。
```cpp
  std::set<int>::iterator search = int_set.find(2);
  if (search != int_set.end()) {
    std::cout << "Element 2 is in int_set.\n";
  }
```
#### 计数元素
* `count` 函数用于查找某个元素的个数，在 `std::set` 中，每个元素最多只能出现一次，所以返回值要么是 0（元素不存在），要么是 1（元素存在）。
#### 删除元素
`erase(int_set.find(9), int_set.end())` 用于删除从元素 9 开始直到集合末尾的所有元素。
  * 由于`set`有序，即为：删除大于等于9的元素
#### 遍历集合
```cpp
  for (std::set<int>::iterator it = int_set.begin(); it != int_set.end(); ++it) {
    std::cout << *it << " ";  // 通过解引用迭代器访问元素
  }
  std::cout << "\n";

  std::cout << "Printing the elements of the iterator with a for-each loop:\n";
  for (const int &elem : int_set) {
    std::cout << elem << " ";
  }
```
### 6.3 `std::unordered_map`
* `std::unordered_map` 是一个**哈希表**实现的容器，用于存储键值对；每个键是唯一的；且容器中的元素不保证按任何特定顺序排列，而是根据哈希值分布存储。
  * 查找、插入和删除操作的平均时间复杂度为常数级别
* 支持插入、查找、删除和**迭代**操作
```cpp
#include <iostream>   // 用于输出（打印）
#include <unordered_map>  // 包含 std::unordered_map 容器的头文件
#include <string>     // 包含 std::string 类型的头文件
#include <utility>    // 包含 std::make_pair 等实用功能的头文件
```
#### 插入元素
* `std::make_pair` 用于创建一个键值对。
```cpp
map.insert({"foo", 2});  
map.insert(std::make_pair("jignesh", 445));  
map.insert({{"spam", 1}, {"eggs", 2}, {"garlic rice", 3}});  // 批量插入多个键值对
map["bacon"] = 5;  // 通过下标语法插入 "bacon" 键和 5 值
```
#### 查找元素
* 通过迭代器可以访问到键值对，`result->first` 获取键，`result->second` 获取值
```cpp
std::unordered_map<std::string, int>::iterator result = map.find("jignesh");
if (result != map.end()) {
    std::cout << "Found key " << result->first << " with value " << result->second << std::endl;
}
```
### 6.4 使用`auto`关键字
* `auto` 关键字用于告诉编译器**根据变量的初始化表达式来推断变量的类型**。这样，开发者不需要显式地指定类型，编译器会根据右侧的赋值表达式自动推导类型。这对于复杂的类型声明，尤其是模板类或容器类型，非常有用。
#### 复杂类型的推导示例
```cpp
// 定义一个模板类，它的名称非常长。
template <typename T, typename U>
class Abcdefghijklmnopqrstuvwxyz {
public:
  Abcdefghijklmnopqrstuvwxyz(T instance1, U instance2)
      : instance1_(instance1), instance2_(instance2) {}

  void print() const {
    std::cout << "(" << instance1_ << "," << instance2_ << ")\n";
  }

private:
  T instance1_;
  U instance2_;
};

// 返回一个模板类实例的函数
template <typename T>
Abcdefghijklmnopqrstuvwxyz<T, T> construct_obj(T instance) {
  return Abcdefghijklmnopqrstuvwxyz<T, T>(instance, instance);
}

int main() {
  Abcdefghijklmnopqrstuvwxyz<int, int> obj = construct_obj<int>(2);
  auto obj1 = construct_obj<int>(2);  // 使用 auto 推导类型，无需写出长类名
}
```
#### 复制与引用
* `auto` **默认会进行深拷贝**
  * 在此例中，`copy_int_values` 是 `int_values` 的一个副本，修改 `copy_int_values` 不会影响 `int_values`
  * 如果希望避免深拷贝，可以使用 `auto&` 来创建一个引用，这样对 `ref_int_values` 的修改将直接影响 `int_values`
```cpp
std::vector<int> int_values = {1, 2, 3, 4};
// auto 会创建一个副本
auto copy_int_values = int_values;  // 深拷贝 int_values
// 使用 auto& 创建引用，避免深拷贝
auto& ref_int_values = int_values;  // 引用 int_values
```
#### 用于迭代容器
```cpp
// 使用 auto 推导迭代器类型
for (auto it = map.begin(); it != map.end(); ++it) {
  std::cout << "(" << it->first << "," << it->second << ") ";
}
```
## 7. C++标准库动态内存
智能指针：是用于内存管理的一个数据结构，尤其是在没有内存管理内建机制（如垃圾回收）的语言中（例如 C++）。智能指针的主要功能是**自动处理内存的分配与释放**。在现代 C++ 标准库中，常用的智能指针有 `std::unique_ptr` 和 `std::shared_ptr`，它们都在内部封装了原始指针，自动管理内存。
### 7.1 `std::unique_ptr`
* 具有**唯一所有权**的特性。即同一个对象只能被一个 `std::unique_ptr` 管理，不能共享对象的所有权。
* 它**无法进行拷贝**，但可以通过 `std::move` 转移所有权。
* 当 `std::unique_ptr` 离开作用域时，它所管理的对象会被自动释放。这避免了手动释放内存时可能出现的错误（如内存泄漏）。

```cpp
#include <iostream>
#include <memory> // 引入 unique_ptr 功能
#include <string> // 用于打印的字符串库
#include <utility> // 引入 std::move

class Point {
public:
  Point() : x_(0), y_(0) {}
  Point(int x, int y) : x_(x), y_(y) {}
  inline int GetX() { return x_; }
  inline int GetY() { return y_; }
  inline void SetX(int x) { x_ = x; }
  inline void SetY(int y) { y_ = y; }

private:
  int x_;
  int y_;
};

void SetXTo445(std::unique_ptr<Point>& ptr) {
  ptr->SetX(445); // 修改唯一指针指向的对象的 x 值
}

int main() {
  // 初始化 unique_ptr
  std::unique_ptr<Point> u1; // 空指针
  std::unique_ptr<Point> u2 = std::make_unique<Point>(); // 默认构造函数
  std::unique_ptr<Point> u3 = std::make_unique<Point>(2, 3); // 自定义构造函数

  // 判断 unique_ptr 是否为空
  if (u1) {
    // 这个条件不会成立，因为 u1 是空的
    std::cout << "u1's value of x is " << u1->GetX() << std::endl;
  }

  if (u2) {
    // 这个条件会成立，因为 u2 包含一个有效的 Point 实例
    std::cout << "u2's value of x is " << u2->GetX() << std::endl;
  }

  // 打印 unique_ptr 是否为空
  std::cout << "Pointer u1 is " << (u1 ? "not empty" : "empty") << std::endl;
  std::cout << "Pointer u2 is " << (u2 ? "not empty" : "empty") << std::endl;
  std::cout << "Pointer u3 is " << (u3 ? "not empty" : "empty") << std::endl;

  // unique_ptr 不能被拷贝（没有拷贝构造函数），因此以下代码不能编译：
  // std::unique_ptr<Point> u4 = u3;

  // 但是可以通过 std::move 转移所有权
  std::unique_ptr<Point> u4 = std::move(u3);

  // 因为所有权已经转移到 u4，u3 现在为空
  std::cout << "Pointer u3 is " << (u3 ? "not empty" : "empty") << std::endl;
  std::cout << "Pointer u4 is " << (u4 ? "not empty" : "empty") << std::endl;

  // 传递 unique_ptr 给函数
  SetXTo445(u4);  // 通过引用传递，确保所有权没有改变

  // 打印 u4 的 x 值，确认所有权未变且对象已被修改
  std::cout << "Pointer u4's x value is " << u4->GetX() << std::endl;

  return 0;
}
```

### `std::shared_ptr`
`std::shared_ptr` 实现了**共享所有权**的机制，这意味着多个 `shared_ptr` 实例可以指向同一个对象，且每个 `shared_ptr` 的析构都会降低对象的引用计数。当引用计数降至 0 时，所管理的对象会被自动销毁。
* **拷贝构造与赋值**：`std::shared_ptr` 支持拷贝构造和赋值操作符。这些操作会导致引用计数（`use_count`）增加。
* **转移所有权**：可以通过 `std::move` 转移 `shared_ptr` 的所有权，使得原来的 `shared_ptr` 成为“空”指针，而新的 `shared_ptr` 会接管原来的对象。
#### 创建和初始化`shared_ptr`
```cpp
std::shared_ptr<Point> s1;
std::shared_ptr<Point> s2 = std::make_shared<Point>();
std::shared_ptr<Point> s3 = std::make_shared<Point>(2, 3);
```

#### 引用计数与拷贝构造
* 初始时，`s3` 是唯一一个指向 `Point` 对象的指针，因此引用计数为 1；将 `s3` 拷贝给 `s4` 后，引用计数增加到 2，表示 `s3` 和 `s4` 都指向同一块内存。
  * 修改 `s3` 的数据会影响 `s4`，因为它们共享同一个对象。**`shared_ptr` 确保当任何一个指针修改对象时，所有的共享指针都会看到这个变化**。
```cpp
std::cout << "s3 use_count: " << s3.use_count() << std::endl;  // 输出 1
std::shared_ptr<Point> s4 = s3;
std::cout << "s3 use_count after copy: " << s3.use_count() << std::endl;  // 输出 2
```

#### 转移所有权
* 使用 `std::move` 可以将 `s5` 的所有权转移到 `s6`。此时，`s5` 变为空指针，而 `s6` 接管了原来 `s5` 所指向的对象。
```cpp
std::shared_ptr<Point> s6 = std::move(s5);
```

#### 通过引用和右值引用传递`shared_ptr`
* 通过**引用**传递时，原始的 `shared_ptr` 仍然**保持所有权**；
* 通过**右值引用**传递时，**所有权会转移到函数内的指针**。
```cpp
void modify_ptr_via_ref(std::shared_ptr<Point> &point) { point->SetX(15); }
void modify_ptr_via_rvalue_ref(std::shared_ptr<Point> &&point) {point->SetY(645);}
```

## 8. C++标准库同步原语
在并发编程中，同步原语用于管理线程之间的访问顺序、共享资源的访问权限以及防止数据竞争。在 C++ 标准库（STL）中，提供了一些同步原语，用于实现线程安全和协调多个线程的执行。以下是 STL 中常见的同步原语及其说明。
### 8.1 `std::mutex`
* `std::mutex` 提供了基本的互斥锁功能，保证在**同一时刻只有一个线程可以访问**共享资源。
  * `lock()` 和 `unlock()` 用于手动获取和释放锁，确保线程在访问共享资源时是独占的。
  * C++11 引入了 `std::lock_guard` 和 `std::unique_lock` 等工具，这些工具通过 RAII（资源获取即初始化）模式简化了锁的管理，自动管理锁的获取和释放。

```cpp
#include <iostream> // 包含 std::cout 用于打印输出
#include <mutex>    // 包含 std::mutex 库
#include <thread>   // 包含 std::thread 库，用于创建线程

// 定义一个全局计数变量和一个 mutex 互斥锁
int count = 0;
std::mutex m;  // 声明并默认初始化一个互斥锁 m

// add_count 函数允许线程原子性地将 count 增加 1
void add_count() {
  // 在访问共享资源 count 之前，首先获取锁
  m.lock();
  count += 1;
  // 操作完成后，释放锁
  m.unlock();
}

int main() {
  // 创建两个线程，分别运行 add_count 函数
  std::thread t1(add_count);
  std::thread t2(add_count);

  // 等待两个线程完成执行
  t1.join();
  t2.join();

  // 打印 count 的最终值
  std::cout << "Printing count: " << count << std::endl;
  return 0;
}
```

### 8.2 `std::scoped_lock`
* `std::scoped_lock` 是 C++11 引入的一个新的互斥锁包装类，它遵循 RAII（资源获取即初始化）范式。在构造 `std::scoped_lock` 对象时，它会**自动获取指定的互斥锁，并在作用域结束时自动释放锁**。
  * 这使得锁的管理变得更加简洁和安全，避免了手动调用 `lock()` 和 `unlock()` 的错误;
  * 它确保互斥锁会被自动释放，无论函数如何退出（正常结束或抛出异常）;
  * `std::scoped_lock` 会**按传递顺序**自动获取多个锁：如果需要在同一作用域中锁定多个互斥锁，可以使用 `std::scoped_lock` 来确保按正确的顺序获取锁，避免死锁问题。

```cpp
#include <iostream> // 包含 std::cout 用于打印输出
#include <mutex>    // 包含 std::mutex 库
#include <thread>   // 包含 std::thread 库，用于创建线程

// 定义一个全局计数变量和一个互斥锁
int count = 0;
std::mutex m;  // 声明并默认初始化一个互斥锁 m

// add_count 函数允许线程原子性地将 count 增加 1
void add_count() {
  // 使用 std::scoped_lock 自动获取互斥锁 m
  std::scoped_lock slk(m);  // 锁 m，作用范围在 slk 对象的生命周期内
  count += 1;
  // 在函数结束时，slk 销毁，mutex m 被自动释放
}

int main() {
  // 创建两个线程，分别运行 add_count 函数
  std::thread t1(add_count);
  std::thread t2(add_count);

  // 等待两个线程完成执行
  t1.join();
  t2.join();

  // 打印 count 的最终值
  std::cout << "Printing count: " << count << std::endl;
  return 0;
}
```

### 8.3 条件变量`std::condition_variable`
#### 主要功能
* 条件变量使得线程可以在满足某个条件之前挂起（等待）；
* 通知机制：线程可以**通过条件变量，通知等待线程某个条件的改变**（例如，条件变为true）；

#### 关键组件
1. `std::mutex`：用于保护共享资源，防止数据竞争；
2. `std::condition_variable`：用于实现条件同步；
3. `std::unique_lock`：配合条件变量使用，允许线程在等待期间释放锁，以便其他线程可以访问共享资源；当条件满足时，`unique_lock` 会重新获得锁。
  * `cv.wait(lk, pred)`：该函数**使线程阻塞，直到 `pred` 条件返回 `true`**。在等待期间，线程会释放锁，当条件成立时会重新获取锁。

```cpp
#include <condition_variable>
#include <iostream>
#include <mutex>
#include <thread>

int count = 0;                    // 全局计数器
std::mutex m;                     // 互斥锁，用于同步对 count 的访问
std::condition_variable cv;       // 条件变量，用于线程间同步

// 函数用于增加 count 值并在 count == 2 时通知等待线程
void add_count_and_notify() {
  std::scoped_lock slk(m);        // 自动加锁和解锁
  count += 1;
  if (count == 2) {
    cv.notify_one();              // 如果 count 达到 2，通知一个等待线程
  }
}

// 等待线程函数，等待 count == 2 时继续执行
void waiter_thread() {
  std::unique_lock lk(m);         // 使用 unique_lock 管理锁
  cv.wait(lk, []{ return count == 2; });  // 等待条件成立，count == 2
  std::cout << "Printing count: " << count << std::endl; // 打印 count 值
}

int main() {
  // 创建三个线程，t1 和 t2 会运行 add_count_and_notify，t3 运行 waiter_thread
  std::thread t1(add_count_and_notify);
  std::thread t2(add_count_and_notify);
  std::thread t3(waiter_thread);
  
  // 等待线程执行完成
  t1.join();
  t2.join();
  t3.join();
  
  return 0;
}
```

### 8.4 C++ STL 示例：读写锁（RWLock）模拟
C++ 标准库（STL）并没有直接提供读写锁（Reader-Writer Lock）的实现，但可以通过 `std::shared_mutex`、`std::shared_lock` 和 `std::unique_lock` 来模拟读写锁的行为。

#### 关键组件
1. `std::shared_mutex m`：共享互斥锁，保护对 `count` 变量的访问
2. 共享锁（`std::shared_lock`）：在 `read_value()` 中，`std::shared_lock` 允许多个线程同时读取 `count`，但不能同时进行写入操作。
3. 独占锁（`std::unique_lock`）：在 `write_value()` 中，`std::unique_lock` 确保每次只有一个线程可以修改 `count`，从而避免了数据竞争。

```cpp
#include <iostream>
#include <mutex>
#include <shared_mutex>
#include <thread>

// 全局变量 count 和共享互斥锁 m，用于保护 count
int count = 0;
std::shared_mutex m;

// 读取值的函数，使用 std::shared_lock 获取共享锁，表示读操作
void read_value() {
  std::shared_lock lk(m);  // 获取共享锁
  std::cout << "Reading value " + std::to_string(count) + "\n" << std::flush;
}

// 写入值的函数，使用 std::unique_lock 获取独占锁，表示写操作
void write_value() {
  std::unique_lock lk(m);  // 获取独占锁
  count += 3;  // 对 count 进行修改
}

int main() {
  // 创建六个线程，两个执行写操作，四个执行读操作
  std::thread t1(read_value);  // 读取操作
  std::thread t2(write_value); // 写入操作
  std::thread t3(read_value);  // 读取操作
  std::thread t4(read_value);  // 读取操作
  std::thread t5(write_value); // 写入操作
  std::thread t6(read_value);  // 读取操作

  // 等待所有线程完成
  t1.join();
  t2.join();
  t3.join();
  t4.join();
  t5.join();
  t6.join();

  return 0;
}
```
