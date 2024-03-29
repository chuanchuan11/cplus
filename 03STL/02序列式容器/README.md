> ## 序列式容器

##### 0. 概述

&emsp;&emsp;所谓序列容器，即以线性排列（类似普通数组的存储方式）来存储某一指定类型的数据，该类容器并不会自动对存储的元素按照值的大小进行排序

```
array<T,N>（数组容器）：容器一旦建立，其长度就是固定不变的，这意味着不能增加或删除元素，只能改变某个元素的值；

vector<T>（向量容器）：长度可变的序列容器，即在存储空间不足时，会自动申请更多的内存。此容器，在尾部增加或删除元素的效率最高（时间复杂度为 O(1) 常数阶），在其它位置插入或删除元素效率较差（时间复杂度为 O(n) 线性阶，其中 n 为容器中元素的个数）；

deque<T>（双端队列容器）：和 vector 非常相似，区别在于使用该容器不仅尾部插入和删除元素高效，在头部插入或删除元素也同样高效，时间复杂度都是 O(1) 常数阶，但是在容器中某一位置处插入或删除元素，时间复杂度为 O(n) 线性阶；

list<T>（链表容器）：是一个长度可变的序列，它以双向链表的形式组织元素，在这个序列的任何地方都可以高效地增加或删除元素（时间复杂度都为常数阶 O(1)），但访问容器中任意元素的速度要比前三种容器慢，这是因为 list<T> 必须从第一个元素或最后一个元素开始访问，需要沿着链表移动，直到到达想要的元素。

forward_list<T>（正向链表容器）：和 list 容器类似，只不过它以单链表的形式组织元素，它内部的元素只能从第一个元素开始访问，是一类比链表容器快、更节省内存的容器。

注意：stack<T> 和 queue<T> 本质上也属于序列容器，只不过它们都是在 deque 容器的基础上改头换面而成，通常更习惯称它们为容器适配器，后续介绍

```

![image](https://user-images.githubusercontent.com/42632290/171986585-439f233f-0f3b-47c3-88a6-9a1933954db8.png)

##### 1. array容器

(0) 概述
    
&emsp;&emsp; array 容器是 C++ 11 标准中新增的序列容器，简单地理解，它就是在 C++ 普通数组的基础上，添加了一些成员函数和全局函数。在使用上，它比普通数组更安全（原因后续会讲），且效率并没有因此变差。

&emsp;&emsp; array 容器的大小是固定的，无法动态的扩展或收缩，这也就意味着，在使用过程中无法通过增加或移除元素而改变其大小，**只允许访问或者替换存储的元素**。

```
1. 头文件  <array> 

    #include <array>
    using namespace std;

2. array 容器以类模板的形式定义

   namespace std{
        template <typename T, size_t N>
        class array;
   }

   注意：array<T,N> 类模板中，T 用于指明容器中的存储的具体数据类型，N 用于指明容器的大小. 这里的 N 必须是常量，不能用变量表示

```

(1) 创建

```
a) 直接创建
    std::array<double, 10> values;    //各个元素的值是不确定的，array 容器不会做默认初始化操作
    
b) 创建并使用默认初始化为0    
    std::array<double, 10> values {}; //所有的元素被初始化为 0.0
    
c) 创建并自定义初始化值  
    std::array<double, 10> values {0.5,1.0,1.5,,2.0};  //只初始化前 4 个元素，剩余的元素都会被初始化为 0.0
    
```

(2) 常见成员函数

![image](https://user-images.githubusercontent.com/42632290/177032011-4e8646ef-b25f-46c5-9cf7-ee4277a0b6c9.png)

    另外，在 <array> 头文件中还重载了 get() 全局函数，该重载函数的功能是访问容器中指定的元素，并返回该元素的引用。

    **正是由于 array 容器中包含了 at() 这样的成员函数，使得操作元素时比普通数组更安全**



(3) 迭代器使用详解

1. 遍历

```
#include <iostream>
#include <array>
using namespace std;

int main()
{
    std::array<int, 4> values{};  //初始化 values 容器为 {0,1,2,3}
    
    for (int i = 0; i < values.size(); i++) {
        values.at(i) = i;  //使用at赋值
    }
    
    cout << get<3>(values) << endl;  //使用 get() 重载函数输出指定位置元素
    
    if (!values.empty()) {   //如果容器不为空，则输出容器中所有的元素
        for (auto val = values.begin(); val < values.end(); val++) 
        {
            cout << *val << " ";
        }
    }
}

输出：
    3
    0 1 2 3

```       

2. 增

```
  不支持      
```       

3. 删

```
  不支持          
```         
             
 4. 改

```
a) at 函数
b) []
c) data 函数
```              
  
 5. 查

```
a) []方法：该方式没有边界检查，即使越界访问，也不会被检测到

    values[4] = values[3] + 2.O*values[1];
    
b) at() 成员函数访问元素：索引越界时，程序会抛出 std::out_of_range 异常，更安全，推荐使用

    values.at (4) = values.at(3) + 2.O*values.at(1);

c) get<n> 模板函数: 参数实参必须是在编译时可以确定的常量表达式，所以它不能是一个循环变量

    #include <iostream>
    #include <array>
    #include <string>
    using namespace std;
    int main()
    {
        array<string, 5> words{ "one","two","three","four","five" };
        cout << get<3>(words) << endl;        // 输出：four
        //cout << get<6>(words) << std::endl; // 越界，会发生编译错误
        return 0;
    }
    
d)  data() 成员函数: 得到指向容器首个元素的指针

    #include <iostream>
    #include <array>
    using namespace std;
    int main()
    {
        array<int, 5> words{1,2,3,4,5};
        cout << *( words.data()+1);    //通过该指针，可以获得容器中的各个元素
        return 0;
    }

```  

(4) array相对于数组的提升

&emsp;&emsp; array 容器代替普通数组，最直接的好处就是 array 模板类中已经为我们写好了很多实用的方法，可以大大提高我们编码效率。例如:
  
&emsp;&emsp;&emsp;&emsp; 1) array 容器提供的 at() 成员函数，可以有效防止越界操纵数组的情况
  
&emsp;&emsp;&emsp;&emsp; 2) fill() 函数可以实现数组的快速初始化
  
