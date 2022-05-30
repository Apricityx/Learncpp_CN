---
title: 4.5 - 无符号整型以及为什么要避免使用它
alias: 4.5 - 无符号整型以及为什么要避免使用它
origin: /unsigned-integers-and-why-to-avoid-them/
origin_title: "4.5 — Unsigned integers, and why to avoid them"
time: 2022-4-11
type: translation
tags:
- data type
- integer
- overflow
---

??? note "关键点速记"

    - 无符号整型表示超过范围的数时，实际存储的值为原值除以范围最大值加 1 后的余数。例如，`value mod 2^32`。称为无符号的反转。
    - 无符号会翻转，有符号会溢出
    - 无符号反转可以向0反转也可以向最大值反转
    - 无符号整型表示超过范围的数时不会产生未定义行为，它的行为是确定的，因此 C++ 标准明确说明无符号整型不会"溢出"
    - 混用无符号和有符号类型时可能产生问题，因为有符号数会被隐式转换为无符号数，此时可能发生反转。
    - 尽量使用有符号数保存数量值和进行数学运算

## 无符号整型

在上节课 ([[4-4-Signed-integers|4.4 - 有符号整型]]) 中，我们介绍了有符号整型，这些征信可以保存正负整数和 0。

C++ 还支持无符号整型。无符号整型只能表示非符整数。

## 定义无符号整型

定义无符号整型，需要使用 `unsigned` 关键字。通常来讲，该关键字应该写在类型前：

```cpp
unsigned short us;
unsigned int ui;
unsigned long ul;
unsigned long long ull;
```

## 无符号整型的范围

1 字节(8 位)无符号整型的范围为 0 和 255，而 1 字节有符号整型的范围是 -128 到 127。它们都能够存放 256 个不同的值，但是有符号整型的一半范围被用来表示负数，而无符号整型则全部用来存放整数，因此能够表示的正数范围是有符号整型的两倍。

下表展示了不同有符号整型可以表示的范围：

|大小/类型    |范围|
|---|---|
|1 byte unsigned    |0 到 255
|2 byte unsigned    |0 到 65,535
|4 byte unsigned    |0 到 4,294,967,295
|8 byte unsigned    |0 到 18,446,744,073,709,551,615

一个 n 为的无符号整型可表示的范围为 0 到 $(2^n)-1$。

如果不需要表示负数，那么无符号整型更适合在内存比较小的网络和系统上使用，因为它可以在不占用更多内存的前提下保存更多的正整数。

## 分清有符号(signed) 和无符号(unsigned)

很多新手程序员经常把有符号(signed) 和无符号(unsigned)这两个术语搞混。以下介绍一种简单的方法来记忆它们的不同：为了区分负数和正数，我们需要负号。如果没有指定符号，我们假定该数为正数。这样一来，具有符号的整型（有符号整型）可以区分正负。而不具有符号的整型（有符号整型）则全部为正数。

## 无符号整型溢出

What happens if we try to store the number 280 (which requires 9 bits to represent) in a 1-byte (8-bit) unsigned integer? The answer is overflow.

如果我们尝试把 280 （需要 9 个位来表示）赋值给一个 8 位无符号整型会发生什么呢？答案是——溢出。

