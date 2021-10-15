### 1.增强型for循环之字符串

C++11中新增的一种循环写法，对数组(或容器类,如vector和array)的每个元素执行相同的操作，此外string类也支持这种对字符的遍历循环操作

```c++
for (char c : s)
```

时会复制一个s字符串再进行遍历操作，而使用

```c++
for (char& c : s)
```

时直接引用原字符串进行遍历操作。由于复制一个字符串花费了大量的时间，所以第二种解法要快于第一种解法。

### 2.unordered_map与unordered_set底层原理

（1）unordered_map的底层是一个防冗余的哈希表（采用除留余数法），哈希表最大的优点是把数据的存储和查找消耗的时间大大降低，时间复杂度为O（1）；代价是消耗比较多的内存

使用一个下标范围比较大的数组来存储元素。可以设计一个函数（哈希函数（⼀般使⽤除留取余法），也叫做散列 函数），使得每个元素的key都与一个函数值（即数组下标，hash值）相对应，于是用这个数组单元来存储这个元 素；也可以简单的理解为，按照key为每一个元素“分类”，然后将这个元素存储在相应“类”所对应的地方，称为桶。 但是，不能够保证每个元素的key与函数值是一一对应的，因此极有可能出现对于不同的元素，却计算出了相同的 函数值，这样就产生了“冲突”，换句话说，就是把不同的元素分在了相同的“类”之中。 一般可采用拉链法解决冲突

```c++
#include <iostream>
#include <vector>
#include <list>
#include <random>
#include <ctime>
using namespace std;
const int hashsize = 12;
//定⼀个节点的结构体
template <typename T, typename U>
struct HashNode
{
 T _key;
 U _value;
};
//使⽤拉链法实现哈希表类
template <typename T, typename U>
class HashTable
{
public:
 HashTable() : vec(hashsize) {}//类中的容器需要通过构造函数来指定⼤⼩
 ~HashTable() {}
 bool insert_data(const T &key, const U &value);
 int hash(const T &key);
 bool hash_find(const T &key);
private:
vector<list<HashNode<T, U>>> vec;//将节点存储到容器中
};
//哈希函数（除留取余）
template <typename T, typename U>
int HashTable<T, U>::hash(const T &key)
{
 return key % 13;
}
//哈希查找
template <typename T, typename U>
bool HashTable<T, U>::hash_find(const T &key)
{
 int index = hash(key);//计算哈希值
 for (auto it = vec[index].begin(); it != vec[index].end(); ++it)
 {
 if (key == it -> _key)//如果找到则打印其关联值
 {
 cout << it->_value << endl;//输出数据前应该确认是否包含相应类型
 return true;
 }
 }
 return false;
}
//插⼊数据
template <typename T, typename U>
bool HashTable<T, U>::insert_data(const T &key, const U &value)
{
 //初始化数据
 HashNode<T, U> node;
 node._key = key;
 node._value = value;
 for (int i = 0; i < hashsize; ++i)
 {
 if (i == hash(key))//如果溢出则把相应的键值添加进链表
 {
 vec[i].push_back(node);
 return true;
 }
 }
}
int main(int argc, char const *argv[])
{
 HashTable<int, int> ht;
 static default_random_engine e;
 static uniform_int_distribution<unsigned> u(0, 100);
long long int a = 10000000;
 for (long long int i = 0; i < a; ++i)
 ht.insert_data(i, u(e));
 clock_t start_time = clock();
 ht.hash_find(114);
 clock_t end_time = clock();
 cout << "Running time is: " << static_cast<double>(end_time - start_time) /
CLOCKS_PER_SEC * 1000 <<
 "ms" << endl;//输出运⾏时间。
 system("pause");
 system("pause");
 return 0;
}
```

##### unordered_map 与map的区别

构造函数：unordered_map 需要hash函数，等于函数;map只需要比较函数(小于函数). 

存储结构：unordered_map 采用hash表存储，map一般采用红黑树(RB Tree) 实现。因此其memory数据结构是 不一样的。 

总体来说，unordered_map 查找速度会比map快，而且查找速度基本和数据数据量大小，属于常数级别;而map的查找速度是log(n)级别。并不一定常数就比log(n)小，hash还有hash函数的耗时，如果考虑效率，特别是在元素达到一定数量级时，考虑考虑unordered_map 。但若你对内存使用特别严格，希望程序尽可能少消耗内存，那么unordered_map 可能会让你陷入尴尬，特别是当你的unordered_map 对象特别多时， 你就更无法控制了，而且unordered_map 的构造速度较慢。

### 3.using的用法

###### 1）配合命名空间，对命名空间权限进行管理

```c++
using namespace std;//释放整个命名空间到当前作用域
using std::cout;    //释放某个变量到当前作用域
```

###### 2）作用等同于typedef，但逻辑更直观

```c++
#include <iostream>

using namespace std;

#define DString std::string    //! 不建议使用！

typedef std::string TString;   //! 使用typedef的方式

using Ustring = std::string;   //！使用 using typeName_self = stdtypename;

//更直观
typedef void (tFunc*)(void);
using uFunc = void(*)(void);
```

###### 3）继承体系中，改变部分接口的继承权限

比如需要私有继承一个基类，然后又想将基类中的某些public接口在子类对象实例化后对外开放直接使用。

