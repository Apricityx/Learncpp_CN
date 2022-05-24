---
title: 2.5 局部变量和局部作用域
alias: 2.5 局部变量和局部作用域
tag:

---

> [!INFO] 原文信息：
> - 链接： [2.5 — Introduction to local scope](https://www.learncpp.com/cpp-tutorial/introduction-to-local-scope/)
> - 作者：[_ALEX_](https://www.learncpp.com/author/Alex/ "View all posts by Alex")
> - 2022-5-21


## 局部变量

函数的[[形参(parameters)]]和函数内部定义的变量一样，都是[[局部变量(local variable)]] (局部变量是和[[全局变量(global variable)]]相对的概念，在后面的课程中我们会介绍全局变量）。

```cpp
int add(int x, int y) // function parameters x and y are local variables
{
    int z{ x + y }; // z is a local variable too

    return z;
}
```

本节课将详细介绍[[局部变量(local variable)]]所具有的一些属性。

## 局部变量生命周期

在[[1-3-Introduction-to-objects-and-variables]]中我们讨论了以形如 `int x;` 的方式定义变量是如何在语句执行时[[实例化(instantiated)]] 变量的。函数的[[形参(parameters)]]是在进入函数时被创建和初始化的，函数体内的函数则是在定义时创建并初始化的。

例如：

```cpp
int add(int x, int y) // x 和 y 在此处创建并初始化
{
    int z{ x + y }; // z 在此处被创建和初始化

    return z;
}
```

此时我们不经要问，”那么实例化的变量是在合适被销毁的呢？“。[[局部变量(local variable)]]是在**包含其定义的一组花括号的结尾**时销毁的，其销毁的顺序和定义顺序正好相反（对于[[形参(parameters)]]来说，则是在函数结束时销毁）。

```cpp
int add(int x, int y)
{
    int z{ x + y };

    return z;
} // z, y, and x destroyed here
```

正如人的生命周期被定义为其出生到死亡的这段时间一样，变量的[[生命周期(lifetime)]]指的是该变量被创建到被销毁的这段时间。注意，变量的创建和销毁都是在程序运行的时候发生的（称为[[运行时(runtime)]]），而不是在[[编译时(compile-time)]]发生的。因此，生命是一个运行时属性。

!!! info "扩展阅读"

	The above rules around creation, initialization, and destruction are guarantees. That is, objects must be created and initialized no later than the point of definition, and destroyed no earlier than the end of the set of the curly braces in which they are defined (or, for function parameters, at the end of the function).

	In actuality, the C++ specification gives compilers a lot of flexibility to determine when local variables are created and destroyed. Objects may be created earlier, or destroyed later for optimization purposes. Most often, local variables are created when the function is entered, and destroyed in the opposite order of creation when the function is exited. We’ll discuss this in more detail in a future lesson, when we talk about the call stack.

下面这段程序展示了有关变量 `x` 生命周期更复杂的情况：

```cpp
#include <iostream>

void doSomething()
{
    std::cout << "Hello!\n";
}

int main()
{
    int x{ 0 }; // x 的生命周期起点

    doSomething(); // 在函数调用期间，x 仍然存在

    return 0;
} // x 的生命周期终点
```

在上面的例子中，x 的生命周期从它的定义开始一直到 `main` 函数的末尾结束。这其中包含了函数 `doSomething` 执行的时间。

## 局部作用域

一个[[标识符(identifier)]]的[[作用域(scope)]]表明了该表示符在源码中能够被访问的位置。如果一个标识符可以被访问，则称其在作用域中。当一个标识符不能被访问时，则称其超出了作用域（Out of scope）。作用域是一个[[编译时(compile-time)]]属性，如果在标识符的作用域之外访问它，会导致编译错误。

局部变量的作用域从该变量的定义开始，在包含了该变量定义的一组花括号的结尾终止（对于函数[[形参(parameters)]]则在函数结尾终止）。这确保了变量在定义前不能被使用（[[即使编译器选择在此之前创建变量]]）。在一个函数中定义的局部变量，其作用域不能延伸到其他函数中。

下面的例子展示了变量 `x` 的作用域：

```cpp
#include <iostream>

// x 不在该函数的作用域
void doSomething()
{
    std::cout << "Hello!\n";
}

int main()
{
    // x 此时还不能被访问，因为还没有进入其作用域

    int x{ 0 }; // x 从此时进入作用域，随后可以在该函数中使用它

    doSomething();

    return 0;
} // x 作用域终止与此，此后不再能够被使用
```

在上面的程序中，变量 `x` 从其定义位置开始进入作用域，在 `main` 函数结束时离开其作用域。注意，变量 `x` 的作用域不会延伸到函数 `doSomething()` 中。这也一位置 `main` 函数调用 `doSomething` 和该该变量的作用域没有任何关系。

## “超出作用域” 和 “离开作用域”

术语[[超出作用域(out of scope)]] 和[[离开作用域(going out of scope)]]对于新手来说可能令人困惑。

标识符超出作用域表示其不能在代码中被访问。在上面的例子中，[[标识符(identifier)]] `x` 进入作用域的时间从其定义开始到 `main` 函数结尾为止。这段代码以外的代码中访问该标识符，都属于超出作用域。

术语离开作用域描述的对象则一般是某个对象而不是标识符。我们说一个变量会在实例化它作用域的结尾处（花括号结束处）离开作用域。在上面的例子中，[[对象(objec)]] `x` 会在 `main` 函数的结尾处离开作用域。

具备变量的生命周期会在其离开作用域时终止，局部变量会在此时被销毁。

注意，并不是所有类型的变量都会在其离开作用域时被销毁。我们会在将来的课程中介绍此类例子。

## 其他例子

下面这个例子稍微更复杂一定。请记住，生命周期是一个运行时属性，而作用域是编译时属性。虽然，我们在同一段代码中讨论两者，它们的出发点是不同的。

```cpp
#include <iostream>

int add(int x, int y) // x 和 y 在此处被创建并进入作用域
{
    // x 和 y 仅在该函数中可见/可用
    return x + y;
} // y 和 x 离开作用域并被销毁

int main()
{
    int a{ 5 }; // a 在此处被创建、初始化并进入作用域
    int b{ 6 }; // b 在此处被创建、初始化并进入作用域

    // a 和 b 只在该函数中可用
    std::cout << add(a, b) << '\n'; // calls function add() with x=5 and y=6

    return 0;
} // b 和 a 离开作用域并被销毁
```

函数形参 `x` 和 `y` 在 `add` 函数被调用时创建，作为变量，它们只在 `add` 函数中可见可用，并在 `add` 函数结束时销毁。变量 `a` 和 `b` 在函数 `main` 中被创建，只在 `main` 函数中可用，并且在 `main` 函数结束时被销毁。

为了强化理解，让我们逐步解析该函数，下列操作按顺序发生：
- 程序从 `main` 函数的顶部开始执行；
- `main` 函数的局部变量 `a` 被创建并初始化为 5；
- `main` 函数的局部变量 `b` 被创建并初始化为 6；
- `add` 函数被调用，函数的[[实参(arguments)]]为 5 和 6；
- `add` 的变量 `x` 被创建并初始化为 5；
- `add` 的变量 `y` 被创建并初始化为 6；
- 表达式 `x + y` 求值得到结果 11；
- `add` 将值 11 拷贝返回给主调函数 `main`；
- `add` 的 `y`  和 `x` 被销毁；
- `main` 在控制台打印 11；
- `main` 返回 0 给操作系统；
- `main` 的局部变量 `b`  和 `a` 被销毁；

以上。

注意，如果函数 `add` 被先后两次调用，那么对应的形参 `x`  和 `y` 也被先后创建并销毁两次——每次调用就会发生一次创建和销毁。如果一个程序中有大量的函数调用，那么变量也会随之被频繁的创建和销毁。

## 函数的隔离

在上述的例子中，很明显可以看出，变量 `a` 和 `b` 是不同于变量 `x` 和 `y` 的变量。

现在，请考虑下面这个程序：

```cpp
#include <iostream>

int add(int x, int y) // x 和 y 在此处被创建并进入作用域
{
    // x 和 y 仅在该函数中可见/可用
    return x + y;
} // y 和 x 离开作用域并被销毁

int main()
{
    int x{ 5 }; // x 在此处被创建、初始化并进入作用域
    int y{ 6 }; // y 在此处被创建、初始化并进入作用域

    // x 和 y 只在该函数中可用
    std::cout << add(x, y) << '\n'; // calls function add() with x=5 and y=6

    return 0;
} // x 和 y 离开作用域并被销毁
```

与前一个例子相比，本例只是将 `main` 函数中的变量 `a` 和 `b` 修改成了 `x` 和 `y`。这个程序的编译和运行和之前的程序没有任何区别，即使 `main` 函数和 `add` 函数都使用了变量名 `x` 和 `y`。为什么这样也可以工作呢？

首先，我们需要认识到的是，即使 `main` 函数和 `add` 函数都使用了变量名 `x` 和 `y`，但是这些变量实际上是完全不同的。`main` 函数中的 `x` 和 `y`，和 `add` 函数中的 `x` 和 `y` 是没有任何关系的——它们只是碰巧同名罢了。

其次，在 `main` 函数中， 变量名 `x` 和 `y` 指的是 `main` 函数局部作用中的变量 `x` 和 `y`。这些变量只能够在 `main` 函数中被看到（被使用）。同样的，在 `add` 函数中，, 变量名 `x` 和 `y` 指的是 `add` 函数的形参 `x` 和 `y`，它们也只能够在 `add` 中被看到和使用。

长话短说，其实 `add` 和 `main` 这两个函数都不知道对方使用了与自己相同的变量名。因为，这些变量的作用域并没有重叠的部分，因此对于编译器来说，`x` 和  `y` 在任何时候被引用，其指代的对象都是清楚的。  

!!! tldr "关键信息"

	函数形参或者是局部变量在声明的时候所使用的名字，只在函数体内部可见。也就是说，函数在定义局部变量的时候，可以不考虑其他函数是否使用了某些变量名。这一特性，可以帮助函数保持独立性。

在后续的章节中，我们会探讨关于局部作用域的更多内容，同时也会谈到其他类型的作用域。

## 在何处定义局部变量 

函数体内的局部变量，其定义的位置应该尽可能解决其第一次被使用的位置：

```cpp
#include <iostream>

int main()
{
    std::cout << "Enter an integer: ";
    int x{}; // x 在此处定义
    std::cin >> x; // 在此处使用

    std::cout << "Enter another integer: ";
    int y{}; // y 在此处定义
    std::cin >> y; // 在此处使用

    int sum{ x + y }; // sum 在此处定义
    std::cout << "The sum is: " << sum << '\n'; // 在此处使用

    return 0;
}
```


在上面的例子中，每个变量都是在它第一次被使用时的前一行定义的。这个规定导师无需严格遵守——如果你希望将第五行和第六行的位置换一下也是可以的。

!!! success "最佳实践"

	尽量在局部变量第一次被使用前最近的地方定义该局部变量。