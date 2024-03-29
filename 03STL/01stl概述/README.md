> ## STL概述

##### 1. C++ STL是什么，有什么用

  STL，英文全称 standard template library，译为标准模板库或者泛型库，其包含有大量的模板类和模板函数，是 C++ 提供的一个基础模板的集合.

  从根本上说，STL 是一些数据结构和算法的高度优化。程序员可以方便的存取数据，不需要关心内部的实现。

##### 2. C++ STL基本组成（6大组件+13个头文件）

  STL 是由容器、算法、迭代器、函数对象、适配器、内存分配器这 6 部分构成，其中后面 4 部分是为前 2 部分服务的.

![image](https://user-images.githubusercontent.com/42632290/170865476-8c8e27ad-aa39-46d2-94d9-7a76f278f26d.png)

![image](https://user-images.githubusercontent.com/42632290/170865458-f88adc77-a793-4deb-a52d-867b21f835c5.png)

##### 3. STL容器是什么

  简单的理解容器，它就是一些模板类的集合，封装的是组织数据的方法（也就是数据结构）。STL 提供有 3 类标准容器，分别是**序列容器、排序容器和哈希容器**，其中后两类容器有时也统称为**关联容器**.

![image](https://user-images.githubusercontent.com/42632290/170865667-dc74dd33-f0cb-4367-87e8-a46b3e8ba558.png)

  以上 3 类容器的存储方式完全不同，因此使用不同容器完成相同操作的效率也大不相同。所以在实际使用时，要善于根据想实现的功能，选择合适的容器.

##### 4. STL迭代器

  迭代器可以指向容器中的某个元素，还可以对该元素进行读/写操作。除了要具有对容器进行遍历读写数据的能力之外，还要能对外隐藏容器的内部差异，从而以统一的界面向算法传送数据.

  常用的迭代器按功能强弱分为**输入迭代器、输出迭代器、前向迭代器、双向迭代器、随机访问迭代器** 5 种。[输入迭代器和输出迭代器比较特殊，它们不是把数组或容器当做操作对象，而是把输入流/输出流作为操作对象。]

```
  1) 前向迭代器（forward iterator）假设 p 是一个前向迭代器，则 p 支持 ++p，p++，*p 操作，还可以被复制或赋值，可以用 == 和 != 运算符进行比较。此外，两个迭代器可以互相赋值。

  2) 双向迭代器（bidirectional iterator）双向迭代器具有正向迭代器的全部功能. 假设 p 是一个双向迭代器，则还可以进行 --p 或者 p-- 操作（即一次向后移动一个位置）。

  3) 随机访问迭代器（random access iterator）随机访问迭代器具有双向迭代器的全部功能。假设 p 是一个随机访问迭代器，i 是一个整型变量或常量，则 p 还支持以下操作：

     p+=i：使得 p 往后移动 i 个元素
     p-=i：使得 p 往前移动 i 个元素
     p+i：返回 p 后面第 i 个元素的迭代器
     p-i：返回 p 前面第 i 个元素的迭代器
     p[i]：返回 p 后面第 i 个元素的引用
     
此外，两个随机访问迭代器 p1、p2 还可以用 <、>、<=、>= 运算符进行比较。另外，表达式 p2-p1 也是有定义的，其返回值表示 p2 所指向元素和 p1 所指向元素的序号之差
```

![image](https://user-images.githubusercontent.com/42632290/170871228-d1dce6ff-38e9-45ae-b56c-554d00fb15f9.png)

尽管不同容器对应着不同类别的迭代器，但这些迭代器有着较为统一的定义方式，具体分为 4 种:

![image](https://user-images.githubusercontent.com/42632290/170871268-431557b5-989b-4c44-a90a-35d8399ce1c8.png)

```
    a) "*迭代器名" 就表示迭代器指向的元素。其中，
    b) 常量迭代器和非常量迭代器的分别在于: 通过非常量迭代器还能修改其指向的元素。
    c) 反向迭代器和正向迭代器的区别在于：
       对正向迭代器进行 ++ 操作时，迭代器会指向容器中的后一个元素
       对反向迭代器进行 ++ 操作时，迭代器会指向容器中的前一个元素
```