```c++
#include <iostream>
//#include <array>
#include <typeinfo>


using namespace std;

class Base
{
public:
    Base()
    {}
    ~Base(){}

    void dis1()
    {
        cout<<"dis1"<<endl;
    }
    void dis2()
    {
        cout<<"dis2"<<endl;
    }
};

class BaseA:private Base
{
public:
    using Base::dis1;//需要在BaseA的public下释放才能对外使用，
    void dis2show()
    {
        this->dis2();
    }
};

int main(int argc, char *argv[])
{

    BaseA ba;
    ba.dis1();
    ba.dis2show();

    return 0;
}
```

### 4. STL内嵌数据类型：value_type

每个STL中的类都有value_type这种东西，通俗的说value_type 就是stl容器盛装的数据的数据类型，例如：

```c++
vector<int> vec;

vector<int>::value_type x;
```

上述两句代码，第一句是声明一个盛装数据类型是int的数据的vector，第二句是使用`vector<int>::value_type`定义一个变量x，这个变量x实际上是int类型的，因为`vector<int>::value_type`中声明的为int型。相应的，假设有：

```c++
vector<C> vec;  //假设C是自定义类型

vector<C>::value_type x;
```

那么第二句定义的变量x的数据类型是C。

每个STL容器类（感觉应该是迭代器类更加准确），都有一句相同的代码：

`typede T value_type;`

其中T则是类模板中使用的参数 ：

`template <class T>` 

以STL的list容器为例，那么它的类定义就应该有下面的语句:

```c++
template<class T>

class list{

publict:

typedef  T  value_type;

//……

};
```

T可以是一个class，那么value_type也就是可以用来定义class的对象了。所以就可以有下面代码的用法：

```c++
#include <list>  
#include <vector>  
#include <iostream>  
using namespace std;  
class C{  
public:  
  C(int x){  
    cout << x << endl;  
  }  
  C(){  
    cout << 10 << endl;  
  }  

};  

void main(){  
  vector<C> vec;  
  C c1,c2(11);  
  vec.push_back(c1);  
  vec.push_back(c2);  
  vector<C>::value_type n1;  
  vector<C>::value_type n2(13);  
  vec.push_back(n1);  
  vec.push_back(n2);  
  cout << vec.size() << endl;  
}
```

### 5.vector插入操作可能会导致迭代器失效

vector动态增加大小的时候，并不是在原空间后增加新的空间，而是以原大小的两倍在另外配置一片较大的新空间，然后将内容拷贝过来，并释放原来的空间，由于操作改变了空间，所以迭代器失效

### 6.标准库中的容器以及各自的特点

标准库中的容器主要有三类：顺序容器、关联容器和容器适配器

##### 顺序容器：

`array<T,N>` 数组：固定大小数组，支持快速随机访问，但不能插入或删除元素

`vector<T>` 动态数组：支持快速随机访问，尾位插入和删除的速度很快；

`deque<T>` 双向队列：支持快速随机访问，首尾位置插入和删除的速度很快；（可以看作是 `vector`的增强版，与 `vector` 相比，可以快速地在首位插入和删除元素）

`list<T>`双向链表：只支持双向顺序访问，任何位置插入和删除的速度都很快；

##### 关联容器：

map容器：

`map<K,T>` 关联数组：用于保存关键字-值对；

`multimap<K,T>` ：关键字可重复出现的 `map` ；

`unordered_map <K,T>`：用哈希函数组织的 `map` ；

`unordered_multimap <K,T>`：关键词可重复出现的 `unordered_map` ；

set容器：

`set <T>`：只保存关键字；

`multiset<T>` ：关键字可重复出现的 set ； 

`unordered_set <T>`：用哈希函数组织的 set ； 

`unordered_multiset<T>` ：关键词可重复出现的 unordered_set ；

###### 容器适配器包含三种类型： stack 栈、 queue 队列、 priority_queue 优先队列

### 7.union和struct

##### 联合体

使几个不同类型的变量共占一段内存（相互覆盖）

##### 结构体

把不同类型的数据组合成一个整体——自定义数据类型

声明一个联合体

```c++
union myun {
	struct {
		int x;
		int y;
		int z;
	}u;
	int k;
}a;
```

```c++
union abc{
          int i;
          char m;
         };
```

在联合体myun中，整型量x和k公用同一内存位置

当一个联合被说明时，编译程序自动的产生一个变量，其长度为联合中最大的变量长度

结构体变量所占内存长度是各成员占的内存长度的总和。

共同体变量所占内存长度是各最长的成员占的内存长度。

共同体每次只能存放哪个的一种

共同体变量中起作用的成员是最后一次存放的成员,在存入新的成员后原有的成员失去了作用

#### struct用处

##### 1.在网络协议、通信控制、嵌入式系统的**C/C**++**编程中，我们经常要传送的不是简单的字节流（**char**型数组），而是多种数据组合起来的一个整体，其表现形式是一个结构体

假设网络或控制协议中需要传送三种报文，其格式分别为**packetA**、**packetB**、**packetC**：

```c++
struct structA 
{
    int a;
    char b;
};
struct structB 
{
    char a;
    short b;
};
struct structC
{
    int a;
    char b;
    float c;
}
```

传送的报文：

```c++
struct CommuPacket
{
    int iPacketType;　　//报文类型标志
    union　　　　　　   //每次传送的是三种报文中的一种，使用union
    {
        struct structA packetA;
        struct structB packetB;
        struct structC packetC;
    }
};
```

在进行报文传送时，直接传送`struct CommuPacket`一个整体。

