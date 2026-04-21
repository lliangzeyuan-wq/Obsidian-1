---
data: 2026-04-21
---
[C++ decltype的用法（非常详细） - C语言中文网](https://c.biancheng.net/view/b0eivk0.html)

# C++ decltype的用法（非常详细）

decltype 关键字在 [C++](https://c.biancheng.net/cplus/) 中用于查询表达式的类型，而不进行表达式的评估。这一特性使得 decltype 成为模板编程和类型推导中非常有用的工具，特别是在需要精确控制类型行为的场合。  

## C++ decltype的基本用法

decltype用来查询变量或表达式的类型，其基本用法如下：

- int x = 0;
- decltype(x) y = 1; // y 的类型为 int
- auto z = 2.0;
- decltype(z) w = 2.3; // w 的类型为 double

在这里，decltype 直接推导出变量的类型，并用于声明新的变量。  
  
decltype 特别适用于复杂的表达式，其中涉及的操作可能改变结果的类型。例如：

- int n = 0;
- double d = 0.0;
- decltype(n + d) result; // result 的类型为 double

这种能力使得 decltype 在模板编程中尤为重要，可以确保类型精确匹配。  

## C++ decltype与auto的差异化

#### 1) 类型推导差异

尽管 auto 和 decltype 都用于类型推导，但它们的行为有显著差异。auto 忽略顶层 const 和引用，而 decltype 则保留这些类型修饰符。  
  
例如：

- const int ci = 0;
- auto ai = ci;       // ai 的类型为 int
- decltype(ci) di = ci; // di 的类型为 const int

这种区别使得 decltype 更适合需要保留表达式完整类型信息的场景。  

#### 2) 与引用结合使用

当 decltype 用于引用的表达式时，其行为也会有所不同。例如：

- int x = 0;
- int& xr = x;
- decltype(xr) y = xr; // y 的类型为 int&，与 xr 一致

这说明 decltype 能够精确地推导出包括引用在内的完整类型。  

## C++ decltype的复杂用法

#### 1) 模板与decltype

decltype 在模板编程中极为有用，尤其当函数的返回类型依赖于参数类型时。例如：

- template<typename T, typename U>
- auto multiply(T t, U u) -> decltype(t * u) {
-     return t * u;
- }

在这里，decltype 用于推导两个模板参数乘积的类型，确保返回类型的正确性。  

#### 2) 条件表达式与decltype

decltype 同样适用于条件表达式，它可以保证返回类型的一致性，特别是在条件表达式的结果类型可能不同的情况下。例如：

- int i = 4;
- float f = 5.5;
- decltype((i < f) ? i : f) result; // result 的类型为 float

通过上述探讨，我们看到 decltype 提供了一种强大的机制，用于精确地掌握和操作表达式和变量的类型。它在模板编程和复杂的类型推导场景中尤为重要，允许开发者写出更安全、更健壮的类型安全代码。