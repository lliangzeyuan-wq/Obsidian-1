## bg 命令（background）

### 作用

把**暂停态**的作业，变成**后台运行态**。

### 语法

bash

运行

```
bg          # 默认：最近一个被暂停的任务
bg %1       # 作业号 1
bg %vim     # 名字含 vim 的那个（唯一才行）
```

### 典型流程（最常用）

1. 跑一个前台程序：
    
    bash
    
    运行
    
    ```
    ./long_run.sh
    ```
    
2. 不想等了，按 **Ctrl+Z** → 任务**暂停**，回到命令行：
    
    plaintext
    
    ```
    [1]+  Stopped                 ./long_run.sh
    ```
    
3. 让它在后台继续跑：
    
    bash
    
    运行
    
    ```
    bg
    ```
    
    变成：
    
    plaintext
    
    ```
    [1]+  ./long_run.sh &
    ```
    
    现在终端空闲，你可以干别的。