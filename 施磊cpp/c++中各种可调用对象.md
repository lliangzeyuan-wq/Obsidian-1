---
data: 2026-03-07
---
### 普通函数
```cpp
#include <iostream>
using namespace std;

int func1(int x, int y) { return x + y; }

typedef int (*FUNC1)(int, int);
using FUNC2 = int (*)(int, int);

int main() {
    FUNC1 f = func1;
    FUNC2 f1 = func1;

    cout << f(1, 2) << endl;
    cout << f1(2, 2) << endl;

    return 0;
}
```



### 类成员函数
```cpp
#include <iostream>

using namespace std;

class MyMath

{

public:

    int add(int x, int y) { return x + y; }

};

  

typedef int(MyMath::* FUNC1)(int, int);

// //c++11

// using FUNC1 = int(MyMath::*)(int, int);

  

int main() {

    FUNC1 f = &MyMath::add;

    MyMath m;

    int sum = (m.*f)(1, 2);

    cout << sum << endl;

    return 0;

}
```



### 静态类成员函数
- 除了//111处和普通函数不一样外，其他的地方和普通函数一样
```cpp
#include <iostream>

using namespace std;

class MyMath

{

public:

    static int add(int x, int y) { return x + y; }

};

typedef int(*FUNC2)(int, int);

int main() {

    FUNC2 f = &MyMath::add;//111

    cout << f(2, 3) << endl;

    return 0;

}
```