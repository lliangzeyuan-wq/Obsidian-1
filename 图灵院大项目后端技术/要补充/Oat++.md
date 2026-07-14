
- step by step guide
- overview :  api controller  ,   dto  ,  




```
项目根目录
├─ CMakeLists.txt              // 项目整体编译配置文件
├─ src/                         // 业务源码根目录
│  ├─ dto/                      // 存放所有数据传输对象(DTO)定义
│  │  └─ DTOs.hpp               // 全部接口DTO结构体在此声明
│  ├─ controller/               // API控制器目录，所有接口路由都写在这里
│  │  └─ MyController.hpp       // 示例控制器，接口业务逻辑存放处
│  ├─ AppComponent.hpp          // 全局组件配置（对象映射器、路由、数据库连接等单例注入）
│  └─ App.cpp                   // 程序入口，main() 函数所在文件
└─ test/                        // 单元测试目录
   ├─ app/
   │  ├─ MyApiTestClient.hpp    // 测试专用API请求客户端封装
   │  └─ TestComponent.hpp      // 测试环境专属组件配置
   ├─ MyControllerTest.cpp      // 控制器单元测试实现代码
   ├─ MyControllerTest.hpp      // 控制器测试头文件
   └─ Tests.cpp                 // 测试程序入口，测试main()函数
```