假设发送函数的原形如下：

*// pSendData*：发送字节流的首地址，*iLen*：要发送的长度*
Send(char \* pSendData, unsigned int iLen);
*发送方可以直接进行如下调用发送*struct CommuPacket*的一个实例*sendCommuPacket*：*
Send( (char \*)&sendCommuPacket , sizeof(CommuPacket) );
*假设接收函数的原形如下：*
// pRecvData*：发送字节流的首地址，*iLen*：要接收的长度*
//*返回值：实际接收到的字节数*
unsigned int Recv(char \* pRecvData, unsigned int iLen)*；*
*接收方可以直接进行如下调用将接收到的数据保存在*struct CommuPacket*的一个实例*
recvCommuPacket*中：

*Recv( (char \*)&recvCommuPacket , sizeof(CommuPacket) );
*接着判断报文类型进行相应处理：

```c++
switch(recvCommuPacket. iPacketType)
{
    case PACKET_A:
    …    //A类报文处理
    break;
    case PACKET_B:
    …　 //B类报文处理
    break;
    case PACKET_C:
    …   //C类报文处理
    break;
}
```

2. ##### struct成员对齐

```C++

#include <iostream.h>
#pragma pack(8)
struct example1
{
    short a;
    long b;
};
struct example2
{
    char c;
    example1 struct1;
    short e;    
};
#pragma pack()
int main(int argc, char* argv[])
{
    example2 struct2;
    cout << sizeof(example1) << endl;
    cout << sizeof(example2) << endl;
    cout << (unsigned int)(&struct2.struct1) - (unsigned int)(&struct2) << endl;
    return 0;
}
```

答案是8 16 4

2.1 自然对界

  *struct*是一种复合数据类型，其构成元素既可以是基本数据类型（如*int*、*long*、*float*等）的变量，也可以是一些复合数据类型（如 *array*、*struct*、*union*等）的数据单元。对于结构体，编译器会自动进行成员变量的对齐，以提高运算效率。缺省情况下，编译器为结构体的每个 成员按其自然对界（*natural alignment*）条件分配空间。各个成员按照它们被声明的顺序在内存中顺序存储，第一个成员的地址和整个结构的地址相同。

  自然对界*(natural alignment)*即默认对齐方式，是指按结构体的成员中*size*最大的成员对齐。

  例如：

```C++
struct naturalalign
{
    char a;
    short b;
    char c;
};
```

在上述结构体中，*size*最大的是*short*，其长度为*2*字节，因而结构体中的*char*成员*a*、*c*都以*2*为单位对齐，*sizeof(naturalalign)*的结果等于*6*；

2.2 指定对界

一般地，可以通过下面的方法来改变缺省的对界条件：

 使用伪指令*#pragma pack (n)*，编译器将按照*n*个字节对齐；
 使用伪指令*#pragma pack ()*，取消自定义字节对齐方式。

注意：如果*#pragma pack (n)*中指定的*n*大于结构体中最大成员的*size*，则其不起作用，结构体仍然按照*size*最大的成员进行对界。

### 8.extern C

用于C++链接在C语言模块中定义的函数

C++虽然兼容C，但C++文件中函数编译后生成的符号与C语言生成的不同。因为C++支持函数重载，C++函数编译后生成的符号带有函数参数类型的信息，而C则没有。

例如`int add(int a, int b)`函数经过C++编译器生成.o文件后，`add`会变成形如`add_int_int`之类的, 而C的话则会是形如`_add`, 就是说：相同的函数，在C和C++中，编译后生成的符号不同。

这就导致一个问题：如果C++中使用C语言实现的函数，在编译链接的时候，会出错，提示找不到对应的符号。此时`extern "C"`就起作用了：告诉链接器去寻找`_add`这类的C语言符号，而不是经过C++修饰的符号

C++调用C函数的例子: 引用C的头文件时，需要加`extern "C"`，通常为了C代码能够通用，即既能被C调用，又能被C++调用，头文件通常会有如下写法：

```c++
#ifdef __cplusplus
extern "C"{
#endif
int add(int x,int y);
#ifdef __cplusplus
}
#endif
```

在C语言的头文件中，对其外部函数只能指定为extern类型，C语言中不支持extern "C"声明，在.c文件中包含了extern "C"时会出现编译语法错误。所以使用extern "C"全部都放在于cpp程序相关文件或其头文件中。

### 9. const与指针

常类型是指使用类型修饰符const说明的类型，常类型的变量或对象的值不能被更新

#### const作用

1）定义常量

const定义的常量不可更改，而且必须初始化（常量被定义后就不能被修改，所以必须初始化）

2）类型检查

- const常量与`#define`宏定义常量的区别：

- const定义的变量只有类型为整数或枚举，且以常量表达式初始化时才能作为常量表达式。
- 其他情况下它只是一个 `const` 限定的变量，不要将与常量混淆。

3）防止修改，起保护作用，增加程序健壮性

```c++
void f(const int i){
    i++; //error!
}
```

4）可以节省空间，避免不必要的内存分配

- const定义常量从汇编的角度来看，只是给出了对应的内存地址，而不是像`#define`一样给出的是立即数。
- const定义的常量在程序运行过程中只有一份拷贝，而`#define`定义的常量在内存中有若干个拷贝。

#### const对象默认为文件局部变量

非const变量默认为extern。要使const变量能够在其他文件中访问，必须在文件中显式地指定它为extern。