&emsp;&emsp;&emsp;&emsp; 3) swap() 函数可以轻松实现两个相同数组内容交换（类型相同，大小相同）

&emsp;&emsp;&emsp;&emsp; 4) 两个 array 容器满足大小相同并且保存元素的类型相同时，两个 array 容器可以直接直接做赋值操作

&emsp;&emsp;&emsp;&emsp; 5) 可以用任何比较运算符直接比较两个 array 容器

例子：

```
1) 实现两个数组内容交换

    array<char, 50>addr1{"http://www.cdsy.xyz"};
    array<char, 50>addr2{ "http://www.cdsy.xyz/computer/programme/stl/" };
    addr1.swap(addr2);
    printf("addr1 is：%s\n", addr1.data());
    printf("addr2 is：%s\n", addr2.data());

    输出：
         addr1 is：http://www.cdsy.xyz/computer/programme/stl/
         addr2 is：http://www.cdsy.xyz

2) 两个数组之间赋值

    array<char, 50>addr1{ "http://www.cdsy.xyz" };
    array<char, 50>addr2{ "http://www.cdsy.xyz/computer/programme/stl/" };
    addr1 = addr2;
    
3) 两个数组之间比较

    array<char, 50>addr1{ "http://www.cdsy.xyz" };
    array<char, 50>addr2{ "http://www.cdsy.xyz/computer/programme/stl/" };
    if (addr1 == addr2) {
        std::cout << "addr1 == addr2" << std::endl;
    }
    if (addr1 < addr2) {
        std::cout << "addr1 < addr2" << std::endl;
    }
    if (addr1 > addr2) {
        std::cout << "addr1 > addr2" << std::endl;
    }

```

##### 2. vector容器

(0) 概述

&emsp;&emsp; vector 容器是 STL 中最常用的容器之一，它和 array 容器非常类似，都可以看做是对 C++ 普通数组的“升级版”。不同之处在于，array 实现的是静态数组（容量固定的数组），而 vector 实现的是一个**动态数组，即可以进行元素的插入和删除，在此过程中，vector 会动态调整所占用的内存空间，整个过程无需人工干预**。

&emsp;&emsp; vector 常被称为向量容器，因为该容器擅长在尾部插入或删除元素，效率高；而对于在容器头部或者中部插入或删除元素，效率较低（移动元素需要耗费时间）。

```
1. 头文件
   #include <vector>
   using namespace std;
```

(1) 创建

```
a) 创建空容器
    std::vector<double> values;  //空的 vector 容器，没有元素，所以没有为其分配空间。当添加第一个元素时，会自动分配内存。

b) 指定初始值及元素个数
    std::vector<int> primes {2, 3, 5, 7, 11, 13, 17, 19};  //含有 8 个素数的 vector 容器
    
    std::vector<double> values(20);      //含有 20 个元素，默认初始值都为 0

    std::vector<double> values(20, 1.0); //含有 20 个元素，值都是 1.0

c) 使用已有容器初始化新容器
    std::vector<char>value1(5, 'c');
    std::vector<char>value2(value1);  //value2 容器中也具有 5 个字符 'c'

d) 指定初始值的范围
    int array[]={1,2,3};
    std::vector<int>values(array, array+2);//values 将保存{1,2}
    
    std::vector<int>value1{1,2,3,4,5};
    std::vector<int>value2(std::begin(value1),std::begin(value1)+3);//value2保存{1,2,3}

```

(2) 常见成员函数

