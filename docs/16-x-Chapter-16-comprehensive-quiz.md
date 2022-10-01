---
title: 16.x - 小结与测试
alias: 16.x - 小结与测试
origin: /chapter-16-comprehensive-quiz/
origin_title: "16.x — Chapter 16 comprehensive quiz"
time: 2022-8-12
type: translation
tags:
- summary
---

## 复习

使用简单对象构建复杂对象的过程，称为**对象组合**。对象组合有两种：[[Composition|组合]]和[[aggregation|聚合]]。

当一个类的类成员为该类的**一部分**（part-of）时，就产生了组合关系。在组合关系中，类负责管理成员是否存在。一个合格的组合关系应该满足如下条件：

-   部分(成员) 是对象（类）的一部分；
-   部分(成员) 同时只能属于一个对象（类）;
-   部分(成员) 的存在与否受到对象（类）的管理；
-   部分(成员) 并不知道对象（类）是否存在。

通常情况下，组合关系可以使用一般的成员变量来实现，或者也可以使用指针，但其表示的内存的申请和释放必须完全由该类来管理。如果你能够将一个类实现为组合关系，那你就应该尽量这么做。

当某个类**具有（has）** 某个成员是，聚合关系就出现了。在聚合关系中，类并不负责管理该成员的存在性。一个合格的聚合关系应该满足如下条件：

- 部分(成员) 是对象（类）的一部分；
- 部分(成员) 不仅仅属于一个对象（类）;
- 部分(成员) 的存在与否不会受到对象（类）的管理；
- 部分(成员) 并不知道对象（类）是否存在。

聚合关系通常可以通过指针和引用来实现。

[[Associations|关联]]是一种相对更加松散的关系。类使用了一个与它并无直接关系的对象。只有满足如下条件，才能将两个对象称为关联关系：

-   关联对象（成员）与该对象（类）本不该相关；
-   关联对象（成员）可以同时属于多个对象（类）；
-   关联对象（成员）存在与否不会受到对象（类）的管理；
-   关联对象（成员）对对象（或类）的存在，可能知晓也可能不知晓。

关联关系可以通过指针或引用来实现，也可以通过更加间接的方式实现（例如通过关联对象的对应键或索引来使用它）。

当一个类使用另一个类完成某项任务时，它们之间属于[[dependency|依赖]]关系。被依赖的对象通常并不是使用它的对象的一部分。被使用的对象通常是被临时创建、使用然后销毁，或者是通过成员函数传入并使用的外部资源。

[[container-class|容器类(container class)]]指的是为其他一些类提供容器来存储它们的类。 **值容器**和它保存的数据副本属于组合关系。**引用容器**是一种聚合关系，它保存了容器外部对象的指针或引用。


`std::initializer_list` 可以用来实现构造函数、赋值操作符和其他接列表初始化参数的函数。 `std::initailizer_list` 位于`<initializer_list>` 头文件中。


|属性类型|组合	|聚合	|关联	|依赖|
|:----:|:----:|:----:|:----:|:----:|
|关系类型	|整体/部分	|整体/部分|	本不相关|	本不相关
|成员是否可以属于多个类|	No	|Yes	|Yes	|Yes
|成员存在与否是否受类管理|Yes	|No	|No	|No
|方向	|单向	|单向	|单向或双向|	单向或双向
|关系动词|Part-of	|Has-a	|Uses-a|	Depends-on