未被const修饰的变量在不同文件的访问

```c++
// file1.cpp
int ext
// file2.cpp
#include<iostream>

extern int ext;
int main(){
    std::cout<<(ext+10)<<std::endl;
}
```

const常量在不同文件的访问

```c++
//extern_file1.cpp
extern const int ext=12;
//extern_file2.cpp
#include<iostream>
extern const int ext;
int main(){
    std::cout<<ext<<std::endl;
}
```

#### 指针与const

四种情况：

```c++
const char * a; //指向const对象的指针或者说指向常量的指针。
char const * a; //同上
char * const a; //指向类型对象的const指针。或者说常指针、const指针。
const char * const a; //指向const对象的const指针。
```

如果*const*位于`*`的左侧，则const就是用来修饰指针所指向的变量，即指针指向为常量；
如果const位于`*`的右侧，*const*就是修饰指针本身，即指针本身是常量。

##### 1）指向常量的指针

```c++
const int *ptr;
*ptr = 10; //error
```

ptr是一个指向int类型const对象的指针，const定义的是int类型，也就是ptr所指向的对象类型，而不是ptr本身，所以ptr可以不用赋初始值。但是不能通过ptr去修改所指对象的值

除此之外，也不能使用void`*`指针保存const对象的地址，必须使用const void`*`类型的指针保存const对象的地址

```c++
const int p = 10;
const void * vp = &p;
void *vp = &p; //error
```

**允许把非const对象的地址赋给指向const对象的指针**：

```c++
const int *ptr;
int val = 3;
ptr = &val; //ok
```

小结：
1.对于指向常量的指针，不能通过指针来修改对象的值。
2.不能使用void`*`指针保存const对象的地址，必须使用const void`*`类型的指针保存const对象的地址。
3.允许把非const对象的地址赋值给const对象的指针，如果要修改指针所指向的对象值，必须通过其他方式修改，不能直接通过当前指针直接修改

##### 2）常指针

const指针必须进行初始化，且const指针的值不能修改

```c++
#include<iostream>
using namespace std;
int main(){

    int num=0;
    int * const ptr=&num; //const指针必须初始化！且const指针的值不能修改
    int * t = &num;
    *t = 1;
    cout<<*ptr<<endl;
}
```

上述修改ptr指针所指向的值，可以通过非const指针来修改。

##### 3）指向常量的常指针

```c++
const int p=3;
const int *const ptr=&p;
```

ptr是一个const指针，然后指向了一个int 类型的const对象

#### 函数中使用const

##### const修饰函数返回值

（1）**const int**

```
const int func1();
```

这个本身无意义，因为参数返回本身就是赋值给其他的变量！

（2）**const int***

```
const int* func2();
```

指针指向的内容不变。

（3）int *const

```
int *const func2();
```

指针本身不可变。

##### const修饰函数参数

1）参数指针所指内容为常量不可变

```
void StringCopy(char *dst, const char *src);
```

其中src 是输入参数，dst 是输出参数。给src加上const修饰后，如果函数体内的语句试图改动src的内容，编译器将指出错误。这就是加了const的作用之一。

2）参数为引用，为了增加效率同时防止修改

```
void func(const A &a)
```

对于非内部数据类型的参数而言，象void func(A a) 这样声明的函数注定效率比较低。因为函数体内将产生A 类型的临时对象用于复制参数a，而临时对象的构造、复制、析构过程都将消耗时间。

为了提高效率，可以将函数声明改为void func(A &a)，因为“引用传递”仅借用一下参数的别名而已，不需要产生临时对象。

以上解决了两个面试问题：

- 如果函数需要传入一个指针，是否需要为该指针加上const，把const加在指针不同的位置有什么区别；
- 如果写的函数需要传入的参数是一个复杂类型的实例，传入值参数或者引用参数有什么区别，什么时候需要为传入的引用参数加上const。

#### 类中使用const

在一个类中，任何不会修改数据成员的函数都应该声明为const类型，使用const关键字进行说明的成员函数，称为常成员函数，只有常成员函数才有资格操作常量或常对象

对于类中的const成员变量必须通过初始化列表进行初始化，如下所示：

```c++
class Apple{
private:
    int people[100];
public:
    Apple(int i); 
    const int apple_number;
};

Apple::Apple(int i):apple_number(i)
{

}
```

const对象只能访问const成员函数,而非const对象可以访问任意的成员函数,包括const成员函数.

### 10.volatile

- volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素（操作系统、硬件、其它线程等）更改。所以使用 volatile 告诉编译器不应对这样的对象进行优化。
- volatile 关键字声明的变量，每次访问时都必须从内存中取出值（没有被 volatile 修饰的变量，可能由于编译器的优化，从 CPU 寄存器中取值）
- const 可以是 volatile （如只读的状态寄存器）
- 指针可以是 volatile

#### 具体应用

1.并行设备的硬件寄存器（如状态寄存器）。 假设要对一个设备进行初始化，此设备的某一个寄存器为0xff800000。

```c++
int  *output = (unsigned  int *)0xff800000; //定义一个IO端口；  
int   init(void)  
{  
    int i;  
    for(i=0;i< 10;i++)
    {  
    *output = i;  
    }  
}
```

经过编译器优化后，编译器认为前面循环半天都是废话，对最后的结果毫无影响，因为最终只是将output这个指针赋值为 9，所以编译器最后给你编译编译的代码结果相当于：

