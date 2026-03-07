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



### 成员函数
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