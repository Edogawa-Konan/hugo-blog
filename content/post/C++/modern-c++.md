---
title: "Modern C++"
date: 2019-11-20T16:54:50+08:00
draft: false
tags: ["C++"]
categories: ["C++"]
---

# modern c++

## 语言可用性的强化

### 常量

1. `nullptr`表示空指针，专门用来区分空指针、0。

2. C++11 提供了 `constexpr` 让用户显式的声明函数或对象构造函数在编译期会成为**常量表达式**，而且可以使用递归。

   ```c++
   constexpr int fibonacci(const int n) {
       if(n == 1) return 1;
       if(n == 2) return 1;
       return fibonacci(n-1) + fibonacci(n-2);
   }//from c++14 
   ```

### 变量和初始化

1. 在传统 C++ 中，变量的声明虽然能够位于任何位置，甚至于 `for` 语句内能够声明一个临时变量 `int`，但始终没有办法在 `if` 和 `switch` 语句中声明一个临时的变量。C++17 消除了这一限制，使得我们可以在 if（或 switch）中完成这一操作：

   ```c++
   // 将临时变量放到 if 语句内
   if (const std::vector<int>::iterator itr = std::find(vec.begin(), vec.end(), 3);
       itr != vec.end()) {
       *itr = 4;
   }
   ```

2. 在传统 C++ 中，不同的对象有着不同的初始化方法，例如普通数组、 POD （**P**lain **O**ld **D**ata，即没有构造、析构和虚函数的类或结构体） 类型都可以使用 `{}` 进行初始化，也就是我们所说的初始化列表。 而对于类对象的初始化，要么需要通过拷贝构造、要么就需要使用 `()` 进行。**也就是说对象不能通过`{}`来初始化(列表初始化)**。

   类模板`initializer_list`用于访问初始化列表（initialization list）,列表元素的数据类型为const T。编译器从花括号封闭的，元素由逗号分隔开的初始化列表自动构造initializer_list模板类，这一过程中，编译器自动查询哪些构造函数可以用`initializer_list<T>`作为参数，并据此确定initializer_list的模板参数T的类型，从而对braced-init-list做类型转换。例如：

   ```c++
   auto il = { 10, 20, 30 };  // the type of il is an initializer_list<int> 
   ```

   ```c++
   class myclass {
     myclass (int,int);
     myclass (initializer_list<int>);
     /* definitions ... */
   };
   myclass foo {10,20};  // calls initializer_list ctor
   myclass bar (10,20);  // calls first constructor tips：
   ```

   - 如果使用`std::initializer_list`定义构造函数，最好也相应重载赋值运算符和提供深拷贝构造函数。

3. ==Todo结构化绑定==

### 类型推导

1. `auto`关键字，但是注意其不能用于**函数传参和推导数组类型**。

   从 C++14 开始是可以直接让普通函数具备返回值推导：

   ```c++
   template<typename T, typename U>
   auto add3(T x, U y){
       return x + y;
   }
   ```

2. `decltype` 关键字是为了解决 auto 关键字只能对变量进行类型推导的缺陷而出现的，它的用法和 `sizeof` 很相似：

   ```c++
   decltype(表达式)
   ```

   Ps:`std::is_same` 用于判断 `T` 和 `U` 这两个类型是否相等。

### 控制流

1. C++11 引入了 `constexpr` 关键字，它将表达式或函数编译为常量结果。C++17 将 `constexpr` 这个关键字引入到 `if` 语句中，允许在代码中声明常量表达式的判断条件：

   ```c++
   #include <iostream>
   
   template<typename T>
   auto print_type_info(const T& t) {
       if constexpr (std::is_integral<T>::value) {
           return t + 1;
       } else {
           return t + 0.001;
       }
   }
   int main() {
       std::cout << print_type_info(5) << std::endl;
       std::cout << print_type_info(3.14) << std::endl;
   }
   ```

2. 区间for循环

### 面向对象

1. C++11 利用关键字 using 引入了继承构造函数的概念：

   ```c++
   #include <iostream>
   class Base {
   public:
       int value1;
       int value2;
       Base() {
           value1 = 1;
       }
       Base(int value) : Base() { // 委托 Base() 构造函数
           value2 = value;
       }
   };
   class Subclass : public Base {
   public:
       using Base::Base; // 继承构造
   };
   int main() {
       Subclass s(3);
       std::cout << s.value1 << std::endl;  // 1
       std::cout << s.value2 << std::endl;  // 3
   }
   ```

2. `override`和`final`关键字。注意，它们都是放在最后面。

3. 可以显式禁用默认函数：

   ```c++
   class Magic {
       public:
       Magic() = default; // 显式声明使用编译器生成的构造
       Magic& operator=(const Magic&) = delete; // 显式声明拒绝编译器生成构造
       Magic(int magic_number);
   }
   
   ```

4. C++11 引入了枚举类（enumeration class），并使用 `enum class` 的语法进行声明：

   ```c++
   enum class new_enum : unsigned int {
       value1,
       value2,
       value3 = 100,
       value4 = 100
   };
   
   ```

   这样定义的枚举实现了类型安全，首先他不能够被隐式的转换为整数，同时也不能够将其与整数数字进行比较， 更不可能对不同的枚举类型的枚举值进行比较，相同类型的则可以比较。

   枚举类型后面使用了冒号及类型关键字来指定枚举中枚举值的类型，这使得我们能够为枚举赋值（未指定时将默认使用 int）。而我们希望获得枚举值的值时，将必须**显式的进行类型转换**。

## 语言运行期的强化