```c++
int  init(void)  
{  
    *output = 9;  
}
```

如果你对此外部设备进行初始化的过程是必须是像上面代码一样顺序的对其赋值，显然优化过程并不能达到目的。反之如果你不是对此端口反复写操作，而是反复读操作，其结果是一样的，编译器在优化后，也许你的代码对此地址的读操作只做了一次。然而从代码角度看是没有任何问题的。这时候就该使用volatile通知编译器这个变量是一个不稳定的，在遇到此变量时候不要优化。

2. 一个中断服务子程序中访问到的变量；

```c++
static int i=0;

int main()
{
    while(1)
    {
    if(i) dosomething();
    }
}

/* Interrupt service routine */
void IRS()
{
	i=1;
}
```

上面示例程序的本意是产生中断时，由中断服务子程序IRS响应中断，变更程序变量i，使在main函数中调用dosomething函数，但是，由于编译器判断在main函数里面没有修改过i，因此可能只执行一次对从i到某寄存器的读操作，然后每次if判断都只使用这个寄存器里面的“i副本”，导致dosomething永远不会被调用。如果将变量i加上volatile修饰，则编译器保证对变量i的读写操作都不会被优化，从而保证了变量i被外部程序更改后能及时在原程序中得到感知。

3. 多线程应用中被多个任务共享的变量。 当多个线程共享某一个变量时，该变量的值会被某一个线程更改，应该用 volatile 声明。作用是防止编译器优化把变量从内存装入CPU寄存器中，当一个线程更改变量后，未及时同步到其它线程中导致程序出错。volatile的意思是让编译器每次操作该变量时一定要从内存中真正取出，而不是使用已经存在寄存器中的值。示例如下：

```c++
volatile  bool bStop=false;  //bStop 为共享全局变量  
//第一个线程
void threadFunc1()
{
    ...
    while(!bStop){...}
}
//第二个线程终止上面的线程循环
void threadFunc2()
{
    ...
    bStop = true;
}
```

要想通过第二个线程终止第一个线程循环，如果bStop不使用volatile定义，那么这个循环将是一个死循环，因为bStop已经读取到了寄存器中，寄存器中bStop的值永远不会变成FALSE，加上volatile，程序在执行时，每次均从内存中读出bStop的值，就不会死循环了。

### 11.引用

C++的引用**在减少了程序员自由度的同时提升了内存操作的安全性和语义的优美性**。比如引用强制要求必须初始化，可以让我们在使用引用的时候不用再去判断引用是否为空，让代码更加简洁优美，避免了指针满天飞的情形。

##### 左值引用

常规引用，一般表示对象的身份。引用是为对象起的别名，必须被初始化，与变量绑定到一起，且将一直绑定在一起。

##### 右值引用

右值引用是C++11中引入的新特性 , 它实现了转移语义和精确传递。

它的主要目的有两个方面：

消除两个对象交互时不必要的对象拷贝，节省运算存储资源，提高效率。
能够更简洁明确地定义泛型函数。

右值引用就是必须绑定到右值的引用，他有着与左值引用完全相反的绑定特性，我们通过 && 来获得右值引用。
右值有一个重要的性质——只能绑定到一个将要销毁的对象上：

```c++
int  &&rr = i;  //错误，i是一个变量，变量都是左值
int &&rr1 = i *42;  //正确，i*42是一个右值
```

##### 引用型参数

一般我们使用const reference参数作为只读形参，这种情况下既可以避免参数拷贝还可以获得与传值参数一样的调用方式

```c++
void test(const vector<int> &data)
{
    //...
}
int main()
{
  	vector<int> data{1,2,3,4,5,6,7,8};
    test(data);
}
```

##### 引用型返回值

C++提供了重载运算符的功能，我们在重载某些操作符的时候，使用引用型返回值可以获得跟该操作符原来语法相同的调用方式，保持了操作符语义的一致性。一个例子就是operator []操作符，这个操作符一般需要返回一个引用对象，才能正确的被修改

```c++
vector<int> v(10);
v[5] = 10;    //[]操作符返回引用，然后vector对应元素才能被修改
              //如果[]操作符不返回引用而是指针的话，赋值语句则需要这样写
*v[5] = 10;   //这种书写方式，完全不符合我们对[]调用的认知，容易产生误解
```

##### C++编译器在编译程序的时候将指针和引用编译成了完全一样的机器码。所以C++中的引用只是C++对指针操作的一个“语法糖”，在底层实现时C++编译器实现这两种操作的方法完全相同。

### 12.this指针

特点：

1.一个对象的this指针不是对象的一部分，不会影响sizeof的结果

2.this作用域是在类内部，当在类的非静态成员函数中访问类的非静态成员时，编译器会将对象本身地址作为一个隐含参数地址传递给函数，也就是说，没有写this指针，编译器在编译的时候也会将其加上，他作为非静态成员函数的隐含形参，对各成员的访问均通过this进行

使用：

1.在类的非静态成员函数中返回类对象本身的时候，直接使用return *this

2.当参数与成员变量名相同时，如this->n=n







### 13.类大小计算

空类的大小为1字节

在类中，虚函数本身、成员函数（静态与非静态）和静态数据成员都是不占用类对象的存储空间

对于包含虚函数的类，不管有多少虚函数，只有一个虚指针vptr的大小

普通继承，派生类继承了所有基类的函数和成员，要按照字节对齐来计算

