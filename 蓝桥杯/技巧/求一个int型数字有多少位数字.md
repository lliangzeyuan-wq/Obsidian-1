---
data: 2026-03-01
---
好！我给你写 **最简单、最常用、比赛最稳** 的两种写法，你直接背就行！

# 方法 1：数学法（最快，推荐）

cpp

运行

```
#include <iostream>
#include <cmath>
using namespace std;

int main() {
    int n;
    cin >> n;
    
    int len = to_string(n).size();  // 直接转字符串求长度
    
    cout << len << endl;
    return 0;
}
```

# 方法 2：循环除法（最经典，蓝桥杯常考）

cpp

运行

```
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    if (n == 0) {  // 0 是 1 位
        cout << 1 << endl;
        return 0;
    }

    int cnt = 0;
    while (n > 0) {
        n /= 10;   // 去掉最后一位
        cnt++;     // 位数+1
    }

    cout << cnt << endl;
    return 0;
}
```

