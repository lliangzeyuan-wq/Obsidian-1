---
data: 2026-02-16
---
 - 使用的时候必须加上`#include <memory>
 - ```
   #include <memory>
   using namespace std;
   
   shared_ptr<int> p;
   p=make_shared<int>(100);
   ```
- 在上面的程序中，可以看到shared_ptr是一个模板，你要指定参数。并用make_shared来初始化
- 另一种写法
```
  #include <memory>
   using namespace std;
   
   shared_ptr<int> p{make_shared<int>(100)};
```