虚函数继承，不管是单继承还是多继承，都是继承了基类的vptr（32位操作系统4字节，64位操作系统8字节）

虚继承，继承基类的vptr

```c++
/**
 * @file static.cpp
 * @brief 静态数据成员
 * 静态数据成员被编译器放在程序的一个global data members中，它是类的一个数据成员，但不影响类的大小。不管这个类产生了多少个实例，还是派生了多少新的类，静态数据成员只有一个实例。静态数据成员，一旦被声明，就已经存在。 
 * @author 光城
 * @version v1
 * @date 2019-07-21
 */
#include<iostream>
using namespace std;
class A
{
    public:
        char b;
        virtual void fun() {};
        static int c;
        static int d;
        static int f;
};

int main()
{
    /**
     * @brief 16  字节对齐、静态变量不影响类的大小、vptr指针=8
     */
    cout<<sizeof(A)<<endl; 
    return 0;
}
```

```c++
/**
 * @file geninhe.cpp
 * @brief 1.普通单继承,继承就是基类+派生类自身的大小(注意字节对齐)
 * 注意：类的数据成员按其声明顺序加入内存，与访问权限无关，只看声明顺序。
 * 2.虚单继承，派生类继承基类vptr
 * @author 光城
 * @version v1
 * @date 2019-07-21
 */

#include<iostream>

using namespace std;

class A
{
    public:
        char a;
        int b;
};

/**
 * @brief 此时B按照顺序：
 * char a
 * int b
 * short a
 * long b
 * 根据字节对齐4+4=8+8+8=24
 */
class B:A
{
    public:
        short a;
        long b;
};
class C
{
    A a;
    char c;
};
class A1
{
    virtual void fun(){}
};
class C1:public A1
{
};

int main()
{
    cout<<sizeof(A)<<endl; // 8
    cout<<sizeof(B)<<endl; // 24
    cout<<sizeof(C)<<endl; // 12
    /**
     * @brief 对于虚单函数继承，派生类也继承了基类的vptr，所以是8字节
     */
    cout<<sizeof(C1)<<endl; // 8 
    return 0;
}
```

```C++
/**
 * @file virnhe.cpp
 * @brief 虚继承
 * @author 光城
 * @version v1
 * @date 2019-07-21
 */

#include<iostream>
using namespace std;
class A
{
    virtual void fun() {}
};
class B
{
    virtual void fun2() {}
};
class C : virtual public  A, virtual public B
{
    public:
        virtual void fun3() {}
};

int main()
{
    /**
     * @brief 8 8 16  派生类虚继承多个虚函数，会继承所有虚函数的vptr
     */
    cout<<sizeof(A)<<" "<<sizeof(B)<<" "<<sizeof(C);

    return 0;
}
```

### 14.纯虚函数和抽象类

C++中的纯虚函数(或抽象函数)是我们没有实现的虚函数！我们只需声明它! 通过声明中赋值0来声明纯虚函数！

```
// 抽象类
Class A {
public: 
    virtual void show() = 0; // 纯虚函数
    /* Other members */
}; 
```

- 纯虚函数：没有函数体的虚函数
- 抽象类：包含纯虚函数的类

抽象类只能作为基类来派生新类使用，不能创建抽象类的对象,抽象类的指针和引用->由抽象类派生出来的类的对象

##### 实现抽象类

抽象类中：在成员函数内可以调用纯虚函数，在构造函数/析构函数内部不能使用纯虚函数。

如果一个类从抽象类派生而来，它必须实现了基类中的所有纯虚函数，才能成为非抽象类。

```c++
// A为抽象类
class A {
public:
    virtual void f() = 0;  // 纯虚函数
    void g(){ this->f(); }
    A(){}  // 构造函数
};

class B : public A{
public:
    void f(){ cout<<"B:f()"<<endl;}  // 实现了抽象类的纯虚函数
```

##### 重点

1.纯虚函数使一个类变成抽象类

2.抽象类类型的指针和引用

```c++
class Derived : public Base { 
public: 
    void show() { cout << "In Derived \n"; } // 实现抽象类的纯虚函数
    Derived(){} // 构造函数
}; 

int main(void) 
{ 
    //Base b;  // error! 不能创建抽象类的对象
    //Base *b = new Base(); error!
    
    Base *bp = new Derived(); // 抽象类的指针和引用 -> 由抽象类派生出来的类的对象
    bp->show();
    return 0; 
}
```

3.如果不在派生类中覆盖纯虚函数，那么派生类也会变成抽象类

4.抽象类可以有构造函数，构造函数不能是虚函数，而析构函数可以是虚析构函数

```c++
// 抽象类
class Base  {
public:
    Base(){ cout << "Constructor: Base" << endl; }
    virtual ~Base(){ cout << "Destructor : Base" << endl; }
    
    virtual void func() = 0;
};

class Derived: public Base {
public:
    Derived(){ cout << "Constructor: Derived" << endl; }
    ~Derived(){ cout << "Destructor : Derived" << endl;}
    
    void func(){cout << "In Derived.func()." << endl;}
};
```





### 15.虚函数

多态：允许将子类类型的指针赋值给父类类型的指针，该指针会指向子类中的父类部分。多态的目的为了接口复用，即统一接口不同形态。

静多态    编译阶段确定函数的调用（函数入口地址）静态绑定   早绑定

通过函数重载和模板来实现

动多态   运行阶段确定函数的调用（函数入口地址）动态绑定 晚绑定

使用虚函数实现