![image](https://user-images.githubusercontent.com/42632290/178138057-7e64b928-1154-4a9a-a2a7-561ffa7f19c1.png)
![image](https://user-images.githubusercontent.com/42632290/178138157-1578d909-5c90-44be-9e23-29ad6937e4fd.png)

(3) 迭代器使用详解

1. 遍历

```
a) 遍历容器所有元素

#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<char>value;    //初始化一个空vector容量
    
    value.push_back('S'); //向value容器中的尾部依次添加 S、T、L 字符
    value.push_back('T');
    value.push_back('L');
    
    printf("元素个数为：%d\n", value.size());             //调用 size() 成员函数容器中的元素个数
    
    for (auto i = value.begin(); i < value.end(); i++)   //使用迭代器遍历容器
    {  
        cout << *i << " ";
    }
    cout << endl;
    
    value.insert(value.begin(), 'C');   //向容器开头插入字符
    cout << "首个元素为：" << value.at(0) << endl;
    return 0;
}  

b) 当容器容量增加时，地址可能会发生改变，这会导致之前创建的迭代器失效

#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3};
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    auto first = values.begin();
    auto end = values.end();
    
    //增加 values 的容量
    values.reserve(20);
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    while (first != end) 
    {
        cout << *first;    //增加容量之后，首个元素的存储地址发生了改变，此时再使用先前创建的迭代器，显然是错误的
        ++first;
    }
    return 0;
}

  输出：
       values 容器首个元素的地址：0096DFE8
       values 容器首个元素的地址：00965560

```       

2. 增

```
a) push_back()函数: 先创建这个元素，然后再将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）
b) emplace_back()函数:直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程

#include <vector> 
#include <iostream> 
using namespace std;
class testDemo
{
public:
    testDemo(int num):num(num)
    {
        std::cout << "调用构造函数" << endl;
    }
    testDemo(const testDemo& other) :num(other.num) 
    {
        std::cout << "调用拷贝构造函数" << endl;
    }
    testDemo(testDemo&& other) :num(other.num) 
    {
        std::cout << "调用移动构造函数" << endl;
    }
private:
    int num;
};

int main()
{
    cout << "emplace_back:" << endl;
    std::vector<testDemo> demo1;
    demo1.emplace_back(2);  

    cout << "push_back:" << endl;
    std::vector<testDemo> demo2;
    demo2.push_back(2);
}

   输出：emplace_back:
            调用构造函数
         push_back:
            调用构造函数
            调用移动构造函数
   注意：在实际使用时，优先选用 emplace_back(), 由于emplace_back() 是 C++ 11 标准新增加的，如果程序要兼顾之前的版本，还是应该使用 push_back()

c) insert()函数：指定位置插入一个或多个元素
   iterator insert(pos,elem);             //在迭代器 pos 指定的位置之前插入一个新元素elem，并返回表示新插入元素位置的迭代器
   iterator insert(pos,n,elem);           //在迭代器 pos 指定的位置之前插入 n 个元素 elem，并返回表示第一个新插入元素位置的迭代器
   iterator insert(pos,first,last);       //在迭代器 pos 指定的位置之前，插入其他容器位于 [first,last) 区域的所有元素，并返回表示第一个新插入元素位置的迭代器
   iterator insert(pos,initlist);         //在迭代器 pos 指定的位置之前，插入初始化列表（用大括号{}括起来的多个元素，中间有逗号隔开）中所有的元素，并返回表示第一个新插入元素位置的迭代器
   
#include <iostream> 
#include <vector> 
#include <array> 
using namespace std;
int main()
{
    std::vector<int> demo{1,2};
    //第一种格式用法
    demo.insert(demo.begin() + 1, 3);//{1,3,2}

    //第二种格式用法
    demo.insert(demo.end(), 2, 5);//{1,3,2,5,5}

    //第三种格式用法
    std::array<int,3>test{ 7,8,9 };
    demo.insert(demo.end(), test.begin(), test.end());//{1,3,2,5,5,7,8,9}

    //第四种格式用法
    demo.insert(demo.end(), { 10,11 });//{1,3,2,5,5,7,8,9,10,11}

    for (int i = 0; i < demo.size(); i++) {
        cout << demo[i] << " ";
    }
    return 0;
}

d) emplace()函数：emplace() 每次只能插入一个元素，而不是多个
   iterator emplace (const_iterator pos, args...);  //args...，即被插入元素的构造函数需要多少个参数，那么在 emplace() 的第一个参数的后面，就需要传入相应数量的参数

#include <vector>
#include <iostream>
using namespace std;

int main()
{
    std::vector<int> demo1{1,2};
    //emplace() 每次只能插入一个 int 类型元素
    demo1.emplace(demo1.begin(), 3);
    for (int i = 0; i < demo1.size(); i++) {
        cout << demo1[i] << " ";
    }
    return 0;
}

    注意： emplace() 在插入元素时，是在容器的指定位置直接构造元素，而不是先单独生成，再将其复制（或移动）到容器中。因此，推荐优先使用 emplace()。

```       

3. 删

```
a) pop_back(): 删除 vector 容器中最后一个元素，该容器的大小（size）会减 1，但容量（capacity）不会发生改变
b) erase(pos): 删除 vector 容器中 pos 迭代器指定位置处的元素，并返回指向被删除元素下一个位置元素的迭代器。该容器的大小（size）会减 1，但容量（capacity）不会发生改变
c) swap(end)、pop_back(): 先调用 swap() 函数交换要删除的目标元素和容器最后一个元素的位置，然后使用 pop_back() 删除该目标元素
d) erase(beg,end): 删除 vector 容器中位于迭代器 [beg,end)指定区域内的所有元素，并返回指向被删除区域下一个位置元素的迭代器。该容器的大小（size）会减小，但容量（capacity）不会发生改变。
e) remove(): 删除容器中所有和指定元素值相等的元素，并返回指向最后一个元素下一个位置的迭代器。值得一提的是，调用该函数不会改变容器的大小和容量
f) clear(): 删除 vector 容器中所有的元素，使其变成空的 vector 容器。该函数会改变 vector 的大小（变为 0），但不是改变其容量

示例c:

#include <vector>
#include <iostream>
#include <algorithm>  //swap在头文件 <algorithm> 和 <utility> 中都有定义，使用时引入其中一个即可
using namespace std;

int main()
{
    vector<int>demo{ 1,2,3,4,5 };
    
    swap(*(std::begin(demo)+1),*(std::end(demo)-1));//等同于 swap(demo[1],demo[4]) //交换要删除元素和最后一个元素的位置
   
    //交换位置后的demo容器
    for (int i = 0; i < demo.size(); i++) {
        cout << demo[i] << " ";
    }
    demo.pop_back();
    cout << endl << "size is :" << demo.size() << endl;
    cout << "capacity is :" << demo.capacity() << endl;
    
    //输出demo 容器中剩余的元素
    for (int i = 0; i < demo.size(); i++) {
        cout << demo[i] << " ";
    }
    return 0;
}
  运行结果：
           1 5 3 4 2
           size is :4
           capacity is :5
           1 5 3 4

示例e:
#include <vector>
#include <iostream>
#include <algorithm>  //remove函数，定义在 <algorithm> 头文件中
using namespace std;

int main()
{
    vector<int>demo{ 1,3,3,4,3,5 };
    
    auto iter = std::remove(demo.begin(), demo.end(), 3);  //交换要删除元素和最后一个元素的位置

    cout << "size is :" << demo.size() << endl;
    cout << "capacity is :" << demo.capacity() << endl;
    //输出剩余的元素
    for (auto first = demo.begin(); first < iter;++first) {
        cout << *first << " ";
    }
    return 0;
}

输出：
    size is :6
    capacity is :6
    1 4 5

注意：
    在对容器执行完 remove 函数之后，由于并没有改变容器原来的大小和容量，因此无法使用之前的方法遍历容器，而是需要向程序中那样，借助 remove返回的迭代器完成正确的遍历

原理：
    remove() 的实现原理是，在遍历容器中的元素时，一旦遇到目标元素，就做上标记，然后继续遍历，直到找到一个非目标元素，即用此元素将最先做标记的位置覆盖掉，同时将此非目标元素所在的位置也做上标记，等待找到新的非目标元素将其覆盖。因此，如果将上面程序中 demo 容器的元素全部输出，得到的结果为 1 4 5 4 3 5

    另外还可以看到，既然通过 remove() 函数删除掉 demo 容器中的多个指定元素，该容器的大小和容量都没有改变，其剩余位置还保留了之前存储的元素。我们可以使用 erase() 成员函数删掉这些 "无用" 的元素

#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    vector<int>demo{ 1,3,3,4,3,5 };
    //交换要删除元素和最后一个元素的位置
    auto iter = std::remove(demo.begin(), demo.end(), 3);
    
    demo.erase(iter, demo.end());  // remove()用于删除容器中指定元素时，常和 erase() 成员函数搭配使用
    
    cout << "size is :" << demo.size() << endl;
    cout << "capacity is :" << demo.capacity() << endl;
    //输出剩余的元素
    for (int i = 0; i < demo.size();i++) {
        cout << demo[i] << " ";
    }
    return 0;
}

输出：size is :3
      capacity is :6
      1 4 5

```         
             
 4. 改

```
a) []：进行访问与修改元素，不会检查越界访问
 
   vector<int> values{1,2,3,4,5};
   cout << values[0] << endl;            //获取容器中首个元素：1
   
   values[0] = values[1] + values[2] + values[3] + values[4];   //修改容器中下标为 0 的元素的值
   cout << values[0] << endl;            // 输出：14
 
 b) at() 成员函数: 越界时，会抛出std::out_of_range异常，提高安全性
 
   vector<int> values{1,2,3,4,5};
    
   cout << values.at(0) << endl;    //获取容器中首个元素：1
    
   values.at(0) = values.at(1) + values.at(2) + values.at(3) + values.at(4);   //修改容器中下标为 0 的元素的值
   cout << values.at(0) << endl;    //输出：14

   //cout << values.at(5) << endl;  //这条语句会发生 out_of_range 异常
 
c)  front() 和 back(): 修改首尾元素

   vector<int> values{1,2,3,4,5};
   cout << "values 首元素为：" << values.front() << endl;   //输出：1
   cout << "values 尾元素为：" << values.back() << endl;    //输出：5

   values.front() = 10;      //修改首元素
   cout <<"values 新的首元素为：" << values.front() << endl;  //输出：10
   
   values.back() = 20;       //修改尾元素
   cout << "values 新的尾元素为：" << values.back() << endl;  //输出：20

d) data() 成员函数: 通过指针修改
   vector<int> values{1,2,3,4,5};
   cout << *(values.data() + 2) << endl;  //输出容器中第 3 个元素的值：3
   
   //修改容器中第 2 个元素的值
   *(values.data() + 1) = 10;    
   cout << *(values.data() + 1) << endl;  //输出：10

```              
  
 5. 查

```

```  

(4) 高级使用及特别注意事项

1. 如何避免vector容器进行不必要的扩容

&emsp;&emsp; vector 容器看做是一个动态数组, 只要**有新元素要添加到 vector 容器中而恰好此时 vector 容器的容量不足时，该容器就会自动扩容**, vector 容器扩容大致分为以下 4 个步骤：
    
&emsp;&emsp;&emsp;&emsp; a) 分配一块大小是当前 vector 容量几倍的新存储空间。注意，多数 STL 版本中的 vector 容器，其容器都会以 2 的倍数增长
    
&emsp;&emsp;&emsp;&emsp; b) 将 vector 容器存储的所有元素，依照原有次序从旧的存储空间复制到新的存储空间中；
    
