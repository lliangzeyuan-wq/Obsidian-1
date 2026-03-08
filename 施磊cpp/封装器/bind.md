---
data: 2026-02-21
---
### bind绑定普通函数
```cpp
#include<iostream>

#include<functional>

#include<map>

using namespace std;

void print(const string& s1, const string& s2)

{

    cout << s1 << " " << s2 << endl;

}

  

int main()

{

    string s1{ "hello" };

    string s2{ "world" };

    string s3{ "HI" };

  

    // //没有占位符的情况

    // function<void()>f = bind(print, s1, s2);

    // f()

  

    // //一个占位符的情况

    // function<void(const string&)>f = bind(print, s1, placeholders::_1);

    // f(s3);

  
  

    //一个占位符的另一种情况

    function<void(const string&)>f = bind(print, placeholders::_1, s1);

    f(s3);

  

    // //两个占位符的情况

    // function<void(const string&, const string&)>f = bind(print, placeholders::_1, placeholders::_2);

    // f(s1, s2);

  
  

    return 0;

}
```


### bind绑定类成员函数
```cpp
#include <iostream>

#include<functional>

using namespace std;

class MyMath

{

public:

    MyMath()

    {

        cout << "默认构造函数" << endl;

    }

    MyMath(const MyMath& math)

    {

        cout << "默认拷贝构造函数" << endl;

    }

    int add(int x, int y) { return x + y; };

};

// typedef int(MyMath::* FUNC)(int, int);

using FUNC = int(MyMath::*)(int, int);

int main() {

    FUNC f = &MyMath::add;

    MyMath m;//实例化一个对象

    int sum = (m.*f)(1, 2);

    cout << sum << endl;

  

    int a = 20;

    function<int(int)>f1 = bind(&MyMath::add, m, placeholders::_1, a);

    cout << f1(4) << endl;

    return 0;

}
```