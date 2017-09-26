---
title: C++异常处理之noexcep之
categories: programming
tags:
  - c++
  - 异常处理
date: 2017-09-25 00:07:40
---

在C语言中，如果程序的运行出现异常、错误，我们想提供方案处理这些异常时，我们面临许多问题，如： 
* C语言没有提供统一（标准）的方式来处理错误； 
* 无法保证错误会被正确的处理； 
* 错误的传播无法控制，特别是在函数的嵌套调用时；   

当程序在运行时发生错误，使得程序的继续运行变得毫无意义时，C++中的异常机制给我们提供了一个解决方法。

<!-- more -->

### C++03 异常处理（throw）
C++98中，在函数声明时，我们使用throw指定一个函数可以抛出异常的类型。例如：

```c++
class Ex {
public:
  double getVal();
  void display() throw();
  void setVal(int i) throw (char*, double);
 private:
   int m_val;
};
```
上述函数的声明指定了该函数可以抛出异常的类型： `getVal()` 可以抛出任何异常(默认)；   
`display()` 不可以抛出任何异常； `setVal()` 只可以抛出`char*` 和 `double`类型异常。  
从功能上来说，C++98中的异常处理机制完全能满足我们的需要，正确的处理异常。   
然而，编译器为了遵守C++语言标准，在编译时，只检查部分函数的异常规格（exception specification）   
`exception specification`: 函数名字后面的`throw`表达式，或者`noexcept`。 
```c++
// declaration
extern void funAny(void);                   //May throw ANY exception.
void check(void) throw (std::out_of_range); // May throw only std::out_of_range.

// implementation
void check(void) throw(std::out_of_range) {
    funAny();   // Compiler does not check if
    ...         // funAny(), or one of its 
}               // subordinates, only throws std::out_of_range!
```
程序在运行时，如果`funAny()`抛出一个异常，但是它的类型不是`std::out_of_range`，   
异常处理机制将调用`std::unexpected()`（该函数自己也可能抛出异常），   
这个函数默认情况下会调用`std::teminate()`.

### C++11异常处理（noexcept）

编译器在编译时能过做的检测非常有限，因此在C++11中异常声明被简化为以下两种情况： 
* 函数可以抛出任何异常(和之前的默认情况相同)； 
* 函数不可以抛出任何异常。

在C++11中，声明一个函数不可以抛出任何异常使用关键字noexcept.
```c++
void mightThrow(); // could throw any exceptions.
void doesNotThrow() noexcept; // does not throw any exceptions.
```
下面两个函数声明的异常规格在语义上是相同的，都表示函数不抛出任何异常。
```c++
void old_stytle() throw();
void new_style() noexcept;
```
它们的区别在于程序运行时的行为和编译器优化的结果。   
使用`throw()`， 如果函数抛出异常，异常处理机制会进行栈回退，寻找(一个或多个）`catch`语句。    
此时，检测`catch`可以捕捉的类型，如果没有匹配的类型，`std::unexpected(`)会被调用。     
但是`std::unexpected()`本身也可能抛出异常。如果`std::unexpected()`抛出的异常对于当前的异常规格是有效的，   
异常传递和栈回退会像以前那样继续进行这意味着，如果使用`throw`， 编译器几乎没有机会做优化。  
事实上，编译器甚至会让代码变得更臃肿、庞大：  
* 栈必须被保存在回退表中； 
* 所有对象的析构函数必须被正确的调用（按照对象构建相反的顺序析构对象）； 
* 编译器可能引入新的传播栅栏（`propagation barriers`）、引入新的异常表入口，使得异常处理的代码变得更庞大； 
* 内联函数的异常规格（`exception specification`）可能无效的。   

当使用`noexcep`t时，`std::teminate()`函数会被立即调用，而不是调用`std::unexpected()`;    
因此，在异常处理的过程中，编译器不会回退栈，这为编译器的优化提供了更大的空间。   

简而言之，如果**你知道你的函数绝对不会抛出任何异常**，应该使用`noexcept`, 而不是`throw()`.