&emsp;&emsp;&emsp;&emsp; c) 析构掉旧存储空间中存储的所有元素；
    
&emsp;&emsp;&emsp;&emsp; d) 释放旧的存储空间。

&emsp;&emsp; vector 容器的扩容过程是非常耗时的，并且当容器进行扩容后，之前和该容器相关的所有指针、迭代器以及引用都会失效。因此在使用 vector 容器过程中，我们应尽量避免执行不必要的扩容操作, reserve()函数可以实现该目标 

```
几种方法区分:
  a) size(): 当前 vector 容器中已经存有多少个元素
  
  b) capacity(): 当前 vector 容器总共可以容纳多少个元素。如果想知道当前 vector 容器有多少未被使用的存储空间，可以通过 capacity()-size() 得知
  
  c) resize(n): 强制 vector 容器必须存储 n 个元素, 如果 n 比 size() 小，则容器尾部多出的元素将会被删除；如果 n 比 size() 大，则 vector 会借助默认构造函数创建出更多的默认值元素，并将它们存储到容器末尾；如果 n 比 capacity() 的返回值还要大，则 vector 会先扩增，在添加一些默认值元素
  
  d) reserve(n): 强制 vector 容器的容量至少为 n。注意，如果 n 比当前 vector 容器的容量小，则该方法什么也不会做；反之如果 n 比当前 vector 容器的容量大，则 vector 容器就会扩容


避免 vector 容器执行不必要的扩容操作的关键在于，在使用 vector 容器初期，就要将其容量设为足够大的值:

示例1：
     代码的整个循环过程中，vector 容器会进行 2~10 次自动扩容（多数的 STL 标准库版本中，vector 容器通常会扩容至当前容量的 2 倍，而这里 1000≈2 10），程序的执行效率可想而知
vector<int>myvector;
for (int i = 1; i <= 1000; i++) {
    myvector.push_back(i);
}

优化：
vector<int>myvector;
myvector.reserve(1000);  //仅扩充了 1 次，执行效率大大提高
cout << myvector.capacity();
for (int i = 1; i <= 1000; i++) {
    myvector.push_back(i); 
}

注意：
   我们可能并不知道 vector 容器到底要存储多少个元素。这种情况下，可以先预留出足够大的空间，当所有元素都存储到 vector 容器中之后，再去除多余的容量

```

2. swap()成员方法使用

&emsp;&emsp;vector 模板类中提供了 pop_back()、erase()、clear() 等成员方法，可以轻松实现删除容器中已存储的元素。**但需要注意得是，借助这些成员方法只能删除指定的元素，容器的容量并不会因此而改变**。

