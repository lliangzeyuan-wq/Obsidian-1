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