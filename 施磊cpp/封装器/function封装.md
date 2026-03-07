---
data: 2026-02-05
---
```cpp
#include<iostream>

#include<functional>

#include<map>

using namespace std;

int add(int x, int y) { return x + y; }

int sub(int x, int y) { return x - y; }

void func(function<int(int, int)>f, int x, int y)

{

    cout << f(x, y) << endl;

}

int main()

{

    // function<int(int, int)>f = add;

    // cout << f(1, 2) << endl;

    // f = sub;

    // cout << f(4, 2) << endl;

  

    func(add, 2, 3);

    func(sub, 2, 3);

    map<string, function<int(int, int)>>map

    {

        {"+",add}

        ,{"-",sub}

        ,{"*",[](int x,int y)->int {return x * y;}}

    };

  

    map["+"](2, 3);

    map["-"](2, 3);

    map["*"](2, 3);

}
```