```
1) 示例
  myvector.erase(myvector.begin());
  cout << "4、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;

  myvector.pop_back();
  cout << "5、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;

  myvector.clear();
  cout << "6、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;

  输出：
  4、当前 myvector 拥有 9 个元素，容量为 1000
  5、当前 myvector 拥有 8 个元素，容量为 1000
  6、当前 myvector 拥有 0 个元素，容量为 1000

  显然，myvector 容器存储的元素个数在减少，但容量并不会减小

2) shrink_to_fit(): 将当前 vector 容器的容量缩减至和实际存储元素的个数相等
  myvector.shrink_to_fit();
  cout << "7、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;

3) swap()方法去除vector多余容量

    vector<int>myvector;   //手动为 myvector 扩容
    
    myvector.reserve(1000);
    cout << "1、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;
    
    //利用 myvector 容器存储 10 个元素
    for (int i = 1; i <= 10; i++) {
        myvector.push_back(i);
    }

    vector<int>(myvector).swap(myvector);    //将 myvector 容量缩减至 10
    cout << "2、当前 myvector 拥有 " << myvector.size() << " 个元素，容量为 " << myvector.capacity() << endl;

  输出结果：
    1、当前 myvector 拥有 0 个元素，容量为 1000
    2、当前 myvector 拥有 10 个元素，容量为 10

  作用过程：
    1) 先执行 vector<int>(myvector)，此表达式会调用 vector 模板类中的拷贝构造函数，从而创建出一个临时的 vector 容器（后续称其为 tempvector）
    2) 然后借助 swap() 成员方法对 tempvector 临时容器和 myvector 容器进行调换，此过程不仅会交换 2 个容器存储的元素，还会交换它们的容量
    3) 当整条语句执行结束时，临时的 tempvector 容器会被销毁，其占据的存储空间都会被释放。注意，这里释放的其实是原 myvector 容器占用的存储空间

4) 利用swap()方法清空vector容器

    vector<int>().swap(x);  //会调用默认构造函数，生成空的 vector 容器，再借助 swap() 方法将空容器交换给 x，从而达到清空 x 的目的

```

3. 切忌，vector<bool>不是存储bool类型元素的vector容器

&emsp;&emsp;在实际使用中避免使用 vector<bool> 这样的存储结构. vector会以bit方式存储，无法像其他元素那样直接使用[]获取元素，可能导致编译失败，**可以选择使用 deque<bool> 或者 bitset 来替代**
      
参考：
    
&emsp;&emsp;1) http://www.cdsy.xyz/computer/programme/stl/20210307/cd161510779311975.html

##### 3. deque容器

(0) 概述
    
&emsp;&emsp;deque 是 double-ended queue 的缩写，又称双端队列容器.deque 容器和 vecotr 容器有很多相似之处
  
&emsp;&emsp;相似：deque 容器也擅长在序列尾部添加或删除元素，而不擅长在序列中间添加或删除元素 
    
&emsp;&emsp;不同：deque 还擅长在序列头部添加或删除元素。并且更重要的一点是，**deque 容器中存储元素并不能保证所有元素都存储到连续的内存空间中**
  
&emsp;&emsp;**当需要向序列两端频繁的添加或删除元素时，应首选 deque 容器**。
    
```
1）头文件
  #include <deque>
  using namespace std;    
```

(1) 创建

```
1) 创建空 deque 容器：
    
   std::deque<int> d;

2) 创建具有 n 个元素的 deque 容器
    
   std::deque<int> d(10);    //具有 10 个元素（默认都为 0）
    
3) 具有 n 个元素的 deque 容器，并为每个元素都指定初始值
    
   std::deque<int> d(10, 5)  //包含 10 个元素（值都为 5）
    
4) 通过拷贝函数创建一个新的 deque 容器
    
   std::deque<int> d1(5);
   std::deque<int> d2(d1);
    
5) 拷贝其他类型容器中指定区域内的元素（也可以是普通数组），可以创建一个新容器
    
   int a[] = { 1,2,3,4,5 };       
   std::deque<int>d(a, a + 5);  //拷贝普通数组，创建deque容器
  
   std::array<int, 5>arr{ 11,12,13,14,15 };
   std::deque<int>d(arr.begin()+2, arr.end());  //拷贝arr容器中的{13,14,15}

```

(2) 常见成员函数

