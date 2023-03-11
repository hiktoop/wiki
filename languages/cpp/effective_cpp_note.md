# effective cpp note

## 导言

- 编写易于理解、可维护、易移植、可扩展、效率高的代码

- 选择合适的特性

- 认识到编译器未定义的行为

- **通过案例学习背后的原理**

## 习惯c++

### cpp是语言联邦

- 过程化(procedural)

- 面向对象(object-oriented)

- 函数化(functional)

- 泛型(generic)

- 元编程(metaprogramming)

> 如此丰富，如何避免混乱？

#### 分类

1. C

2. Object-Oriented cpp

3. Template cpp

4. STL

### 用`consts/enums/inlines`取代`#definie`

#### 原理

`#define`不是语言的一部分，预编译的结果：

- 直接替换，不加入符号表 --> 报错常量引人疑惑、多处拷贝占用空间

#### 类内常量

只需声明：`static const int pi = 3.14;`c++要求为使用的任何东西提供定义，但是static整型族的类内常量是例外。

此时不能使用它的地址，除非你给出定义：`const int item::pi;`注意是不需要初始值的。

> 比较老的编译器声明时初始化是不被允许的，这时定义时初始化即可。

#### `the enum hack`

当你需要在类内声明一个常量数组时，这样：

```cpp
class item{
private: 
    enum {num = 5};
    int arr[num];
}
```

`enum`更像是`#define`而不是`const`，没有地址，不可被引用或指向。

#### 对function-like macros用inline-functions取代

感受：这种宏确实很难理解，但是为何很多地方还要用甚至弘扬呢？

### 尽可能用`const`

谋求编译器做出限制，**人是经常出错的**、**限制就是确定，反之可能是未知**

> `int const *a == const int *a ！= int * const a`，**从右往左读**

#### STL iterator以pointer为原型

`iterator`类似于`T *pointer`，故`const iterator`类似于声明指针为`const`，而其指向的东西可变。

```cpp
vector<int> vec;
const std::vector<int>::iterator it = vec.begin();
*it = 10 // OK
it++; // error! it is const
std::vector<int>::const_iterator it = vec.begin();
*it = 10; // error! *it is const
it++; // OK
```

#### const 成员函数

```c
const char &operator[](size_t pos) const {}
```

> 两个成员函数如果只是常量性不同，可以被重载

##### bitwise constness(编译器强制实施)

`const`成员函数不可以更改对象内任何`non-static`成员变量，值得注意的是**修改成员內指针的值**是满足此条件的。例如这可能导致成员内`char *`变量的改变。

##### logical constness

> 把可能在常量函数中修改的变量定义为`mutable`

客户端检测不出时函数可以修改它所处理的对象的某些bits

##### 相同的实现时，non-const函数调用const函数，而不是相反！

> 怎么能对外保证`const`而实际用`non-const`呢？及其失信！

### 确认对象使用前已经初始化

- 内置类型手工初始化

- 自定义类型确保每个构造函数对每个成员都初始化，**注意区分初始化与赋值**

对象的成员变量的初始化动作发生在进入构造函数本体之前，一般是`default`构造函数。

> 使用`menber initialization list`替换赋值动作：

```c
item::item(const string &name, const list &phones) {
    thename = name; thePhones = phones; num = 0; // 先default初始化后才赋值！
}
item::item(const string &name, const list &phones) :
    theName(name), thePhones(phones), num(0) {} // 是初始化，效率很高！
```

> 尽量以声明的顺序书写初值列表

感受：这一条款的后半部分有些复杂，需要重读

## 构造/析构/赋值运算

> 兵者，国之大事，死生之地，存亡之道，不可不察也！
> 
>                                                                             ----孙子

### c++默认编写并调用的函数