动多态的实现是在编译期间生成vftable虚函数表,将含有入口地址的虚函数表放入只读数据段，运行时程序会将指令和数据加载到内存上，然后确定函数的入口地址。在运行时根据基类指针的实际指向放入对象确定所要调动的对象是基类还是派生类。

虚函数：在某基类中声明为 virtual 并在一个或多个派生类中被重新定义的成员函数，

用法格式：virtual 函数返回类型 函数名（参数表） {函数体}；

实现多态性，通过指向派生类的基类指针或引用，访问派生类中同名覆盖成员函数。

作用：虚函数的作用是在程序的运行阶段动态地选择合适的成员函数。在派生类中重新定义的函数应与虚函数具有相同的形参个数和形参类型，（参数类型的顺序也要一致），以实现统一的接口。如果在派生类中没有重新定义虚函数，则它继承基类的虚函数。

如果基类中有同名同参的函数为虚函数，那么派生类中该函数也为虚函数，并且会发生函数覆盖。
派生类对象内存布局中有vfptr虚函数指针，该指针指向vftable虚函数表，虚函数的每个类都有一张虚表。

#### 虚函数的虚指针和虚表

为了实现虚函数，C ++使用一种称为虚拟表的特殊形式的后期绑定。该虚拟表是用于解决在动态/后期绑定方式的函数调用函数的查找表。虚拟表有时会使用其他名称，例如“vtable”，“虚函数表”，“虚方法表”或“调度表”。

虚拟表实际上非常简单，虽然用文字描述有点复杂。首先，**每个使用虚函数的类（或者从使用虚函数的类派生）都有自己的虚拟表**。该表只是编译器在编译时设置的静态数组。虚拟表包含可由类的对象调用的每个虚函数的一个条目。此表中的每个条目只是一个函数指针，指向该类可访问的派生函数。

其次，编译器还会添加一个隐藏指向基类的指针，我们称之为vptr。vptr在创建类实例时自动设置，以便指向该类的虚拟表。与this指针不同，this指针实际上是编译器用来解析自引用的函数参数，vptr是一个真正的指针。

因此，它使每个类对象的分配一个指针的大小。这也意味着vptr由派生类继承，这很重要。

我们发现C++的动态多态性是通过虚函数来实现的。简单的说，通过virtual函数，指向子类的基类指针可以调用子类的函数。例如，上述通过基类指针指向派生类实例，并调用虚函数，将上述代码简化为：

```
Base *pt = new Derived(); // 基类指针指向派生类实例
cout<<"基类指针指向派生类实例并调用虚函数"<<endl;
pt->fun1();
```

其过程为：首先程序识别出fun1()是个虚函数，其次程序使用pt->vptr来获取Derived的虚拟表。第三，它查找Derived虚拟表中调用哪个版本的fun1()。这里就可以发现调用的是Derived::fun1()。因此pt->fun1()被解析为Derived::fun1()

#### 虚函数是动态绑定的，默认参数是静态绑定的。默认参数的使用需要看指针或者应用本身的类型，而不是对象的类型！

1） **静态函数可以声明为虚函数吗？**

原因主要有两方面：

**静态函数不可以声明为虚函数，同时也不能被const 和 volatile关键字修饰**

static成员函数不属于任何类对象或类实例，所以即使给此函数加上virutal也是没有任何意义

虚函数依靠vptr和vtable来处理。vptr是一个指针，在类的构造函数中创建生成，并且只能用this指针来访问它，静态成员函数没有this指针，所以无法访问vptr









### 16.浅拷贝与深拷贝

数据分为基本数据类型(String, Number, Boolean, Null, Undefined，Symbol)和对象数据类型。

1、基本数据类型的特点：直接存储在栈(stack)中的数据

2、引用数据类型的特点：**存储的是该对象在栈中引用，真实的数据存放在堆内存里**

引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体

**深拷贝和浅拷贝是只针对Object和Array这样的引用数据类型的**。

深拷贝和浅拷贝的示意图大致如下：

![image-20211014214321200](C++基础.assets/image-20211014214321200.png)

**浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。**

#### 赋值和浅拷贝的区别

当我们把一个对象赋值给一个新的变量时，**赋的其实是该对象的在栈中的地址，而不是堆中的数据**。也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此，两个对象是联动的。

浅拷贝是按位拷贝对象，**它会创建一个新对象**，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。即默认拷贝构造函数只是对对象进行浅拷贝复制(逐个成员依次拷贝)，即只复制对象空间而不复制资源。





### 17.构造函数