![image](https://user-images.githubusercontent.com/42632290/180638230-efcedbdf-cb05-4945-a106-e3d239dbb9ff.png)
![image](https://user-images.githubusercontent.com/42632290/180638247-89b27b49-696b-4589-9d77-bb328c26c175.png)

&emsp;&emsp;和 vector 相比，额外增加了实现在容器头部添加和删除元素的成员函数，同时删除了 capacity()、reserve() 和 data() 成员函数

&emsp;&emsp;deque 容器没有提供 data() 成员函数，同时 deque 容器在存储元素时，也无法保证其会将元素存储在连续的内存空间中，因此尝试使用指针去访问 deque 容器中指定位置处的元素，是非常危险的
    
(3) 迭代器使用详解

1. 遍历

```
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d{1,2,3,4,5};
    //从容器首元素，遍历至最后一个元素
    for (auto i = d.begin(); i < d.end(); i++) 
    {
        cout << *i << " ";   //输出结果：1 2 3 4 5
    }
    return 0;
}
                      
1) 需要注意的一点是，迭代器的功能是遍历容器，在遍历的同时可以访问（甚至修改）容器中的元素，但迭代器不能用来初始化空的 deque 容器：

错误用法：
        #include <iostream>
        #include <vector>
        using namespace std;
        int main()
        {
            vector<int>values;
            auto first = values.begin();
            //*first = 1;
            return 0;
        }
对于空的 deque 容器来说，可以通过 push_back()、push_front() 或者 resize() 成员函数实现向（空）deque 容器中添加元素

    
2) 向 deque 容器添加元素时，deque 容器会申请更多的内存空间，同时其包含的所有元素可能会被复制或移动到新的内存地址（原来占用的内存会释放），这会导致之前创建的迭代器失效   
    
#include <iostream>
#include <deque>
using namespace std;
int main()
{
    deque<int>d;
    d.push_back(1);
    auto first = d.begin();
    cout << *first << endl;
    //添加元素，会导致 first 失效
    d.push_back(1);
    cout << *first << endl;  //first 失效,会导致程序运行崩溃
    return 0;
}

```       

2. 增加与删除

![image](https://user-images.githubusercontent.com/42632290/180639889-4310d1f8-0af8-48b3-b02f-b19f7a4e7381.png)

**在实际应用中，常用 emplace()、emplace_front() 和 emplace_back()，效率高**
                        
![image](https://user-images.githubusercontent.com/42632290/180639940-f4f12968-b5ba-4d8c-a789-41a32ef33319.png)


3. 查找与修改

```
1) 采用普通数组访问存储元素的方式: 效率高

   #include <iostream>
   #include <deque>
   using namespace std;
    int main()
    {
        deque<int>d{ 1,2,3,4 };
        cout << d[1] << endl;   //输出：2
        
        d[1] = 5;    //修改指定下标位置处的元素
        cout << d[1] << endl;   //输出: 5
        return 0;
    }
    
2) at(): 成员函数, 有效地避免越界访问
    
    #include <iostream>
    #include <deque>
    using namespace std;
    int main()
    {
        deque<int>d{ 1,2,3,4 };
        cout << d.at(1) << endl;
        d.at(1) = 5;
        cout << d.at(1) << endl;
        
        //cout << d.at(10) << endl;  //会抛出 out_of_range 异常
        return 0;
    } 
    
3) front() 和 back(): 访问（甚至修改）容器中的首尾元素
    
    deque<int> d{ 1,2,3,4,5 };
    cout << "deque 首元素为：" << d.front() << endl;
    cout << "deque 尾元素为：" << d.back() << endl;
    
    //修改首元素
    d.front() = 10;
    cout << "deque 新的首元素为：" << d.front() << endl;
    
    //修改尾元素
    d.back() = 20;
    cout << "deque 新的尾元素为：" << d.back() << endl;
    return 0;   

```  

##### 4. list容器

(0) 概述

  STL list 容器，又称双向链表容器，即该容器的底层是以双向链表的形式实现的。这意味着元素可以分散存储在内存空间里，而不是必须存储在一整块连续的内存空间中

![image](https://user-images.githubusercontent.com/42632290/233992153-15a59367-90a7-4268-96cb-f2d26f6d5cd3.png)

  每个元素都配备了 2 个指针，分别指向它的前一个元素和后一个元素。其中第一个元素的前向指针总为 null，因为它前面没有元素；同样，尾部元素的后向指针也总为 null
    
  list 容器具有一些其它容器（array、vector 和 deque）所不具备的优势，即可以在**序列已知的任何位置快速插入或删除元素（时间复杂度为O(1))**。并且在 list 容器中移动元素，也比其它容器的效率高。 
    
    list 容器的缺点是，它不能像 array 和 vector 那样，通过位置直接访问元素
    
```
1.头文件
#include <list>
using namespace std;
```

(1) 创建

```
a) 创建空容器

    std::list<int> values;  //空的 vector 容器，没有元素，所以没有为其分配空间。当添加第一个元素时，会自动分配内存
    
b) 包含 n 个元素的 list 容器
    
    std::list<int> values(10);  //包含 10 个元素，每个元素的值都为相应类型的默认值（int类型的默认值为 0）
    
c) 包含 n 个元素的 list 容器，指定初始值 
    
    std::list<int> values(10, 5); //10 个元素并且值都为 5 
    
d) 使用已有容器初始化新容器

    std::list<int> value1(10);
    std::list<int> value2(value1);

e) 拷贝其他类型容器（或者普通数组）中指定区域内的元素

   //拷贝普通数组，创建list容器
   int a[] = { 1,2,3,4,5 };
   std::list<int> values(a, a+5);

   //拷贝其它类型的容器，创建 list 容器
   std::array<int, 5> arr{ 11,12,13,14,15 };
   std::list<int> values(arr.begin()+2, arr.end());  //拷贝arr容器中的{13,14,15} 

```


(2) 常见成员函数

![image](https://user-images.githubusercontent.com/42632290/233994737-4c4f9def-91a9-444e-a674-0be7c3b498a1.png)
![image](https://user-images.githubusercontent.com/42632290/233994811-a100c8c2-8fc7-4cdc-ab52-3b5d8c622dc1.png)

(3) 迭代器使用详解

1. 遍历

```
    (1) list不支持随机访, 只能通过迭代器遍历容器中的数据
    
示例:
#include <iostream>
#include <list>
using namespace std;

int main()
{
    //创建 list 容器
    std::list<char> values{'h','t','t','p',':','/','/','w','w','w','.','c','d','s','y','.','x','y','z'};
    
    //使用begin()/end()迭代器函数对输出list容器中的元素
    for (std::list<char>::iterator it = values.begin(); it != values.end(); ++it) {
        std::cout << *it;   //输出：http://www.cdsy.xyz
    }
    cout << endl;
    
    //使用 rbegin()/rend()迭代器函数输出 lsit 容器中的元素
    for (std::list<char>::reverse_iterator it = values.rbegin(); it != values.rend();++it) {
        std::cout << *it;   //输出：zyx.ysdc.www//:ptth
    }
    return 0;
}
    
    
    
    
```       

2. 增

    插入元素推荐使用emplace 系列方法的执行效率更高
    
```
a) 仅有 1 种语法格式的内置函数

    push_front()：向 list 容器首个元素前添加新元素；
    push_back()： 向 list 容器最后一个元素后添加新元素；
    emplace_front()：在容器首个元素前直接生成新的元素；
    emplace_back()： 在容器最后一个元素后直接生成新的元素；
    emplace()：在容器的指定位置直接生成新的元素；
    
示例:
#include <iostream>
#include <list>
using namespace std;
int main()
{
    std::list<int> values{1,2,3};
    values.push_front(0);//{0,1,2,3}
    values.push_back(4); //{0,1,2,3,4}

    values.emplace_front(-1);//{-1,0,1,2,3,4}
    values.emplace_back(5);  //{-1,0,1,2,3,4,5}
   
    //emplace(pos,value),其中 pos 表示指明位置的迭代器，value为要插入的元素值
    values.emplace(values.end(), 6);//{-1,0,1,2,3,4,5,6}
}
    
b) insert()成员方法
    
    iterator insert(pos,elem)   //在 pos 位置之前插入一个新元素 elem，并返回表示新插入元素位置的迭代器。
    iterator insert(pos,n,elem)	//在 pos 位置之前插入 n 个元素 elem，并返回表示第一个新插入元素位置的迭代器。
    iterator insert(pos,first,last) //在 pos 位置之前，插入其他容器（例如 array、vector、deque 等）中位于 [first,last) 区域的所有元素，并返回表示第一个新插入元素位置的迭代器。
    iterator insert(pos,initlist) //在 pos 位置之前，插入初始化列表（用大括号 { } 括起来的多个元素，中间有逗号隔开）中所有的元素，并返回表示第一个新插入元素位置的迭代器
    
示例：
#include <iostream>
#include <list>
#include <array>
using namespace std;
int main()
{
    std::list<int> values{ 1,2 };
    //第一种格式用法
    values.insert(values.begin() , 3);  //{3,1,2}

    //第二种格式用法
    values.insert(values.end(), 2, 5);  //{3,1,2,5,5}

    //第三种格式用法
    std::array<int, 3>test{ 7,8,9 };
    values.insert(values.end(), test.begin(), test.end());  //{3,1,2,5,5,7,8,9}

    //第四种格式用法
    values.insert(values.end(), { 10,11 });   //{3,1,2,5,5,7,8,9,10,11}
}
    
c) splice()成员方法   

   void splice (iterator position, list& x); //position 为迭代器，用于指明插入位置；x 为另一个 list 容器。将 x 容器中存储的所有元素全部移动当前 list 容器中 position 指明的位置处。 
   void splice (iterator position, list& x, iterator i); //position 为迭代器，用于指明插入位置；x 为另一个 list 容器；i 也是一个迭代器，用于指向 x 容器中某个元素。将 x 容器中 i 指向的元素移动到当前容器中 position 指明的位置处。 
   void splice (iterator position, list& x, iterator first, iterator last);	//position 为迭代器，用于指明插入位置；x 为另一个 list 容器；first 和 last 都是迭代器。将 x 容器 [first, last) 范围内所有的元素移动到当前容器 position 指明的位置处 

示例：
#include <iostream>
#include <list>
using namespace std;
int main()
{
    //创建并初始化 2 个 list 容器
    list<int> mylist1{ 1,2,3,4 }, mylist2{10,20,30};
    list<int>::iterator it = ++mylist1.begin(); //指向 mylist1 容器中的元素 2
   
    //调用第一种语法格式
    mylist1.splice(it, mylist2); // mylist1: 1 10 20 30 2 3 4
                                 // mylist2:
                                 // it 迭代器仍然指向元素 2，只不过容器变为了 mylist1

    //调用第二种语法格式，将 it 指向的元素 2 移动到 mylist2.begin() 位置处
    mylist2.splice(mylist2.begin(), mylist1, it);   // mylist1: 1 10 20 30 3 4
                                                    // mylist2: 2
                                                    // it 仍然指向元素 2
   
    //调用第三种语法格式，将 [mylist1.begin(),mylist1.end())范围内的元素移动到 mylist.begin() 位置处                  
    mylist2.splice(mylist2.begin(), mylist1, mylist1.begin(), mylist1.end());//mylist1:
                                                                             //mylist2:1 10 20 30 3 4 2
}
    
Note:
    list 容器底层使用的是链表存储结构，splice() 成员方法移动元素的方式是，将存储该元素的节点从 list 容器底层的链表中摘除，然后再链接到当前 list 容器底层的链表中。这意味着，当使用 splice() 成员方法将 x 容器中的元素添加到当前容器的同时，该元素会从 x 容器中删除
```       

3. 删

    list 容器在进行插入（insert()）、接合（splice()）等操作时，都不会造成原有的 list 迭代器失效，甚至进行删除操作，而只有指向被删除元素的迭代器失效，其他迭代器不受任何影响

```
a) pop_front(): 删除位于 list 容器头部的一个元素。
   pop_back(): 删除位于 list 容器尾部的一个元素
   clear(): 删除 list 容器存储的所有元素

示例：
#include <iostream>
#include <list>
using namespace std;
int main()
{
    list<int>values{ 1,2,3,4 };
   
    //删除当前容器中首个元素
    values.pop_front();//{2,3,4}
   
    //删除当前容器最后一个元素
    values.pop_back();//{2,3}
   
    //清空容器，删除容器中所有的元素
    values.clear(); //{}
}
    
b) erase() 成员函数: 按照被删除元素所在的位置来执行删除操作
    
    iterator erase (iterator position);    //删除 list 容器中 position 迭代器所指位置处的元素
    iterator erase (iterator first, iterator last); //删除 list 容器中 first 迭代器和 last 迭代器限定区域内的所有元素（包括 first 指向的元素，但不包括 last 指向的元素）

示例1：
#include <iostream>
#include <list>
using namespace std;
int main()
{
    list<int>values{ 1,2,3,4,5 };
    //指向元素 1 的迭代器
    auto del = values.begin();
    //迭代器右移，改为指向元素 2
    ++del;
    values.erase(del);   //{1,3,4,5}
}

示例2：
#include <iostream>
#include <list>
using namespace std;
int main()
{
    list<int>values{ 1,2,3,4,5 };
    //指定删除区域的左边界
    auto first = values.begin();
    ++first;//指向元素 2

    //指向删除区域的右边界
    auto last = values.end();
    --last;//指向元素 5

    //删除 2、3 和 4
    values.erase(first, last);   //1 5
}
    
c) remove() 成员函数: 根据元素的值来执行删除操作

 示例：
#include <iostream>
#include <list>
using namespace std;
int main()
{
    list<char>values{'a','b','c','d'};
    values.remove('c');  //a b d
}

d) unique() 函数

    void unique() //去除 list 容器中相邻重复的元素，仅保留一份
    void unique（BinaryPredicate）//传入一个二元谓词函数, 自定义去重的规则

示例1：
#include <iostream>
#include <list>
using namespace std;

//二元谓词函数
bool demo(double first, double second)
{
    return (int(first) == int(second));
}

int main()
{
    list<double> mylist{ 1,1.2,1.2,3,4,4.5,4.6 };
    mylist.unique(); //{1, 1.2, 3, 4, 4.5, 4.6}  //删除相邻重复的元素，仅保留一份

    //demo 为二元谓词函数，是我们自定义的去重规则
    mylist.unique(demo);  //{1, 3, 4}
}    

e)  remove_if() 成员: 满足条件的都会被删除, unique只能删除相邻重复的
    
#include <iostream>
#include <list>
using namespace std;

int main()
{
    std::list<int> mylist{ 15, 36, 7, 17, 20, 39, 4, 1 };
    
    //删除 mylist 容器中能够使 lamba 表达式成立的所有元素。
    mylist.remove_if([](int value) {return (value < 10); }); //{15 36 17 20 39}
}

```         
             
 4. 改与查

    list容器不支持随机访问, 未提供下标操作符 [] 和 at() 成员函数，也没有提供 data() 成员函数
    
```
a)  front() 和 back()函数: 返回第一个元素和最后一个元素的引用形式, 可以进行修改值 
             
示例：
#include <iostream>
#include <list>
using namespace std;

int main()
{
    std::list<int> mylist{ 1,2,3,4 };
    int &first = mylist.front();
    int &last = mylist.back();
    cout << first << " " << last << endl;  //输出：1 4
    first = 10;
    last = 20;
    cout << mylist.front() << " " << mylist.back() << endl;  //输出：10 20
    return 0;
}

b) 使用迭代器进行修改和查找

#include <iostream>
#include <list>
using namespace std;
int main()
{
    const std::list<int> mylist{1,2,3,4,5};
    auto it = mylist.begin();
    cout << *it << " ";   //输出：1 
    ++it;
    while (it!=mylist.end())
    {
        cout << *it << " ";    //输出：2 3 4 5
        ++it;  
    }
    return 0;
}    
             
```  

5. empty()和size()都可以判断容器是否为空，谁更好？
    
```
a) if(cont.size() == 0)
b) if(cont.empty())
    
    建议使用 empty() 成员方法。理由很简单，无论是哪种容器，只要其模板类中提供了 empty() 成员方法，使用此方法都可以保证在 O(1) 时间复杂度内完成对“容器是否为空”的判断；但对于 list 容器来说，使用 size() 成员方法判断“容器是否为空”，可能要消耗 O(n) 的时间复杂度
    
```

参考：https://www.cdsy.xyz/computer/programme/stl/20210307/cd161510780611986.html
    
    
##### 5. forward_list容器

(0) 概述

```
    1) forward_list 是 C++ 11 新添加的一类容器，其底层实现和 list 容器一样，采用的也是链表结构，只不过 forward_list 使用的是单链表，而 list 使用的是双向链表.
    
    2) forward_list 容器具有和 list 容器相同的特性，即擅长在序列的任何位置进行插入元素或删除元素的操作，但对于访问存储的元素，没有其它容器（如 array、vector）的效率高.
    
    3) 单链表没有双向链表那样灵活，因此相比 list 容器，forward_list 容器的功能受到了很多限制。比如，由于单链表只能从前向后遍历，而不支持反向遍历，因此 forward_list 容器只提供前向迭代器，而不是双向迭代器
    
    4) 既然 forward_list 容器具有和 list 容器相同的特性，list 容器还可以提供更多的功能函数，forward_list 容器有什么存在的必要呢？
    
        forward_list 容器底层使用单链表，也不是一无是处。比如，存储相同个数的同类型元素，单链表耗用的内存空间更少，空间利用率更高，并且对于实现某些操作单链表的执行效率也更高
    
       效率高是选用 forward_list 而弃用 list 容器最主要的原因，换句话说，只要是 list 容器和 forward_list 容器都能实现的操作，应优先选择 forward_list 容器
```

```
头文件：
#include <forward_list>
using namespace std; 
```

(1) 创建

```
a) 创建空的forward_list
    
    std::forward_list<int> values;
    
b) 创建包含n个元素的容器
    
    std::forward_list<int> values(10);  //int类型的默认值为 0

c) 创建包含n个元素的容器，并指定初始值

    std::forward_list<int> values(10, 5); //包含 10 个元素并且值都为 5 个 values 容器

d) 使用已有容器初始化新容器

    std::forward_list<int> value1(10);
    std::forward_list<int> value2(value1); //必须保证新旧容器存储的元素类型一致

e) 拷贝其他类型容器（或者普通数组）中指定区域内的元素

    int a[] = { 1,2,3,4,5 };
    std::forward_list<int> values(a, a+5); //拷贝普通数组，创建forward_list容器
    
    std::array<int, 5>arr{ 11,12,13,14,15 }; //拷贝其它类型的容器，创建forward_list容器
    std::forward_list<int>values(arr.begin()+2, arr.end());//拷贝arr容器中的{13,14,15} 

```

(2) 常见成员函数

<img width="577" alt="image" src="https://github.com/chuanchuan11/cplus/assets/42632290/6f9e08c1-6218-4549-826e-8db0dddb522e">
<img width="577" alt="image" src="https://github.com/chuanchuan11/cplus/assets/42632290/802b386b-9de3-45c7-a8e3-944c5c351f8c">
    
(3) 迭代器使用详解

1. 遍历

```
略     
```       

2. 增删改查

```
略 
```       

(4) 使用forward_list容器相关的函数

```
a) forward_list 容器中不提供 size() 函数，如果想获取 forward_list 容器中存储元素的个数，可以使用头文件 <iterator> 中的 distance() 函数 
    
#include <iostream>
#include <forward_list>
#include <iterator>
using namespace std;

int main()
{
    std::forward_list<int> my_words{1,2,3,4};
    int count = std::distance(std::begin(my_words), std::end(my_words));
    cout << count;   //输出:4
    return 0;
}

b) forward_list 容器迭代器的移动除了使用 ++ 运算符单步移动，还能使用 advance() 函数进行跨步移动
    
#include <iostream>
#include <forward_list>
using namespace std;

int main()
{
    std::forward_list<int> values{1,2,3,4};
    auto it = values.begin();
    advance(it, 2);
    while (it!=values.end())
    {
        cout << *it << " ";     //输出：3 4
        ++it;
    }
    return 0;
}
```
    
