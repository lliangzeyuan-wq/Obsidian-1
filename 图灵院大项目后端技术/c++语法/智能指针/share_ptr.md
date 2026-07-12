## 一、share_ptr

　　[share_ptr](https://zhida.zhihu.com/search?content_id=169332346&content_type=Article&match_order=2&q=share_ptr&zhida_source=entity)是C++11新添加的[智能指针](https://zhida.zhihu.com/search?content_id=169332346&content_type=Article&match_order=1&q=%E6%99%BA%E8%83%BD%E6%8C%87%E9%92%88&zhida_source=entity)，它限定的资源可以被多个指针共享。

**只有指向[动态分配](https://zhida.zhihu.com/search?content_id=169332346&content_type=Article&match_order=1&q=%E5%8A%A8%E6%80%81%E5%88%86%E9%85%8D&zhida_source=entity)的对象的指针才能交给 shared_ptr 对象托管。**将指向普通局部变量、[全局变量](https://zhida.zhihu.com/search?content_id=169332346&content_type=Article&match_order=1&q=%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F&zhida_source=entity)的指针交给 shared_ptr 托管，编译时不会有问题，但程序运行时会出错，因为不能析构一个并没有指向动态分配的内存空间的指针。

```cpp
#include <iostream>
#include <memory>
#include <string>
using namespace std;

void fun() {
    shared_ptr<string> pa(new string("CHN"));
    shared_ptr<string> pb(new string("USA"));
    cout << "*pa " << *pa << endl;//CHN
    cout << "pa.use_count " << pa.use_count() << endl;//1
    cout << "*pb " << *pb << endl;//USA
    cout << "pb.use_count " << pb.use_count() << endl;//1

    pa = pb;
    cout << *pa << endl;//USA
    cout << "pa.use_count " << pa.use_count() << endl;//2：pa和pb指向同一个资源USA了，该资源的计数为2，所以pb、pb都输出2
    cout << "pb.use_count " << pb.use_count() << endl;//2

    pa.reset();
    pb.reset();
    cout << "pa.use_count " << pa.use_count() << endl;//0
    cout << "pb.use_count " << pb.use_count() << endl;//0
}

int main()
{
    fun();
    system("pause");
    return 0;
}
```

输出结果：

```text
*pa CHN
pa.use_count 1
*pb USA
pb.use_count 1
USA
pa.use_count 2
pb.use_count 2
pa.use_count 0
pb.use_count 0
```

注意，不能用下面的方式使得两个 shared_ptr 对象托管同一个指针：
```
A* p = new A(10);

shared_ptr <A> sp1(p), sp2(p);

```

sp1 和 sp2 并不会共享同一个对 p 的托管计数，而是各自将对 p 的托管计数都记为 1（sp2 无法知道 p 已经被 sp1 托管过）。这样，当 sp1 消亡时要析构 p，sp2 消亡时要再次析构 p，这会导致程序崩溃。




#    二 。  超详细讲解`shared_ptr`（共享智能指针）

##   一、基础概念梳理

`std::shared_ptr`出自头文件`<memory>`，是 C++11 推出的智能指针，核心特性是**引用计数机制**：

1. 一块动态`new`出来的堆内存，可以被好几个`shared_ptr`共同托管；
2. 内部维护计数器`use_count`，用来记录当前一共有多少个智能指针指向这块内存；
3. 每当新增一个指针共享资源，计数 + 1；指针放弃资源 / 被销毁，计数 - 1；
4. **只有引用计数下降到 0 的时候**，才会自动`delete`释放堆内存，杜绝手动忘记 free 造成的内存泄漏。

⚠️硬性前提：只能托管`new`在堆上开辟的对象，不能传入普通局部栈变量、全局变量的地址，不然智能指针试图去释放栈内存，运行就会出错。

## 二、逐行拆解你贴的示例代码

cpp

运行

```cpp
shared_ptr<string> pa(new string("CHN"));
shared_ptr<string> pb(new string("USA"));
```

这里做了两次独立的堆内存申请：

- `pa`看管存放`"CHN"`的字符串堆空间，此时这块内存引用计数 = 1；
- `pb`看管存放`"USA"`的字符串堆空间，这块内存引用计数 = 1；
    
    所以接下来打印：

cpp

运行

```cpp
cout << "*pa " << *pa << endl;//CHN
cout << "pa.use_count " << pa.use_count() << endl;//1
cout << "*pb " << *pb << endl;//USA
cout << "pb.use_count " << pb.use_count() << endl;//1
```

输出结果和注释完全吻合，两个智能指针互相独立。

### 关键一步：`pa = pb;`赋值操作

拆解赋值发生的两件事：

1. `pa`原本独自持有`"CHN"`那块内存，现在不再指向它，这块内存引用计数从`1`降到`0`，系统立刻自动释放存储`CHN`的堆内存；
2. `pa`转而指向`pb`管控的`USA`内存，这块内存共享者新增 pa，引用计数从`1`上涨到`2`；

后续打印：

cpp

运行

```cpp
cout << *pa << endl;//USA
cout << "pa.use_count " << pa.use_count() << endl;//2
cout << "pb.use_count " << pb.use_count() << endl;//2
```

pa、pb 指向同一份`USA`资源，二者调用`use_count`拿到的都是共享计数`2`。

### `reset()`方法的效果

`shared_ptr::reset()`功能：让当前智能指针放弃对原有资源的托管，引用计数递减

cpp

运行

```cpp
pa.reset();
```

pa 解绑 USA 资源，USA 的计数由 2 变为 1，只剩下 pb 持有；

cpp

运行

```cpp
pb.reset();
```

pb 接着解绑，USA 的计数从 1 降到 0，USA 对应的堆内存也被释放；

所以两次打印`use_count`结果都变为 0。

## 三、末尾重点易错坑详细解释（踩坑高发点）

错误写法：

cpp

运行

```cpp
A* p = new A(10);
shared_ptr<A> sp1(p), sp2(p);
```

### 出错原理

`sp1`、`sp2`是两个互相独立的智能指针：

1. `sp1`接手裸指针`p`，单独创建一份引用计数器，计数记成`1`；
2. `sp2`也接手裸指针`p`，又单独新建了一份独立的计数器，计数也记成`1`；
    
    二者彼此感知不到对方的存在。
    
    生命周期结束的时候：

- sp1 析构：自身计数归零，执行`delete p`释放对象；
- sp2 后续析构：它自己的计数器也归零，**再次执行 delete p**，对已经释放的内存二次释放，造成程序崩溃。

### 正确的实现方式

方案 1：先初始化其中一个 shared_ptr，再通过拷贝让二者共享计数器

cpp

运行

```cpp
shared_ptr<A> sp1(new A(10));
shared_ptr<A> sp2 = sp1; //拷贝构造，共用一套引用计数，计数变为2
```

方案 2：推荐`std::make_shared`（写法更安全高效）

cpp

运行

```cpp
auto sp1 = make_shared<A>(10);
auto sp2 = sp1;
```

## 四、关键 API 小结

1. `.use_count()`：获取当前这块共享内存现存的引用计数；
2. `.reset()`：智能指针解除对托管资源的绑定，计数减一；不传参数就置空；
3. `*ptr`：解引用，访问智能指针包裹的原始对象；
4. `ptr.get()`：取出底层原生裸指针，日常开发谨慎使用，很容易引入上面那种二次释放的坑。

## 五、生命周期小补充

示例的代码写在`fun()`函数内部，当`fun`执行结束，pa、pb 作为局部变量离开作用域，本身也会触发析构，同样会让引用计数减少；本案例提前手动调用 reset 清空，所以在函数返回前计数就已经归零了。

