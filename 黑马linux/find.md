`find . -name "http_for_bench" -type f -executable`

|部分|含义|大白话解释|
|:--|:--|:--|
|`find`|Linux 里的「文件查找命令」|帮你在电脑里找文件|
|`.`|查找的起始路径|从「当前目录」开始找（你现在在 `build/Debug` 目录，就从这里往下找）|
|`-name "http_for_bench"`|按文件名搜索|只找名字叫 `http_for_bench` 的文件|
|`-type f`|指定文件类型|只找「普通文件」，排除文件夹、符号链接|
|`-executable`|筛选可执行文件|只找带「可执行权限」的文件（就是你编译出来的服务器程序）|