```c++
 1 class Complex 
 2 {         
 3  
 4 private :
 5     double m_real;
 6     double m_imag;
 7  
 8 public:
 9  
10     // 无参数构造函数
11     // 如果创建一个类你没有写任何构造函数,则系统会自动生成默认的无参构造函数，函数为空，什么都不做
12     // 只要你写了一个下面的某一种构造函数，系统就不会再自动生成这样一个默认的构造函数，如果希望有一个这样的无参构造函数，则需要自己显示地写出来
13     Complex(void)
14     {
15          m_real = 0.0;
16          m_imag = 0.0;
17     } 
18          
19     // 一般构造函数（也称重载构造函数）
20     // 一般构造函数可以有各种参数形式,一个类可以有多个一般构造函数，前提是参数的个数或者类型不同（基于c++的重载函数原理）
21     // 例如：你还可以写一个 Complex( int num)的构造函数出来
22     // 创建对象时根据传入的参数不同调用不同的构造函数
23     Complex(double real, double imag)
24     {
25          m_real = real;
26          m_imag = imag;         
27      }
28      
29     // 复制构造函数（也称为拷贝构造函数）
30     // 复制构造函数参数为类对象本身的引用，用于根据一个已存在的对象复制出一个新的该类的对象，一般在函数中会将已存在对象的数据成员的值复制一份到新创建的对象中
31     // 若没有显示的写复制构造函数，则系统会默认创建一个复制构造函数，但当类中有指针成员时，由系统默认创建该复制构造函数会存在风险，具体原因请查询有关 “浅拷贝” 、“深拷贝”的文章论述
32     Complex(const Complex & c)
33     {
34         // 将对象c中的数据成员值复制过来
35         m_real = c.m_real;
36         m_img  = c.m_img;
37     }            
38  
39     // 类型转换构造函数，根据一个指定的类型的对象创建一个本类的对象
40     // 例如：下面将根据一个double类型的对象创建了一个Complex对象
41     Complex::Complex(double r)
42     {
43         m_real = r;
44         m_imag = 0.0;
45     }
46  
47     // 等号运算符重载
48     // 注意，这个类似复制构造函数，将=右边的本类对象的值复制给等号左边的对象，它不属于构造函数，等号左右两边的对象必须已经被创建
49     // 若没有显示的写=运算符重载，则系统也会创建一个默认的=运算符重载，只做一些基本的拷贝工作
50     Complex &operator=(const Complex &rhs)
51     {
52         // 首先检测等号右边的是否就是左边的对象本，若是本对象本身,则直接返回
53         if ( this == &rhs ) 
54         {
55             return *this;
56         }
57              
58         // 复制等号右边的成员到左边的对象中
59         this->m_real = rhs.m_real;
60         this->m_imag = rhs.m_imag;
61              
62         // 把等号左边的对象再次传出
63         // 目的是为了支持连等 eg:    a=b=c 系统首先运行 b=c
64         // 然后运行 a= ( b=c的返回值,这里应该是复制c值后的b对象)    
65         return *this;
66     }
67 };
```

```c++
 1 void main()
 2 {
 3     // 调用了无参构造函数，数据成员初值被赋为0.0
 4     Complex c1，c2;
 5  
 6     // 调用一般构造函数，数据成员初值被赋为指定值
 7     Complex c3(1.0,2.5);
 8     // 也可以使用下面的形式
 9     Complex c3 = Complex(1.0,2.5);
10          
11     // 把c3的数据成员的值赋值给c1
12     // 由于c1已经事先被创建，故此处不会调用任何构造函数
13     // 只会调用 = 号运算符重载函数
14     c1 = c3;
15          
16     // 调用类型转换构造函数
17     // 系统首先调用类型转换构造函数，将5.2创建为一个本类的临时对象，然后调用等号运算符重载，将该临时对象赋值给c1
18     c2 = 5.2;
19        
20     // 调用拷贝构造函数( 有下面两种调用方式) 
21     Complex c5(c2);
22     Complex c4 = c2;  // 注意和 = 运算符重载区分,这里等号左边的对象不是事先已经创建，故需要调用拷贝构造函数，参数为c2       
23          
24 }
```

#### 复制构造函数

几个原则：

C++ primer p406 ：复制构造函数是一种特殊的构造函数，具有单个形参，该形参（常用const修饰）是对该类类型的引用。当定义一个新对象并用一个同类型的对象对它进行初始化时，将显示使用复制构造函数。当该类型的对象传递给函数或从函数返回该类型的对象时，将隐式调用复制构造函数。

 

C++支持两种初始化形式：复制初始化（int a = 5;）和直接初始化（int a(5);）对于其他类型没有什么区别，对于类类型直接初始化直接调用实参匹配的构造函数，复制初始化总是调用复制构造函数，也就是说：

A x(2);　　//直接初始化，调用构造函数
A y = x;　　//复制初始化，调用复制构造函数

 

必须定义复制构造函数的情况：

只包含类类型成员或内置类型（但不是指针类型）成员的类，无须显式地定义复制构造函数也可以复制；有的类有一个数据成员是指针，或者是有成员表示在构造函数中分配的其他资源，这两种情况下都必须定义复制构造函数。

 

什么情况使用复制构造函数：

类的对象需要拷贝时，拷贝构造函数将会被调用。以下情况都会调用拷贝构造函数：
（1）一个对象以值传递的方式传入函数体
（2）一个对象以值传递的方式从函数返回
（3）一个对象需要通过另外一个对象进行初始化。

 

深拷贝和浅拷贝：

所谓浅拷贝，指的是在对象复制时，只对对象中的数据成员进行简单的赋值，默认拷贝构造函数执行的也是浅拷贝。在“深拷贝”的情况下，对于对象中动态成员，就不能仅仅简单地赋值了，而应该重新动态分配空间

如果一个类拥有资源，当这个类的对象发生复制过程的时候，资源重新分配，这个过程就是深拷贝

上面提到，如果没有自定义复制构造函数，则系统会创建默认的复制构造函数，但系统创建的默认复制构造函数只会执行“浅拷贝”，即将被拷贝对象的数据成员的值一一赋值给新创建的对象，若该类的数据成员中有指针成员，则会使得新的对象的指针所指向的地址与被拷贝对象的指针所指向的地址相同，delete该指针时则会导致两次重复delete而出错。