!!! info "作者注"

    奇怪的是，C++标准中明确写道，无符号操作数参与的运算永远不会溢出。这和我们的编程常识（无符号和有符号整型都会溢出）是矛盾的。( [参考文献](https://en.wikipedia.org/wiki/Integer_overflow#Definition_variations_and_ambiguity) )。由于大多数的程序员都会考虑该溢出，我们这里也沿用了该说法，尽管和C++标准有所矛盾。

当无符号值超过范围时，它会被除以该类型的最大值加 1，且只保留余数。

280 不在 0 到 255 的范围内。该范围的最大值+1 是 256，所以 280 会除以 256，余数为 24。最终 24 会被存放到内存中。

这个问题还可以这样理解。比最大值还大的数在存放时会被“wraps around” (有时候也称为“modulo wrapping”)，即从 0 开始重新计算。255 在 1 字节整型的表示范围内，所以 255 没问题。而 256 则超过了范围，因此它会被归为 0。257 则会归为 1。280 则归为 24.

这里是一个 2 字节无符号短整型（ `unsigned short`） 的例子：

```cpp
#include <iostream>

int main()
{
    unsigned short x{ 65535 }; // largest 16-bit unsigned value possible
    std::cout << "x was: " << x << '\n';

    x = 65536; // 65536 is out of our range, so we get wrap-around
    std::cout << "x is now: " << x << '\n';

    x = 65537; // 65537 is out of our range, so we get wrap-around
    std::cout << "x is now: " << x << '\n';

    return 0;
}
```

!!! note "译者注"

    无符号不会溢出，的确是 C++标准，不溢出这个说法也是合理的，因为无符号在遇到超过表示范围的情况时并不会表现出未定义行为，结果也不是简单的丢弃，而是对原值进行求模运算。英文叫 warp around，即从 0 开始重新计算。例如，对于 32 位无符号，超出范围时结果等于 `value mod 2^32`

你觉得程序的结果会是什么呢？

(注意，如果你编译上述程序的话，编译器可能会产生关于溢出或截断的告警——你需要关闭”将告警看做错误“功能，才能运行该程序）

```
x was: 65535
x is now: 0
x is now: 1
```

向另一个方向 wrap 也是可以的，0 可以被 2 字节大小的无符号整型表示，但是-1 不可以，因此它会被 wrap 到最大值，结果为 65535。-2 的结果是 65534。

```cpp
#include <iostream>

int main()
{
    unsigned short x{ 0 }; // smallest 2-byte unsigned value possible
    std::cout << "x was: " << x << '\n';

    x = -1; // -1 is out of our range, so we get wrap-around
    std::cout << "x is now: " << x << '\n';

    x = -2; // -2 is out of our range, so we get wrap-around
    std::cout << "x is now: " << x << '\n';

    return 0;
}
```

```
x was: 0
x is now: 65535
x is now: 65534
```

上述代码会引起某些编译器产生告警，因为编译器检测到整型字面量超过了表示它的类型的范围。如果你希望编译上述代码，请暂时关闭”将告警看做错误“功能。

!!! cite "题外话"

    很多电子游戏史上的著名的 bug 都和无符号数反转有关。街机游戏《大金刚》中，你不可能通过22关，因为溢出bug导致用户在这一关中不可能有足够的时间。
    
    电脑游戏《文明》中，甘地通常是第一个使用核武器的，这和他本应该有的保守的属性非常矛盾。玩家认为这是由于甘地的攻击性被初始化成1，但是如果他走民主路线的话，其攻击性会-2。进而导致了数值被反转成了255，使其变得非常好斗。不过，最近席德梅尔对此进行了澄清，他说这并不是引起上述问题的原因。

## 有关无符号整型的争论

很多程序员 (以及一些大厂，例如谷歌）认为程序也应该避免使用无符号整型。

这主要是因为无符号整型有两种行为可能会导致问题。

首先，将两个无符号整型相减，例如3-5等于-2，但是-2不能被无符号整型表示。

```cpp
#include <iostream>

int main()
{
    unsigned int x{ 3 };
    unsigned int y{ 5 };

    std::cout << x - y << '\n';
    return 0;
}
```

在笔者的机器上，尽管上面的代码看上去人畜无害，实际上结果却令人吃惊：

```cpp
4294967294
```

这是因为 -2 被反转到了一个接近 4自己整型最大值的数。另外一个可能产生反转的情况是，对无符号整型使用递减运算符(`--`)。那么当变量循环递减时，最终可能会发生于上述例子中同样的问题。
.
此外，当将有符号整型和无符号整型混用时，也可能产生意外。在C++进行数学运算时（例如算数或比较），如果两个操作数分别为有符号和无符号整型，那么有符号的一方会被转换为无符号。然后，因为无符号整型不可以保存负数，这就有可能造成数据丢失的情况。

考虑下面代码：

```cpp
#include <iostream>

int main()
{
    signed int s { -1 };
    unsigned int u { 1 };

    if (s < u) // -1 被隐式转换成了 4294967295, 而 4294967295 < 1 是错误的
        std::cout << "-1 is less than 1\n";
    else
        std::cout << "1 is less than -1\n"; // 这句语句会被执行

    return 0;
}
```

这个程序可以正常编译，看上去也符合逻辑。但是它打印的结果却是错误的。尽管编译器在检查到有符号/无符号不匹配时，会发出一个警告。但是由于还有很多其他情况下编译器也会报告完全一样的警告（例如两个数都是正数时），这会让我们很难判断真正的问题在哪里。

!!! info "相关内容"

    我们会在[[4-10-Introduction-to-if-statements|4.10 - if 语句]]中介绍`if`语句。

此外，还有一些可能有问题的情况，是根本无法被检查到的。考虑下面这个例子：

```cpp
void doSomething(unsigned int x)
{
    // Run some code x times

    std::cout << "x is " << x << '\n';
}

int main()
{
    doSomething(-1);

    return 0;
}
```

`doSomething()`函数的作者期望用户只传递正数给该函数。但是调用者犯了一个错误，它传了-1。这是个很显然的错误，但是人们仍然难免会犯。那么此时会发生什么呢？

[[arguments|实参]] -1 被隐式转换成了一个无符号[[parameters|形参]]。-1 不在无符号整型的表示范围，因此必然被反转成一个非常大的数(可能是 4294967295)。然后你的程序就会出现问题。 更糟糕的是，并没有一种方法能够避免上述问题的发生。C++ 可以自由地转换有符号和无符号数，但是它并不会在转换时进行任何的范围检查以确保不发生溢出。

上述这些问题都是经常会遇到的，它们产生意料之外的行为，并且难以被发现。即使使用一些被专用于查找此类问题的工具有很难发现。

因此，一些有争议的最佳实践认为，我们应该避免使用无符号类型，除非确实有特殊的需求。

!!! success "最佳实践"

    尽量使用有符号数来存放数量值（尽管数量可能都是非负的）和进行数学运算。避免混用有符号和无符号类型。
    

!!! info "相关内容"

    支持上述建议的一些材料(同时也驳斥了一些常见的反对意见）：

    1.  [Interactive C++ panel](https://www.youtube.com/watch?v=_nrly6PH6NU) (see 12:12-13:08,42:40-45:26, and 1:02:50-1:03:15)
    2.  [Subscripts and sizes should be signed](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1428r0.pdf)
    3.  [Unsigned integers from the libtorrent blog](https://blog.libtorrent.org/2016/05/unsigned-integers/)

## 那么什么时候才使用无符号数呢？

还是有一些场合是需要或者可以使用无符号数的。

首先，无符号数在进行位运算时是推荐使用的（在章节O中介绍）。在一些确实需要反转行为的场合，无符号数也是有用的（例如一些加密算法和随机数生成）。

其次，在有些时候无符号数是无法避免的（很多与数组进行索引有关）。我们会在有关数组的课程中进行详细介绍。在这些情况下，无符号数可以被转换为有符号数。

!!! info "相关内容"

    我们会在[[4-12-Introduction-to-type-conversion-and-static_cast|4.12 - 类型转换和 static_cast]]中介绍如何将无符号数转换为有符号数。
    
同时请注意，如果你在开发嵌入式系统（例如 Arduino）或者其他处理器/内存比较吃紧的环境，出于性能的原因而使用无符号数是非常常见的，同时也是可以被接受的（在有些场景下也是无法避免的）。